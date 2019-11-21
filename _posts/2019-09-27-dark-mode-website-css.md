---
layout: page
title: Dark mode in a website with CSS
image: /images/dark_mode_small.gif
description: This page has a dark mode. Try it! Supporting prefers-color-scheme was easy, though there were a couple surprising details to address.
---
This site has a dark mode and a light one. Which mode you see depends on the setting of your device. Try it!

<img alt="toggling dark mode in iOS" src="/images/dark_mode.gif" class="large">

The instant change in appearance is accomplished by this appendix to the style sheet:

```css
@media (prefers-color-scheme: dark) {
  body {
    background-color: #444;
    color: #e4e4e4;
  }
  a {
    color: #e39777;
  }
  img {
    filter: grayscale(30%);
  }
}
```

This works in Firefox, Safari, and Chrome among other browsers.

## Light on dark

The most important block is the one that overrides the colors of text and the background:

```css
body {
  background-color: #444;
  color: #e4e4e4;
}
```

It's recommended to [avoid pure white](https://web.dev/prefers-color-scheme#avoid-pure-white) for text, and I chose likewise to avoid pure black for the background.

## Desaturate colors

The accent color I use for hyperlinks looked harsh on the dark background, so I overrode it with a less saturated one:

```css
a {
  color: #e39777;
}
```

Photographs also looked harsh. It turns out that many people prefer images, too, to be [desaturated in dark mode](https://medium.com/dev-channel/re-colorization-for-dark-mode-19e2e17b584b):

```css
img {
  filter: grayscale(30%);
}
```

You may want to use a more specific selector here in order to [treat vector images differently](https://web.dev/prefers-color-scheme#invert-vector-graphics-and-icons).

## Read more

All the above is distilled from [Hello darkness, my old friend](https://web.dev/prefers-color-scheme) by [Thomas Steiner](https://twitter.com/tomayac), which also contains a fascinating history of display color schemes:

> With the advent of more sophisticated WYSIWYG desktop publishing, the idea of making the virtual document resemble a physical sheet of paper became popular…
>
> The first ever browser, WorldWideWeb, displayed webpages this way. Fun fact: the second ever browser, Line Mode Browser—a terminal-based browser—was green on dark.

**Update:** This post is discussed on [Hacker News](https://news.ycombinator.com/item?id=21195913) and [Reddit](https://www.reddit.com/r/webdev/comments/df4hxq/dark_mode_in_css_with_preferscolorscheme/).

If you'd like to hear about other projects of mine, [subscribe to my newsletter](https://tinyletter.com/brow)!
