---
layout: post
title: KDE in VR with Keyboard Support
tags: GSoC2021
---

The xrdesktop project comes with built-in support for the two most popular
desktop environments: GNOME and KDE. I've managed to get my virtual keyboard
working for KDE!

![KDE in VR with Keyboard](/assets/2021/08-13-kde-in-vr-with-keyboard.png)

KDE's xrdesktop support comes in the form of a KWin 'desktop effects' plugin.
It is in this plugin that windows are mirrored in the VR environment. This is
also the place where I needed to add support for input through the virtual
keyboard.

Luckily, there was existing code that worked with SteamVR's keyboard. Adding
xrdesktop's own keyboard support was a matter of connecting to its
`key-pressed-event` event with a callback, which synthesizes a key press using
`libinputsynth`.

