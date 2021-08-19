---
layout: post
title: Keyboard Layouts
---

We now support 56 languages in xrdesktop!

![Arabic keyboard layout](/assets/2021/08-19-arabic-keyboard.png)

The Unicode Common Locale Data Repository specifies many keyboard layouts, so we
convert this data to our internal JSON format.

Currently, we only support languages with alphabetic writing systems, such as
English, Arabic or Russian. Languages with logographic writing systems, such as
Chinese, Japanese or Korean, are not supported yet. They require a more
complicated interaction model, because a character is built by multiple
keystrokes before being committed.
