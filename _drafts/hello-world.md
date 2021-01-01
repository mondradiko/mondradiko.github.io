---
layout: post
title: Hello World!
author: Marceline Cramer
---

Happy New Year everyone!
This will be the first development blog post for Mondradiko,
and I'd like to go over the current status of the project,
as well as where it's expected to head in the near future.

# Where are we now?

# Next Steps

## WebAssembly

We've picked out [WebAssembly](https://webassembly.org/) (Wasm) to be
the main scripting environment for Mondradiko. This may seem like a
strange choice at first, but we have several good reasons:

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

## Text/Glyph Rendering

An important feature for Mondradiko to have this early on is a text
rendering system. This will be done by baking font files into
GlyphSetAssets, and rendering the text from those glyph sets using
signed-distance field (SDF) techniques, [as popularized by Valve](https://steamcdn-a.akamaihd.net/apps/valve/2007/SIGGRAPH2007_AlphaTestedMagnification.pdf).

This will be very VR-friendly, as no low-resolution fuzziness is
visible as the user moves their view closer to the rendered text.
It will also be very useful for rendering complex UI elements, without
having to process the geometry for those elements.

## Customizable User Interface

## Windows/Quest Builds

## Physically-Based Rendering and Toon Shading

## Avatar Rendering

# Timeline

## January

For January 2021, we'll try to get these things done in this order:

- C++ scripting API
- Asset system and mondradiko-bundler overhaul
- Entity prefabs
- PBR shading
- Rudimentary UI

We'll be posting blog updates on these features as we develop them!

## February and After

- CVAR system
- Make a launcher
- Build for Windows/Quest
- Avatar loading and rigging

# Opportunities for Contribution

## Community
(plug patreon, discord, and youtube here)

## Design

### Suggestions

### GUI

## Programming

### Networking

### Database

### Sound

### Rendering
