---
layout: post
title: Google Summer of Code 2021, Retrospective
tags: GSoC2021
---

In this post, we look back on the virtual keyboard project that I worked on for
xrdesktop as a participant in Google Summer of Code 2021. It was a great
experience and a good introduction to open source development. 

![Keyboard emoji mode](/assets/2021/07-26-keyboard-mode4-emoji.png "Keyboard emoji mode")

## Current Status

The project was very successful in its goal of providing a text input method for
xrdesktop independent of SteamVR's virtual keyboard. The keyboard is usable for
people in 56 languages and it works with KDE. You can try it out by compiling
the 'next' branches of the various libraries. See below for detailed
instructions.

![Arabic keyboard layout](/assets/2021/08-19-arabic-keyboard.png "Arabic keyboard layout")

## How to Run

You will need to compile and run it for KDE. Follow
[this how-to guide](https://gitlab.freedesktop.org/xrdesktop/xrdesktop/-/wikis/Installation-from-source)
to install xrdesktop from source. However, you need to use different branches in
a few cases:

* gulkan: [gulkan:next](https://gitlab.freedesktop.org/xrdesktop/gulkan/-/tree/next)
* gxr: [gxr:next](https://gitlab.freedesktop.org/xrdesktop/gxr/-/tree/next)
* xrdesktop: [remcokranenburg:keyboard-internationalization](https://gitlab.freedesktop.org/remcokranenburg/xrdesktop/-/tree/keyboard-internationalization)
* libinputsynth: [remcokranenburg:keyvals-input](https://gitlab.freedesktop.org/remcokranenburg/libinputsynth/-/tree/keyvals-input)
* kwin-effect-xrdesktop: [remcokranenburg:virtual-keyboard-support](https://gitlab.freedesktop.org/remcokranenburg/kwin-effect-xrdesktop/-/tree/virtual-keyboard-support)

![KDE in VR with keyboard](/assets/2021/08-13-kde-in-vr-with-keyboard.png "KDE in VR with keyboard")

## Future Work

During our feedback sessions, my mentors and I decided to focus first on making
the basic keyboard experience complete, by implementing it for a desktop
environment and providing keyboard layouts for a lot of languages. In the
revised planning, the prediction mode was postponed.

A start was made on a predictive mode that would work like the swipe keyboard
mode on Android. Post-GSoC, I'd like to continue this work. When finished, I'd
also like to investigate whether the same technology can be used to add a swipe
mode to GNOME Shell's on-screen keyboard.

The third and most experimental mode was dropped for the time being. It entailed
a [Dasher-like](https://en.wikipedia.org/wiki/Dasher_(software)) interface adapted for a virtual reality environment. Since VR has
such a different way of interacting, a keyboard input method might not make the
most use of your abilities. The hypothesis is that a Dasher-like method might
be faster when used with six-degrees-of-freedom controllers.

While implementing keyboard layouts, I learned that languages with
non-alphabetic writing systems, such as Chinese, need a different way of
handling keyboard input. They need to be able to write tentative words, that can
be *committed* by additional key presses. This feature could potentially be
based on the prediction system, because that also predicts tentative words,
which can be committed by choosing one of the proposals.

## Merge Requests

Some warm-up bugfixes / improvements:

* MERGED [xrdesktop!24](https://gitlab.freedesktop.org/xrdesktop/xrdesktop/-/merge_requests/24) Improve menu position when attached to controller
* MERGED [xrdesktop!27](https://gitlab.freedesktop.org/xrdesktop/xrdesktop/-/merge_requests/27) Bump minimum meson version
* MERGED [xrdesktop!28](https://gitlab.freedesktop.org/xrdesktop/xrdesktop/-/merge_requests/28) Client to shell example
* MERGED [xrdesktop!30](https://gitlab.freedesktop.org/xrdesktop/xrdesktop/-/merge_requests/30) g3k-button: correct calculation of background circle radius
* MERGED [gulkan!15](https://gitlab.freedesktop.org/xrdesktop/gulkan/-/merge_requests/15) Add pango example
* MERGED [libinputsynth!4](https://gitlab.freedesktop.org/xrdesktop/libinputsynth/-/merge_requests/4) gitignore: ignore build directory

The main merge requests, implementing the G3kKeyboard and making it work in KDE:

* MERGED [xrdesktop!31](https://gitlab.freedesktop.org/xrdesktop/xrdesktop/-/merge_requests/31) Add virtual keyboard
* OPEN [kwin-effect-xrdesktop!4](https://gitlab.freedesktop.org/xrdesktop/kwin-effect-xrdesktop/-/merge_requests/4) Support G3kKeyboard

Implementing multiple keyboard layouts:

* DECLINED [libintputsynth!5](https://gitlab.freedesktop.org/xrdesktop/libinputsynth/-/merge_requests/5) Keysequence input
* OPEN [libinputsynth!6](https://gitlab.freedesktop.org/xrdesktop/libinputsynth/-/merge_requests/6) Keyvals input
* OPEN [xrdesktop!33](https://gitlab.freedesktop.org/xrdesktop/xrdesktop/-/merge_requests/33) Keyboard internationalization

## Blog Posts

This project also prompted me to finally start a blog:

* [Getting Started with GLib]({% post_url 2021-05-30-getting-started-with-glib %})
* [Introduction to xrdesktop]({% post_url 2021-06-14-introduction-to-xrdesktop %})
* [Keyboard Event Handling]({% post_url 2021-06-27-keyboard-event-handling %})
* [Emoji Mode ✨]({% post_url 2021-07-26-emoji-mode %})
* [KDE in VR with Keyboard Support]({% post_url 2021-08-13-kde-in-vr-with-keyboard %})
* [Keyboard Layouts]({% post_url 2021-08-19-keyboard-layouts %})

## Final Words

I loved working on this project. I've always been interested in computer
graphics and virtual reality, so this was the perfect fit for a start in open
source development.

I would like to thank my mentors Christoph Haag and Lubosz Sarnecki for their
fantastic support, and Google and Collabora for providing the opportunity to
work on such a cool project. We will see each other again, I hope!
