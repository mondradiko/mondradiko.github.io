---
title: Winter Devlog
---

Hello everyone! We've been quite busy for the last couple of months, but we
have a lot to show off in this post, including:

- Scripting upgrades
- Renderer improvements and eye candy
- New asset bundler features
- In-repository documentation

Let's get started.

# Scripting

Thanks to a brand-new automatic code generation system, we're able to easily
add more functionality to the scripting subsystem. Here, we've added bindings
to `TransformComponent`, and written a script to move around some point lights!

<blockquote class="imgur-embed-pub" lang="en" data-id="a/YwgNhqB">
<a href="//imgur.com/a/YwgNhqB">Mondradiko moving point lights</a>
</blockquote>
<script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>

## AssemblyScript

We now support writing scripts in
[AssemblyScript](https://www.assemblyscript.org/)! AssemblyScript is a language
based on TypeScript that targets WebAssembly, allowing for flexible and
user-friendly scripting that doesn't compromise on performance.

Here's the AssemblyScript code used to animate the moving lights above:

```ts
import Transform from "./components/Transform.d";

export class SlidingLight {
  speed: f64 = 3.0;
  destination_a: f64 = -10.0;
  destination_b: f64 = 10.0;

  velocity: f64;

  constructor() {
    this.velocity = this.speed;
  }

  // TODO(marceline-cramer) ComponentScript component fetching
  update(transform: Transform, dt: f64): void {
    let x = transform.getX();
    let y = transform.getY();
    let z = transform.getZ();

    if (x < this.destination_a) {
      this.velocity = this.speed;
    } else if (x > this.destination_b) {
      this.velocity = -this.speed;
    }

    x += this.velocity * dt;

    transform.setPosition(x, y, z);
  }
}
```

The nitty-gritty details behind AssemblyScript are still being worked on, so
expect the specific API demonstrated here to change, but hopefully this gives
you a general idea as to how easy writing scripts for Mondradiko will be.

# Windows Builds

Thanks to [humbletim on GitHub](https://github.com/humbletim), Mondradiko now
builds and runs on Windows! We now rely on vcpkg for downloading and compiling
dependencies, and building the entire project on any OS is now only a two-step
process, dependencies included:

```bash
$ cmake -GNinja ..  # Configure project and build dependencies
$ ninja             # Compile
```

Although Mondradiko runs on Windows now, it's not yet to the point where anyone
other than project developers will find much of a use for it. Mondradiko is
still very early in development, but at least we've ported it to Windows earlier
rather than later. If you're on Windows and want to tinker around or contribute
to it though, now is the time!

# Renderer Upgrades

The renderer has been massively improved since January, and it now features:
- Physically-based rendering (PBR)
- Normal mapping
- Depth prepass
- Masked texture

## Screenshots

![Sponza masking](/assets/images/winter-sponza-with-mask.png)

![Hall PBR](/assets/images/winter-pbr-hall.png)

![Tapestry PBR](/assets/images/winter-pbr-tapestry.png)

![Lion PBR](/assets/images/winter-pbr-lion.png)

## To-Do

The renderer doesn't yet support materials that don't supply a base color
texture, but after support for materials without those textures is implemented,
we'll be able to load a much larger variety of scenes. Keep an eye out for
even more eye candy!

The renderer also doesn't support proper blended transparency yet. More on that
in [the section on glTF support](#gltf-support).

There is also a distracting amount of aliasing in these screenshots. The
aliasing is made worse when moving the camera around the scene. This can be
resolved by adding trilinear filtering, anisotropic filtering, and multisampled
anti-aliasing.

# Bundler Improvements

## Aliases

Assets can now be given string aliases that other assets and manual prefabs can
reference:

```toml
[[assets]]
file = "scripts/SlidingLight.wat"
alias = "SlidingLight"
```

## Manual Prefabs

Prefabs can also now be created in the bundler manifest manually, where before,
the bundler was limited to using prefabs created from glTF models and their node
hierarchies.

Here, for example, we're creating a prefab that has a `PointLightComponent` to
emit light, a `TransformComponent` to position the light in the scene, and a
`ScriptComponent` bound to the script asset declared above to animate the
light's transform.

```toml
[[prefabs]]
initial_prefab = true  # Load this prefab when the world is created

  [prefabs.point_light]
  position = [0.0, 1.5, 0.0]
  intensity = [100.0, 0.0, 100.0]

  [prefabs.script]
  script_asset = "SlidingLight"

  [prefabs.transform]
  position = [0.0, 0.0, 0.0]
```

## Multithreaded Compression

The bundler now begins compressing and saving lumps to disk as soon as their
size limit is reached. This is also done on an individual thread for every
lump. These two things together MASSIVELY reduce bundling time, allowing for
more rapid testing and content creation.

## To-Do

- Dump command
- Bundle caching and dependency graph construction
- Manual prefab nesting
- Prefab glTF model children
- TOML-to-transform helpers
- Cameras for automated tests

# glTF Support

The glTF converter has been patched since January, and now correctly loads
glTF and VRM models!

![VRM avatar (no blending)](/assets/images/winter-vrm-no-blending.png)

You'll notice that there are what look like bright pink sunglasses on the VRM's
face. This is not the fault of the glTF loader, but of the renderer. Masked
materials are drawing correctly, but alpha-blended materials are not, so the
renderer inserts magenta where alpha-blended meshes are supposed to go, until
proper transparency support can be implemented.

If the renderer is configured to skip drawing the pink stand-in meshes, you can
see that the eyes are drawing correctly now too. However, the eyelashes and
eyebrows are missing:

![VRM avatar (with blending)](/assets/images/winter-vrm-with-blending.png)

Little creepy.

There are still lots of things left to implement in the glTF specification, such
as bones, weights, blend shapes, multiple texture coordinates, animations, and
more primitive types, but the loader is coming along quite nicely so far.

# Serverless Entrypoint

The Mondradiko client can now also run standalone (or serverless), meaning
that testing out changes and content is much more convenient, as the server
doesn't have to be launched separately anymore:

```bash
# From:
$ server/mondradiko-server
$ client/mondradiko-client # In another terminal
# To:
$ client/mondradiko-client --serverless
```

# Project Maintenance

As of the time of writing, there are around 17,000 lines of code in the
repository, 12,000 of which are not empty or commented out. Mondradiko is
beginning to become a pretty large project with several subsystems, including a
scripting environment, a codegen system, and an asset management tool. Because
of this, we've been putting a lot of effort this month into making the
repository easier to work with, especially for new code contributors.

## In-Repo Documentation

This month, I've been working on moving all of my thoughts and plans for the
engine into markdown files in the code repository itself. This includes
high-level overviews, diagrams, and explanations for each individual engine
subsystem. This will make it easy for anyone wishing to contribute (or is just
curious about the engine) to jump into the codebase and quickly learn how
everything works.

This is being done in
[PR #18](https://github.com/mondradiko/mondradiko/pull/18), which at the time of
writing may still take a couple more weeks to be completed and merged. However,
this is an essential step to making the engine easy to work with for
programmers, and to growing it into a strong open-source project.

## To-Do Lists

The in-repo documentation also includes comprehensive to-do lists for each
subsystem. This will document what exactly needs done in each part of the
engine and provide resources to help out completing those to-do items. Then,
people wishing to contribute can pick out a to-do item (or several), work on
them in a separate branch, then open a pull request. It will also be each
contributor's responsibility to update these to-do items and other documentation
in their PRs.

## Version 0.1.0 Release

Because of the ever-increasing size of the project, it's been decided that we'll
be creating a version 0.1.0 release of the engine very soon! This is purely
symbolic, but it represents how the majority of the foundation has been laid
down, and how the major design points of the engine have been implemented.

## Roadmap

Around the same time v0.1.0 is released, we'll post another devlog post
detailing a roadmap for this project. This will include goals, an overview of
the engine's design, plans for advertising and documenting development, and what
state the project is expected to be in a year from now.

# Miscellaneous To-Do

- Audio interface (AudioSourceComponent, OpenAL)
- World queries (tags, entity bounding boxes, BVH acceleration)
- ECS overhaul
- Entity relationships and hierarchies
- Resource caching (SDF atlases, GPU pipelines, JIT-compiled WebAssembly)
- User interface (simple demo, scriptable draw functions, basic hooks)
- More scripting (prefab instantiation, more component bindings, messages)
- Avatars (posable VRMs, controller tracking, inverse kinematics)
- Physics (Bullet integration, RigidBodyComponent, synchronization, bindings)

# Opportunities for Contribution

## Suggestions

Any ideas or suggestions for Mondradiko are welcome! Please
join our [Discord server](https://discord.gg/CsDqbFw), where
I and the other developers are active, and drop any thoughts
you have. As a potential future user, your opinions are important.

## Documentation

There's a lot of [documentation being currently written](#in-repo-documentation),
and any feedback is appreciated, as the people reading this devlog post now are
likely the people who are going to be relying on this documentation in the
future.

## Funding

If you'd like to help support the development of
Mondradiko, please become our patron
on [Patreon](https://patreon.com/marcelinecramer)!
A large part of Mondradiko's development relies
on cloud services to function, such as automated
builds, testing, and packaging, along with hosting
the world servers themselves. The money also goes
towards buying a larger variety of VR hardware to
test on and develop for. Anything helps!

<a href="https://www.patreon.com/bePatron?u=46447488" data-patreon-widget-type="become-patron-button">Become a Patron!</a><script async src="https://c6.patreon.com/becomePatronButton.bundle.js"></script>

## Programming

If you have any coding experience, either from making
games in Unreal Engine or Unity, or low-level C++
programming, we need your help! Please join the
[Discord server](https://discord.gg/CsDqbFw)
to get in touch with the project maintainers and
discuss the work needing to be done.
