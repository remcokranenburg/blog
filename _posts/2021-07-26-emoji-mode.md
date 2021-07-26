---
layout: post
title: Emoji Mode
---

We now have multiple keyboard modes in xrdesktop: lowercase, uppercase, numbers,
special characters and emojis!

![Keyboard lowercase mode](/assets/2021/07-26-keyboard-mode1-main.png)

The modes are specified in JSON format and loaded as a GResource. The following
shows an abbreviated example of how each key is specified. Each key is part of
a mode and has a *label*, *x* and *y* coordinates and a *width*. An optional
*target* makes a key switch to a different mode.

```json
{
  "name": "Small",
  "modes": [
    {
      "name": "Main",
      "keys": [
        { "label": "q", "x": 0.0, "y": 0.0, "w": 1.0 },
        { "label": "w", "x": 1.0, "y": 0.0, "w": 1.0 },
        { "label": "e", "x": 2.0, "y": 0.0, "w": 1.0 },
        { "label": "r", "x": 3.0, "y": 0.0, "w": 1.0 },
        { "label": "t", "x": 4.0, "y": 0.0, "w": 1.0 },
        { "label": "y", "x": 5.0, "y": 0.0, "w": 1.0 },
        ...
        { "label": "⇧", "x": 0.0, "y": 2.0, "w": 1.5, "target": "Capitals" },
        { "label": "z", "x": 1.5, "y": 2.0, "w": 1.0 },
        { "label": "x", "x": 2.5, "y": 2.0, "w": 1.0 },
        { "label": "c", "x": 3.5, "y": 2.0, "w": 1.0 },
        { "label": "v", "x": 4.5, "y": 2.0, "w": 1.0 },
        { "label": "b", "x": 5.5, "y": 2.0, "w": 1.0 },
        { "label": "n", "x": 6.5, "y": 2.0, "w": 1.0 },
        ...
        { "label": "⌫", "x": 8.5, "y": 2.0, "w": 1.5 },
        { "label": "?123", "x": 0.0, "y": 3.0, "w": 1.5, "target": "Numbers" },
        { "label": ",", "x": 1.5, "y": 3.0, "w": 1.0 },
        { "label": "☺", "x": 2.5, "y": 3.0, "w": 1.0, "target": "Emoji" },
        { "label": " ", "x": 3.5, "y": 3.0, "w": 4.0 },
        { "label": ".", "x": 7.5, "y": 3.0, "w": 1.0 },
        { "label": "↵", "x": 8.5, "y": 3.0, "w": 1.5 }
      ]
    },
    {
      "name": "Capitals",
      "keys": [
        { "label": "Q", "x": 0.0, "y": 0.0, "w": 1.0 },
        { "label": "W", "x": 1.0, "y": 0.0, "w": 1.0 },
        { "label": "E", "x": 2.0, "y": 0.0, "w": 1.0 },
        { "label": "R", "x": 3.0, "y": 0.0, "w": 1.0 },
        { "label": "T", "x": 4.0, "y": 0.0, "w": 1.0 },
        { "label": "Y", "x": 5.0, "y": 0.0, "w": 1.0 },
        ...
        { "label": "⇧", "x": 0.0, "y": 2.0, "w": 1.5, "target": "Main" },
        { "label": "Z", "x": 1.5, "y": 2.0, "w": 1.0 },
        { "label": "X", "x": 2.5, "y": 2.0, "w": 1.0 },
        { "label": "C", "x": 3.5, "y": 2.0, "w": 1.0 },
        { "label": "V", "x": 4.5, "y": 2.0, "w": 1.0 },
        { "label": "B", "x": 5.5, "y": 2.0, "w": 1.0 },
        { "label": "N", "x": 6.5, "y": 2.0, "w": 1.0 },
        ...
        { "label": "⌫", "x": 8.5, "y": 2.0, "w": 1.5 },
        { "label": "?123", "x": 0.0, "y": 3.0, "w": 1.5, "target": "Numbers" },
        { "label": ",", "x": 1.5, "y": 3.0, "w": 1.0 },
        { "label": "☺", "x": 2.5, "y": 3.0, "w": 1.0, "target": "Emoji" },
        { "label": " ", "x": 3.5, "y": 3.0, "w": 4.0 },
        { "label": ".", "x": 7.5, "y": 3.0, "w": 1.0 },
        { "label": "↵", "x": 8.5, "y": 3.0, "w": 1.5 }
      ]
    },
    ...
  ]
}
```

Numeric mode:

![Keyboard uppercase mode](/assets/2021/07-26-keyboard-mode2-numbers.png)

Special character mode is accessible from the numeric mode.

![Keyboard special mode](/assets/2021/07-26-keyboard-mode3-special.png)

And we have support for emojis!

![Keyboard emoji mode](/assets/2021/07-26-keyboard-mode4-emoji.png)

The emojis are specified literally in the JSON file:

```json
{
  "name": "Emoji",
  "keys": [
    { "label": "😀", "x": 0.0, "y": 0.0, "w": 1.0 },
    { "label": "😃", "x": 1.0, "y": 0.0, "w": 1.0 },
    { "label": "😄", "x": 2.0, "y": 0.0, "w": 1.0 },
    { "label": "😁", "x": 3.0, "y": 0.0, "w": 1.0 },
    { "label": "😆", "x": 4.0, "y": 0.0, "w": 1.0 },
    { "label": "😅", "x": 5.0, "y": 0.0, "w": 1.0 },
    ...
  ]
}
```

To get it all working, I had to add Pango as a dependency, because it supports
font fallbacks. Most fonts do not support all possible characters, so when you
want to draw text in many languages, you will need to use several backup fonts.

I added a small pango example to gulkan, showing Arabic, Chinese and Hindi text
plus emojis:

![Gulkan pango example](/assets/2021/07-26-gulkan-pango.png)

That's it for now! A future post will showcase Kwin support for xrdesktop with
a virtual keyboard.
