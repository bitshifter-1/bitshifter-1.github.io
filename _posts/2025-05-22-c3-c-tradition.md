---
layout: post
title: "C3: Iterative Innovation in the C Tradition"
date: 2025-05-22
---
In the ever-evolving landscape of systems programming languages, C3 emerges as a language that seeks to modernize C without abandoning its foundational principles. Designed to be an evolution rather than a revolution, C3 is intended to leverage familiar syntax while introducing enhancements aimed at improving safety, ergonomics, and performance.

The language began development in mid 2019, after C2 contributor Christoffer Lernö decided to branch out on his own, and the compiler is still primarily a single-developer effort, even though it experienced a breakthrough in popularity from mid 2024.

## Philosophy

Among other things, C3 [lists](https://c3-lang.org/getting-started/design-goals/) the following design goals:

- Procedural language, with a pragmatic ethos to get work done.
- Seamless C integration.
- Ergonomic common patterns.
- Avoid “big ideas”.

Lernö’s approach to language design, judging from his blog on c3.handmade.network, is deeply pragmatic, iterative, and user-focused. He prioritizes practicality over purity, ergonomics over novelty, and clarity over cleverness. While not using the "Joy of Programming" motto, this language clearly shares priorities with Odin, something Lernö himself has pointed out:

> *"C3 has a slightly different feature set than Odin [...] but the goals aligns[sic] strongly with Odin's."* [link](https://news.ycombinator.com/item?id=43569724)

## First impressions

That said, what about actually using C3? The first "Hello World" is somewhat surprising. Even though C3 claims to be an evolution of C, the example looks unfamiliar:

```c
module hello_world;
import std::io;

fn void main()
{
    io::printn("Hello, world!");
}
```
We still have the familiar `void main()`, C3 unexpectedly introduces an `fn` keyword. Also somewhat controversial, C3 seems to embrace `::` as the module separator, which is reminiscent of C++ rather than C, leading to comments such as:

> *"One thing I just can't understand is proactively using the :: syntax. It's sooo ugly with so much unnecessary line noise" [link](https://news.ycombinator.com/item?id=43569724)

C3 somewhat surprisingly doesn't provide any automatic header imports unlike Zig, but using C from the language is surprisingly straightforward. I managed to write a straight-to-LibC hello world like this:

```c
extern fn int puts(char* s);
fn void main()
{
    puts("Hello world!");
}
```

Moving over to Raylib, it's straightforward to grab Raylib using the "vendor-fetch" command of the compiler. Out of the box it gives us a `.c3l` file, which is C3's library format. This seems mainly to be easy to use with the C3 projects, but I was able to use it directly with a single file.

```c
import raylib5;

fn void main()
{
    rl::initWindow(1280, 720, "Testing");
    Vector2 pos = { 640, 320 };

    while (!rl::windowShouldClose()) {
        rl::beginDrawing();
        rl::clearBackground(rl::BLUE);
        rl::drawRectangleV(pos, {32, 32}, rl::GREEN);
        
        if (rl::isKeyDown(rl::KEY_LEFT)) {
            pos.x -= 400 * rl::getFrameTime();
        }
        if (rl::isKeyDown(rl::KEY_RIGHT)) {
            pos.x += 400 * rl::getFrameTime();
        }
        if (rl::isKeyDown(rl::KEY_UP)) {
            pos.y -= 400 * rl::getFrameTime();
        }
        if (rl::isKeyDown(rl::KEY_DOWN)) {
            pos.y += 400 * rl::getFrameTime();
        }
        rl::endDrawing();
    }
    rl::closeWindow();
}
```

Aside from the aforementioned `:` this is just C. For sticking to the basics and not changing what doesn't need to be changed, C3 really gets a big thumbs up. For a C programmer it can’t get much simpler than this.

## Other language features

On its homepage C3 boasts of "compile time and semantic macros", "gradual contracts", "zero overhead errors" and "runtime and compile time reflection". While the macros and compile time seems to be a simpler version of Zig's comptime, but this appears to be a deliberate choice. The design is explained in his blog post [The downsides of compile time evaluation](https://c3.handmade.network/blog/p/8590-the_downsides_of_compile_time_evaluation)

He writes:
> *"Macros and compile time form a set of meta programming tools, and in general meta programming has very strong downsides in terms of maintaining and refactoring code"*

It’s clear that compile-time functionality is available but not intended for heavy use, unlike Zig or Jai.

## Error handling in C3

C3's error handling is probably the biggest hurdle in learning the language. The docs spend two different pages trying to explain how the errors work, but still leaves you somewhat confused:

```c
File? file = file::open("test.txt", "rb");
```

This would look familiar to users of Swift and Kotlin, and largely you can unwrap or force unwrap in the same way but there's a twist: this optional value also carries an error code as well and it's not possible to use it in structs or as parameters.

In Swift we can do optional chaining like `file?.close()`, but in C3 it's implicit: `file.close()`. This novel take on errors is either brilliant or just weird.

On the Zig discord a friendly user pointed out that it's is making it easy to use C3 from C:

> *"it's a better version of C's convention for returning int where -1 means error, positive or 0 means success, and maybe other negative values mean other specific errors. Instead of returning -1 or other negative error codes, you return foo?, and the user will still probably still check it as a binary condition, as in, in C, result = foo(); if (result < 0), and in C3, if (catch excuse = foo())".*

On the C3 discord there was some dissent on whether it was good. One user said:

> *"I love everything in C3 except the error system.  I am using the error system only in inevitable situations."*

However, most other C3 users seemed to disagree with that opinion:

> *"For me error handling was one of the reasons to switch to C3 from Odin. IMHO in C3 it’s (1) much easier to compose code and (2) much harder to ignore error by accident."*

> *"Error handling is also one of the reasons I prefer C3 over Odin."*

For a language designer who is advocating only [innovating if there is no other option](https://c3.handmade.network/blog/p/8682-some_language_design_lessons_learned), it's clear that
Lernö must have felt that adding error handling was a must-have improvement.

## Still small and evolving

While Odin and Zig are fairly stable feature wise (and V at least intending to be so), C3 is still refining  itself. While the language is stable enough to only have breaking releases for dot-1 increments, it's still evolving with operator overloading for arithmetics only added in a recent release.

While the Raylib bindings were fine, it is clear that all the "vendor-fetch" libraries might not be polished. Lernö admits as much on the C3 discord: 

> *"As I've said before, I need to take a more closer[sic] look at vendor this year to try to make them more consistent. People have been contributing but I haven't put in the time to guide it."*

C3’s community remains comparatively small next to Zig, Odin, and V. On the positive side, Lernö has a stellar reputation for responding to bug reports. Release schedules also follow a regular timetable, with new 0.0.1 releases every month. Odin similarly has a monthly release schedule, but Zig still (at the time of this writing) struggles with their massive dot-1 yearly releases which puts a lot of stress on the community.

Still it's clear that it doesn't yet have the same momentum.

## Compile time and operator overloading

Aside from error handling, the major difference compared to C is the macro system. Gone is the trusty – but problematic - C preprocessor, replaced by compile time macros similar to Zig. Although whereas Zig’s comptime is sometimes somewhat implicit, C3's macros are clearly inspired by the C preprocessor in that the compile time statements are orthogonal in style to runtime code, which has been both applauded and criticized.

Operator overloading is noteworthy in that neither Odin nor Zig – the other two most C-like alternatives – have been interested in implementing it. Andrew Kelley expressing it to be in violation to Zig's philosophy. It's interesting that C3 chooses to be more similar to Jai and V, both of which support operator overloading.

## Summary

C3 is a compelling option, particularly for C and C++ developers: it seeks to modernize the C experience without abandoning the language's core principles.  Its enhancements in safety, performance, and ergonomics make it a strong contender in the low level programming domain and beyond. While it may not yet have the extensive ecosystems of some of its peers, its design philosophy and compatibility with C position it as a practical choice for projects requiring both modern features and legacy integration.

Compared to Jai, Odin, Zig, and V, C3 clearly carves out its own principles ideas, distinct from the others. While its features are more complex than Odin and Zig's, in part due to its commitment to preserving C's syntax and semantics, whereas both Odin and Zig Zig has had an easier time trimming away features.

That is not to say that C3 feels particularly complex. It's familiar and approachable with excellent ergonomics and a feature set that feels very complete without being overwhelming.
