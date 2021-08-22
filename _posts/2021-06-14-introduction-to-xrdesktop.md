---
layout: post
title: Introduction to xrdesktop
tags: GSoC2021
---
Hello again! It has been a week and we still haven't been formally introduced!
I'm Remco Kranenburg, and I'm implementing a *virtual keyboard* for
[xrdesktop](https://gitlab.freedesktop.org/xrdesktop/xrdesktop) this summer, for
<abbr title="Google Summer of Code">GSoC</abbr> 2021. In this post I'll talk a
bit about the project and show some initial results. But first, a screenshot!

![Keyboard drawing](/assets/2021/06-14-keyboard-drawing.png)

## Predictive Text Input for Virtual Reality

*xrdesktop* is a project that brings your Linux desktop into a VR space, with
support for KDE and GNOME. It is written in the form of a GLib library. You
could easily write your own VR window manager, in theory in any language (though
language bindings are currently not finished).

One area in which xrdesktop is lacking, is a virtual keyboard. SteamVR provides
one, but it is not available when you are in a virtual environment without
SteamVR, such as when you are using a standalone headset, or when you are using
the fully open source OpenXR implementation
[Monado](https://monado.freedesktop.org/). Since xrdesktop is intended to work
in all such environments, we need our own keyboard implementation. 

During GSoC 2021, that is exactly what I will implement. The project is divided
into three broad milestones:

  1. a basic QWERTY keyboard implementation
  1. a [Swype](https://en.wikipedia.org/wiki/Swype)-like predictive mode
  1. a [Dasher](https://en.wikipedia.org/wiki/Dasher_(software))-like predictive
     mode

The first milestone is the important one, since it enables text input in
xrdesktop at all. Any of the other modes are nice-to-have, but those are the
modes I'm personally most excited about!

## First Week

It was quite a rush to be able to make that first screenshot! In fact, I was so
excited that I forgot to make a 'C' key.

Let's look a bit into the structure of the xrdesktop project! xrdesktop is in
the middle of a major refactoring currently, with much of the code that is not
desktop-specific being moved out to a separate namespace called G3k. It should
result in a very easy to learn system, but right now it is a bit of a mess.

The project uses *Vulkan* to draw graphics and *OpenXR* to interface with VR
hardware. Instead of writing directly against these APIs, two GLib wrappers were
created: *Gulkan* and *GXR.* GXR used to have support for both OpenVR (SteamVR,
basically) and OpenXR, but the refactoring simplifies this to just OpenXR. We
also have a vector math library called *graphene*, which provides your basic
matrix operations. Then there is *G3k*, which you can think of as Gtk for VR. It
should provide things like buttons, containers, stuff like that. I say 'should',
because it is only a few days old at this point! Finally, there is *xrdesktop*,
which contains the code that positions your desktop windows in VR.

At first, I created a class XrdKeyboard (more on how to create classes with GLib
in an upcoming part 2 of *Getting Started with GLib*), but it seemed like this
was the wrong level of abstraction, so I renamed it G3kKeyboard in the end. But
because the refactoring is not complete yet, it still depends on XrdClient for
now. This is also true for G3kButton, which is implemented as an XrdWindow at
the moment. The buttons of G3kKeyboard are currently XrdWindows, but that will
surely change in the future.

Currently, when you click on a key, it prints a message to *stdout*. Next up,
I'll read up a bit more about GObject signals and properties, so we can connect
the keyboard events to the active window. Let's end with another screenshot
showing our amazing VR stdout printer!

![Keyboard with callback](/assets/2021/06-14-keyboard-with-callbacks.png)
