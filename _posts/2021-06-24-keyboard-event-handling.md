---
layout: post
title: Keyboard Event Handling
---
In the past ten days of working on the virtual keyboard for xrdesktop, I've been
trying to make the keyboard behave like a keyboard. That is, it should notice
when one of its keys is clicked by the user, and it should generate the proper
key event when this happens. This is the result:

![Keyboard with events](/assets/2021/06-24-keyboard-events.webm)

## Event Handling

In my previous blog post, you could already see the keys react to clicks, but
this is not enough for a fully functioning keyboard system. For one, you don't
want to handle the events for each button separately; you would have to connect
more than 100 events for a standard keyboard! You just want to connect a single
`key-press-event` to your active window, which would contain all the information
of the pressed key.

The second reason for handling the click events inside the keyboard, is that we
need to keep track of the modifier keys (Shift, Ctrl, Alt, things like that).
The keyboard should maintain the state of these keys (on or off) internally, so
the `key-press-event` would send the key capitalized when Shift is active, or
'Ctrl + X' when Ctrl is active, etc.

So how is this done technically? We need to register a `key-press-event` *signal* for `G3kKeyboard`, which we can then *emit* at the right time. The user
of the keyboard will write an event *callback*, and will *connect* the signal
to it.

What is the right time to emit the `key-press-event`? Well, when a button has
been clicked. So, we need to *connect* the button's `grab-start-event` to a
callback that emits the `key-press-event`.

You might be familiar with event handling in other languages, but in GLib style
this is a fairly verbose affair.

`g3k_keyboard.c`:
```c
static void
g3k_keyboard_class_init(G3kKeyboardClass *klass)
{
  keyboard_signals[KEY_PRESS_EVENT] =
    g_signal_new ("key-press-event",
                  G_TYPE_FROM_CLASS (klass),
                  G_SIGNAL_RUN_LAST,
                  0, NULL, NULL, NULL, G_TYPE_NONE,
                  1, G_TYPE_POINTER | G_SIGNAL_TYPE_STATIC_SCOPE);
}

void
g3k_keyboard_click_cb (XrdWindow     *window,
                       GxrController *controller,
                       gpointer       user_data)
{
  G3kKeyboard *keyboard = G3K_KEYBOARD (user_data);

  g_autofree gchar *title = NULL;
  g_object_get (window, "title", &title, NULL);

  G3kKeyEvent event = { .key = title[0] };

  g_signal_emit (keyboard, keyboard_signals[KEY_PRESS_EVENT], 0, &event);
}
```

Example usage:

```
void
keyboard_pressed_cb(G3kKeyboard *keyboard,
                    G3kKeyEvent *event,
                    gpointer     user_data)
{
  g_print ("Yay, key %c was pressed!\n", event->key);
}

static void
_init_keyboard(XrdShell *shell)
{
  G3kContext *context = xrd_shell_get_g3k (shell);
  G3kKeyboard *keyboard = g3k_keyboard_new (context);

  G3kContainer *container = g3k_keyboard_get_container(keyboard);
  GSList *buttons = g3k_container_get_windows (container);

  for (GSList *button = buttons; button != NULL; button = button->next)
    {
      g_signal_connect (button->data, "grab-start-event",
                        (GCallback) g3k_keyboard_click_cb, keyboard);
    }

  g_signal_connect (keyboard, "key-press-event",
                    G_CALLBACK (keyboard_pressed_cb), NULL);
}
```

## Attachment

Finally, I used some nice existing functionality of xrdesktop and grouped all
keyboard buttons into one `G3kContainer` and attached it to the user's head.
This way, the keyboard is always in view:

![Keyboard attached to head](/assets/2021/06-24-keyboard-attachment.webm)

Up next, let's start on the Swype prediction mode! For this, we need to show the
path that the controller traced over the keyboard's keys and we need to
calculate the word that was most likely meant to be typed.
