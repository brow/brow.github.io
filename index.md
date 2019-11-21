---
layout: default
description: I design and build software. I led iOS development at Asana and cofounded Pod. I'm available for consulting sometimes.
---

<img
  class="headshot"
  src="https://s.gravatar.com/avatar/857ce6f50d086b1232ccfcb9030ae4e2?s=360"
  alt="photo of Tom">

# Tom Brow

I design and build software for human use. For the last 11 years, I've focused on native iOS apps.

I'm sometimes available for [consulting](/consulting).

Previously I cofounded [Pod](/pod) and led iOS development at [Asana](https://asana.com).

Longer ago I worked at [Google](https://www.google.com/) and [Lytro](https://en.wikipedia.org/wiki/Lytro), consulted for startups, and studied computer science at [Stanford](https://cs.stanford.edu/).

My full CV is on [my LinkedIn](https://www.linkedin.com/in/tombrow/).

## Contact

* Reach me anytime: [hello@tombrow.com](mailto:hello@tombrow.com)
* Instagram: [@tom](https://instagram.com/tom)
* Sign up for [my newsletter](https://tinyletter.com/brow)!

## Projects

* [Timelord](/timelord), a voice-controlled iOS kitchen timer
* **jklp**, a minimal ergonomic keyboard ([1](https://www.reddit.com/r/MechanicalKeyboards/comments/dwc3go/why_have_you_created_me/),
    [2](https://www.reddit.com/r/ErgoMechKeyboards/comments/dq67gm/3_generations_of_a_prototype/),
    [3](https://www.reddit.com/r/ErgoMechKeyboards/comments/dqq4ib/winters_coming/))

## Writing

<ul>

{% for post in site.posts %}

    <li>
        <a href="{{post.url}}">{{post.title}}</a>
        ({{ post.date | date: '%B %Y' }})
    </li>

{% endfor %}

</ul>
