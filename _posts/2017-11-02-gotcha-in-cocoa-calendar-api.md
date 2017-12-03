---
layout: post
title:  "The fault in our stores: a gotcha in Cocoa's calendar and contacts APIs"
---

EKEventStore lets us query up to four years of the user's calendar for events
in one method call. That can yield quite a few `EKEvent`s, which entails reading
plenty of data from disk.

Anticipating this, EventKit provides `enumerateEvents` to let us iterate
through the events that match our query without bringing them all into memory
at once. Like most `enumerate` APIs in Cocoa, the method takes a callback
parameter that will be called for each element.

However, also like other `enumerate` APIs, the presence of a callback does not
imply that `enumerateEvents` is an asynchronous method–that it will return
immediately, unblocking the thread it's running on and performing its work on
another thread or in future iterations of the event loop. In fact,
`enumerateEvents` does not return until it has iterated through every event
matching the supplied predicate–and performed all of the I/O that makes the
iteration possible. [1]

That might be okay to do on the main thread if we're fetching a known, small
number of events. But if the number is unbounded, then `enumerateEvents` is as
hazardous to the smooth running of our user interface as `events` is to our
memory reserves.

Anticipating that hazard as well, the EventKit docs suggest calling `events`
and `enumerateEvents` on another thread using `dispatch_async`. That doesn't
stop those methods from from blocking the thread they're called on, but it does
mean we're calling them on a thread that's okay to block.

This raises an interesting question, at least for developers who've been burned
by Cocoa in the past: is `EKEventStore` "thread-safe"? In other words, are we
allowed call methods on the same instance from different threads? And can we do
that without serializing those calls–guaranteeing that each call returns before
the subsequent call begins?[1]

The EventKit docs don't answer that question. But I found in experimentation
that `EKEventStore` does serialize calls to `events`, `enumerateEvents`, and
other calls that entail the laborious task of disk I/O. I did not see crashes.

So, can we just do all of our event fetching on background threads and, after
the hard and slow work is done, deliver the `EKEvent`s back to the main thread
for display? (Let's assume that we've got plenty of memory to hold them all.)

Maybe not. Read on.

## The gotcha

I described how both ways of getting `EKEvent`s involve costly synchronous I/O.
But surely once we have `EKEvent`s in hand (and in memory), then anything we
want to do with them is fast?

Actually, it turns out that accessing certain properties of an `EKEvent`
triggers additional synchronous I/O. Merely reading the value of
`EKEvent.eventIdentifier` requires an
[XPC](https://www.objc.io/issues/14-mac/xpc/) request, which is evident in a
stack trace.

If you've used Core Data, this behavior will be familiar to you. Properties of
an `NSManagedObject` are not often fetched from the persistent store until they
are accessed, at which point they are fetched-synchronously, because the getter
must return a value. The behavior is called faulting.

In both `EKEvent` and `NSManagedObject`, this synchronous fetching of
properties is usually not a problem. Fetching a few properties for a single
object is fast enough to blend in with other work routinely performed on
the main thread.[3]

But how does this automatic fetching on the main thread interact with the
deliberate fetching we're doing on background threads? Consider this scenario:

1. We call `enumerateEvents` to count the number of calendar events the user
   had in the last year. We know this will take a while, so do it on background
   thread. The background thread inherently has lower priority than the main thread,
   and perhaps we downgrade the priority even further by choosing a low
   [Quality of Service class].
2. While that's happening, the user is browsing events in our app, requiring us
   to access some properties of existing `EKEvent`s, triggering synchronous
   I/O.
3. Because the I/O from (2) involves the same `EKEventStore` that the
   `EKEvent` was originally fetched from, that `EKEventStore` blocks I/0 (2)
   while it waits for I/O (1) to finish. This is the automatic serialization I
   mentioned above.

Our main thread is now blocked on an operation that was supposed to be
relegated to a background thread, and that may take seconds or minutes to
complete, especially given the low priority of the latter thread. It's a
[priority inversion].

## The solution

Why doesn't Core Data have this problem? After all, it's common to access
managed objects on the main thread while a slow write operation churns away
on a background thread, those accesses must be serialized as well.

One reason is that no two threads are allowed to interact substantially with
the same `NSManagedObjectContext` (the rough analog of an `EKEventStore`). A
Core Data application instantiates separate `NSManagedObjectContext`s for
foreground and background work, and sometimes multiple for the latter. This
separation of contexts is called "thread confinement".

We can avoid the EventKit priority inversion described above by allocating one
`EKEventStore` for each thread, or just for each QoS class. However, we would
be responsible not only for always accessing the right `EKEventStore` for the
thread we're on, but also for not accessing any `EKEvent`s that originate from
other `EKEventStore`s.

Given the difficulty of segregating `EKEvent`s in that way, and the high price
of mistakes, it's probably safest to discard `EKEvent`s at the earliest
opportunity, after copying the data we're interested in into some other data
structure that can be accessed without side effects. Ideally, an `EKEvent`
never escapes the scope of the `enumerateEvents` block where we are given
it.[4]

## Conclusion

The solution of discarding `EKEvent`s immediately is at odds with what
EventKit's designers seem to intend. `EKEvent` includes a `refresh()` method
that updates the properties with fresh values from the store, suggesting that
`EKEvent` is meant to be retained and consulted as the underlying data
changes.

Though I've only explored calendar events here, EventKit's API for Reminders is
nearly identical, as is the Contacts framework's API. These frameworks seem
intended to resemble one another, and also to resemble a vision of a Core Data
without thread confinement, prefetching, uniquing, or client control over
faulting.

[1] Why is the callback always `@escaping` then? I don't know.

[2] If the answer to one of these questions is "no", a Cocoa framework usually
tells us so by crashing with surprising, diverse, and opaque stack traces.

[3] A classic accident, however, is to unwittingly cause properties to be
fetched for many `NSManagedObject`s at one time, back-to-back, by iterating
over them.

[4] A benefit of that approach is that only one `EKEvent` need be in memory at once,
and all of the I/O we incur by accessing it occurs on the background thread!

[Quality of Service class]: https://developer.apple.com/library/content/documentation/Performance/Conceptual/EnergyGuide-iOS/PrioritizeWorkWithQoS.html
[priority inversion]: https://en.wikipedia.org/wiki/Priority_inversion
