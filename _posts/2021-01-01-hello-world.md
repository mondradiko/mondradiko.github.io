---
title: Hello World!
---

Happy New Year's everyone!
This is the first development blog post for Mondradiko,
and I'd like to go over the current state of the project,
as well as where it's expected to head in the near future.

# Where are we now?

So far, we've accomplished a lot on the infrastructure of the
engine. We have an Entity-Component-System (ECS) world,
as well as basic network communication and sychronization,
and a rudimentary binary asset system. We've also successfully
ran Mondradiko on a VR headset (HTC Vive on Linux with Monado).

# Next Steps

Development on the engine has been picking up in speed
substantially, because of the time invested building the
framework for it. In the next several months, we can expect
a lot of features to come to fruition:

## Windows/Quest Builds

At the moment, Mondradiko only builds on Linux, meaning
that for most people, you won't be able to run it yet.
Getting it to build on Windows, as well as on Oculus Quest,
is an essential goal, and we're making it a priority.

## WebAssembly

We've picked out [WebAssembly](https://webassembly.org/) (Wasm) to be
the main scripting environment for Mondradiko. This may seem like a
strange choice at first, but we have several reasons:

- Wasm is sandboxed, meaning guaranteed safety
- Wasm is fast and JIT-compiled
- Wasm is compact, for smaller script asset sizes
- Wasm is popular and well-documented
- Content creators have lots of language options

Another very important feature of Wasm is that it's very low-level,
and memory is linear. Local script data can be backed up and synchronized
over a network connection very easily, meaning that scripts are agnostic to
if they're running on the client or server. For anyone that's written
game netcode before, this is very convenient!

All of these features of Wasm make it a very appealing option for
a scripting environment, especially when considering that Mondradiko
has a lot in common with a typical web browser. Users download online
content that is ran locally on their computer, and they must be
safe from malicious code, while content creators should have
an enviroment that is easy to work with and portable.

We're integrating [Wasmtime](https://github.com/bytecodealliance/wasmtime)
as the WebAssembly runtime at the moment, as well as creating
a process for building C++ bindings to write scripts with.
In the future, we may also generate bindings for Rust, or any other
language that can target Wasm.

## Customizable User Interface

A major design goal for Mondradiko is that the user will
have complete control over the design of their user interface,
from everything from local settings menus, to
describing the client-side appearance of a server's menu layouts.

To do this, we'll be using [UIML](https://en.wikipedia.org/wiki/UIML)
for high-level layout descriptions, and writing WebAssemebly
bindings (separate from script bindings!) to define how UI
elements are rendered and interacted with. This will give the
user a huge amount of control over what they can do with their
custom UI.

The Mondradiko client will come with a variety of UI presets
for the user to choose from, but they will also be able to download
premade UI configurations from online. Hopefully, a community of UI
designers will spring up!

## Text/Glyph Rendering

An important feature for Mondradiko to have this early on is a text
rendering system. This will be done by baking font files into
GlyphSetAssets, and rendering the text from those glyph sets using
signed-distance field (SDF) techniques, [as popularized by Valve](https://steamcdn-a.akamaihd.net/apps/valve/2007/SIGGRAPH2007_AlphaTestedMagnification.pdf).

This is very VR-friendly, as no low-resolution fuzziness is
visible as the user moves their view closer to the rendered text.
It is also very useful for rendering complex UI elements, without
having to process and store the polygonal geometry for those elements.

## Avatar Rendering

Mondradiko uses [VRM](https://vrm.dev/en/) for its avatar format.
It's undetermined at this time how exactly VRM models will
be distributed to clients, but the current options are to
download them from a third-party source (such as [VroidHub](https://hub.vroid.com/en/) or [ReadyPlayerMe](https://readyplayer.me/)),
or to download and upload user avatars to the world server,
either directly or through a Matrix room's file storage.
We'll be discussing how this works, as well as Matrix integration, in
coming months.

# Timeline

## January

For January 2021, we'll try to get these things done in this order:

- C++ scripting API
- Asset system and mondradiko-bundler overhaul
- Entity prefabs
- Rudimentary UI
- PBR shading

Get ready for more blog updates on these features as we develop them!

## February and After

- CVAR system
- Launcher and release version control
- Build for Windows/Quest
- Avatar loading and rigging

# Community

We've set up a [YouTube channel](https://www.youtube.com/channel/UCFt7rHYh4ssg1vM1h-1K_RQ),
where we'll post video devlogs in coming months.
Please subscribe!

We also have a [Discord server](https://discord.gg/CsDqbFw),
which is where all dev discussion takes place. Feel free
to join and say hi!

# Opportunities for Contribution

# Design

## Suggestions

Any ideas or suggestions for Mondradiko are welcome! Please
join our [Discord server](https://discord.gg/CsDqbFw), where
I and the other developers are active, and drop any thoughts
you have. As a potential future user, your opinions are important.

## Funding

If you'd like to help support the development of
Mondradiko, please become our patron
on [Patreon](https://patreon.com/marcelinecramer)!
A large part of Mondradiko's development relies
on cloud services to function, such as automated
builds, testing, and packaging, along with hosting
the world servers themselves. Anything helps!

## Programming

If you have any coding experience, either from making
games in Unreal Engine or Unity, or low-level C++
programming, we need your help! 

Please join the [Discord server](https://discord.gg/CsDqbFw)
to get in touch with the project maintainers and
discuss the work needing to be done.
