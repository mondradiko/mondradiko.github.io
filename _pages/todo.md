---
title: To-Do
permalink: /todo
---

Many of these items have been migrated from the Trello, and are very
out-of-date. Others are brand-new. Either way, please don't rely on these to-do
items for understanding what needs done just yet.

# General Maintenance

- Make specialized log_ftl class
- Minimize public members
- Diagram class inheritance and dependencies with UML
- Tracy memory profiling
- Remove stb_image_write
- Make tinygltf use rapidjson over json.cpp
- Switch glslangValidator out for glslc
- Add Mondradiko to EnTT wiki
- Make Docker CI environment

# Documentation

- Write style guide
- Write issue guide
- Write PR guide
- Document class interfaces
- Place in-repo documentation
- Migrate to-dos to the repo docs

## PR Checklist

- Update documentation
- Fix style
- Pass CI

# Funding

- mondradiko.io domain
- Mondradiko domain server hosting
- GitHub Sponsors

# Codegen

- Scriptable object base classes
- World component synchronization
- World update event factory implementation
- AssemblyScript bindings

# Porting

## Android/Quest

## Linux

## Windows

# Core Subsystems

## Assets

- Make each engine component responsible for specific assets
- PrimaryAsset and SecondaryAsset
- Generate texture mips in converter
- EnTT resource cache

## Audio

- Find a good audio library (OpenAL is a good choice, but there may be others)

## Avatar

## CVars

- EnumCVar

## Displays

- Move SDL physical device selection to GpuInstance

## GPU

- GpuHeap
- SPIR-V reflection
- GpuCommandBuffer
- GpuPipelineLayout
- Break GpuVector inheritance
- Descriptor binding asserts (and general GpuDescriptor abstraction?)
- Tracy Vulkan profiling

## Jobs

## Network

- Handle unjoined clients
- Validate events when received
- VMC support
- Iamus integration
- REST API for domain server queries?
- Client-broadcast messages

## Physics

- Find a good physics library (almost certainly either PhysX or Bullet; Amani77 will have some more input on which to use)

## Renderer

- PBR toon shader
- Render graph
- Fix framebuffer recreation bug

## Scripting

- Make API for asset acquisition
- AssemblyScript constructor parameters from prefabs
- AssemblyScript object network synchronization

## User Interface

- SDF font rendering

## World
