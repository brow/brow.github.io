---
layout: default
description: I design and build software, focusing on native iOS apps.
---

<div class="header">
  <h1>Tom Brow</h1>
  <p class="title">Software Engineer</p>
</div>

<p class="intro">I design and build software for human use. For the last 15 years, I've focused on native iOS apps.</p>

<div class="section">

Currently building foundational frameworks and tools at [Square](https://squareup.com).

Previously led iOS at [Asana](https://asana.com) and cofounded [Pod](/pod). Before that, worked at [Google](https://www.google.com/) and [Lytro](https://en.wikipedia.org/wiki/Lytro). Studied computer science at [Stanford](https://cs.stanford.edu/).

</div>

## Now

<div class="section">

Building iOS infrastructure at Square. Creating [Timelord](/timelord), a voice-controlled kitchen timer. Designing ergonomic keyboards like [Balbuzard](https://github.com/brow/balbuzard) and [jklp](https://github.com/brow/jklp).

</div>

## Writing

<ul>
{% for post in site.posts %}
<li class="item">
  <a class="item-title" href="{{post.url}}">{{post.title}}</a>
  <span class="item-meta">{{ post.date | date: '%B %Y' }}</span>
</li>
{% endfor %}
</ul>

## Links

<div class="links">
  <a href="mailto:hello@tombrow.com">Email</a>
  <a href="https://www.linkedin.com/in/tombrow/">LinkedIn</a>
  <a href="https://instagram.com/tom">Instagram</a>
  <a href="https://github.com/brow">GitHub</a>
</div>
