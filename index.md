---
layout: default
description: I design and build software, focusing on native iOS apps.
---

<img
  class="headshot"
  src="https://s.gravatar.com/avatar/857ce6f50d086b1232ccfcb9030ae4e2?s=360"
  alt="photo of Tom">

# Tom Brow

I design and build software for human use. For the last 15 years, I've focused on native iOS apps.

Today I build foundational frameworks and tools for [Square](https://squareup.com)'s apps.

Previously I led iOS development at [Asana](https://asana.com) and cofounded [Pod](/pod).

Longer ago I worked at [Google](https://www.google.com/) and [Lytro](https://en.wikipedia.org/wiki/Lytro), consulted for startups, and studied computer science at [Stanford](https://cs.stanford.edu/).

My full CV is on [my LinkedIn](https://www.linkedin.com/in/tombrow/).

## Contact

* Reach me anytime: [hello@tombrow.com](mailto:hello@tombrow.com)
* Instagram: [@tom](https://instagram.com/tom)
* Sign up for [my newsletter](https://tinyletter.com/brow)!

## Projects

* [Timelord](/timelord), a voice-controlled iOS kitchen timer
* [Balbuzard](https://github.com/brow/balbuzard) and [jklp](https://github.com/brow/jklp), compact ergonomic keyboards

## Writing

<ul>

{% for post in site.posts %}

<li>
    <a href="{{post.url}}">{{post.title}}</a>
    ({{ post.date | date: '%B %Y' }})
</li>

{% endfor %}

</ul>


