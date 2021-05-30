---
layout: post
title: Getting Started with GLib
---
For GSoC 2021, I will be working on xrdesktop, which is written with the GLib library. Since
learning by explaining is even better than learning by doing, I'll show the basics of using GLib in
this blog post. We'll start with a plain C program and gradually replace parts of it with GLib
functionality.

# What is GLib?

First things first, GLib is a C library designed with two goals:

  1. making C programmers' lives easier by adding high-level language constructs to C 
  1. adding an interoperability layer to C libraries so they can automatically be used in other
     programming languages

Initially, we'll only be concerned with the first goal. GLib extends the standard C environment in
many ways, such as safer string handling, abstract data types, and a fully-featured object-oriented
type system. 

# A Simple Program

As an example program, let's look at a solution for the
[first puzzle](https://adventofcode.com/2019/day/1) of the wonderful *Advent of Code 2019* puzzles.
If you want to do these puzzles yourself, you might want to stop reading further now, although the
first exercise is just an introduction of course!

```c
#include <math.h>
#include <stdio.h>
#include <string.h>

int calculate_fuel(int weight, gboolean include_fuel) {
    int additional_weight = fmax(weight / 3 - 2, 0);

    if(include_fuel && additional_weight > 0) {
        additional_weight += calculate_fuel(additional_weight, include_fuel);
    }

    return additional_weight;
}

int main(int argc, char *argv[]) {
    _Bool include_fuel = 0;

    for(int i = 0; i < argc; i++) {
        if(strcmp(argv[i], "--include-fuel") == 0) {
            include_fuel = 1;
        }
    }

    int total_fuel_required = 0;

    for(;;) {
        int mass = 0;
        int tokens_scanned = scanf("%d", &mass);

        if(tokens_scanned <= 0) {
            break;
        }

        int fuel_required = calculate_fuel(mass, include_fuel);
        total_fuel_required += fuel_required;
    }

    printf("%d\n", total_fuel_required);
    return 0;
}
```

This program must be compiled with `gcc -lm main.c`, to link C's math library to our program.

The first thing we need to do to convert this to a GLib-based program, is to add the `glib.h`
include to the top of the file:

```c
#include <glib.h>
```

We will also need to add some more options to the compiler. You can find out which ones we need
exactly by running `pkg-config --cflags --libs glib-2.0`. On my system, this gives the following
output:

```
-I/usr/include/glib-2.0
-I/usr/lib64/glib-2.0/include
-I/usr/include/sysprof-4
-pthread
-lglib-2.0
```

You see a few include directories that tell the compiler where to find GLib's header files, as well
as `-pthread` which enables thread support, and `-lglib-2.0`, which links the GLib library to our
program.

# GLib Conversion

Now that we have GLib, we can start thinking about improving the code.

## Math

One thing that comes to mind immediately is that we need to include `math.h` and link C's math
library only for the `fmax()` function. If we are using GLib anyway, we can use its `MAX()` macro
instead.

## Booleans

Another peculiarity of C is its boolean logic. It is essentially integer comparison: `0` means
false and any other value means true. Traditional C code would look like this:

```c
int done = 0;

while(!done) {
    do_cool_things();

    if(is_done()) {
        done = 1;
    }
}
```

This is fairly weak from a type system perspective, since it is now easily possible to mix integer
arithmetic and boolean logic. For example, the values `1` and `42` are both *true*, but they do not
equal each other, and it is possible to turn a false value into true by adding to it `value += 1`.
A `_Bool` type was added to the language at a relatively late stage, and it is even possible to use
`bool`, `true` and `false` if you include `stdbool.h`. However, this does not change the fact that
it is all integer logic underneath.

GLib has its own `gboolean` type with `TRUE` and `FALSE` values, so we will use that here.

## Other Basic Types

GLib has a bunch of other types, such as types with explicit sizes (`gint32`, `gint64`), easier
alternatives to some types (`guint` instead of `unsigned integer`, `gconstpointer` instead of
`const void *`), and a few cosmetic types that just exist for completeness (`gint`, `gfloat`).

## Safer Strings

Nul-terminated strings in C are notoriously difficult to use safely, because a string's length is
not stored in the object. It is simply an array of bytes that ends in an all-zeroes byte (a nul).
All string-related functions that need to know the length will search the array until they find such
a nul byte. Often, this byte is accidentally replaced with another character and the string
functions will start operating on memory that is unrelated to the string! This usually leads to code
that is vulnerable to attack.

GLib includes a `GString` type that makes it easier to use strings correctly. It does this by
storing the length alongside the character array. You can use it like this:

```c
void print_hello_world() {
    GString *message = g_string_new("hello");
    g_string_append(message, ", world!");
    printf("%s\n", message->str);
    g_string_free(message);
}
```

This makes it much easier to do string operations like inserting and removing parts without making
memory management mistakes. Note that you must still remember to free the memory you allocated with
`g_string_new()`.

## Automatic Memory Management

Way back in the 1990s, Java was developed as an alternative to C that was not only easier to use,
but also safer. One of these safety features is automatic memory management. Instead of manually
marking an area of memory as being used by an object in our program, the runtime would decide by
itself whether an object and its memory was still reachable in the current scope of the program. 

```java
void printBookTitles() {
    List<Book> books = get_books();

    for(Book book : books) {
        System.out.println(book.title);
    }
}
```

In the above snippet, a list of Book objects is retrieved from the `get_books()` function. Each
book title is printed and then the function ends. As programmers we don't need to know whether
references to `books` still exist after the function ends: if this was the last reference, the Java
runtime will automatically free its memory, but if other references exist, the runtime will keep
the memory marked as being in use. This makes two undesirable things impossible: using the memory
after it has been freed, and keeping the memory marked as used even though it is unreachable by
the program.

GLib has a type `g_autoptr`, which does something similar: the system keeps track of all references
to the object, and if the last reference is removed, the memory is freed. An example:

```c
void print_hello_world() {
    g_autoptr(GString) message = g_string_new("hello");
    g_string_append(message, ", world!");
    printf("%s\n", message->str);
}
```

Note that the memory behind `message` is never explicitly freed. GLib will automatically free the
memory at the end of the function, because `message` is the only reference and it is removed at the
end of the function.

## The Result

Combining all of the above, we get the following program:

```c
#include <glib.h>
#include <stdio.h>

gint calculate_fuel(gint weight, gboolean include_fuel) {
    gint additional_weight = MAX(weight / 3 - 2, 0);

    if(include_fuel && additional_weight > 0) {
        additional_weight += calculate_fuel(additional_weight, include_fuel);
    }

    return additional_weight;
}

gint main(gint argc, gchar *argv[]) {
    gboolean include_fuel = FALSE;

    g_autoptr(GString) include_fuel_argument = g_string_new("--include-fuel");

    for(gint i = 0; i < argc; i++) {
        g_autoptr(GString) argument = g_string_new(argv[i]);

        if(g_string_equal(argument, include_fuel_argument)) {
            include_fuel = TRUE;
        }
    }

    gint total_fuel_required = 0;

    for(;;) {
        gint mass = 0;
        gint tokens_scanned = scanf("%d", &mass);

        if(tokens_scanned <= 0) {
            break;
        }

        gint fuel_required = calculate_fuel(mass, include_fuel);
        total_fuel_required += fuel_required;
    }

    printf("%d\n", total_fuel_required);
    return 0;
}
```

You might have noticed that I still use `scanf()` and `printf()`, even though GLib provides
alternatives such as `g_printf()` and `GUnixInputStream`. It should be possible to use these
constructs, but it requires a different compiler configuration because we then need to use a
Unix-specific version of GLib.

With such a simple example, the conversion to a GLib-based program is maybe not so useful. However,
you will quickly see the benefit when you start working with more complicated data types and their
memory management.

You can find the code for my solution
[here](https://github.com/remcokranenburg/advent-of-glib-2019/tree/main/day01-tyranny). You might
see an interesting file called `meson.build` there, which is an increasingly popular way of
building your programs. But that is a topic for another post! Let's see what the next puzzle brings.
