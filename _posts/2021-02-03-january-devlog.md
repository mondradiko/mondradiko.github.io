---
title: January Devlog
---

# glTF Models

The most significant feature added to Mondradiko this month has been glTF
loading. [glTF](https://www.khronos.org/gltf/) is a model format developed by
the Khronos group, originally intended for ease of 3D asset delivery online, and
drawn using a graphics API like WebGL. However, because the model format is so
compact and easy to work with, it can also come in handy for a native
application like Mondradiko.

Here is a screenshot of a glTF model, [Crytek Sponza](https://github.com/KhronosGroup/glTF-Sample-Models/tree/master/2.0/Sponza),
that has been baked into an asset bundle and loaded into the engine:

![Sponza albedo](/assets/images/bundler-sponza-albedo.png)

As you can see, this is a reasonably complex model, which will make this example
world very useful for testing out different materials and rendering
optimizations in the future. However, Mondradiko's glTF loading
capabilities are not entirely finished up, and there are lots of features
that the loader doesn't support yet. This will have to change in the near
future, as having full glTF compliance gives us a lot of flexibility in
creating experimental content for the engine while it's still in version 0.

# Asset Bundler

As mentioned above, 3D models, and all other runtime assets, like textures,
sounds, and scripts, are stored in "asset bundles." Asset bundles contain
all of the assets that a server needs a client to have to connect and interact
with the world. The tool to create asset bundles and therefore in-engine content
is called `mondradiko-bundler`, or just the "bundler" for short. For now, it
will replace a traditional editor, until we can make a more sophisticated
graphical editor ourselves.

On the more technical side of things, the bundler, when ran, loads a
`bundler-manifest.toml` configuration file from a directory
passed as an argument, and converts all of the files specified in the manifest
from formats like glTF, PNG, WAV, etc. to Mondradiko's internal asset format.
Then, it automatically packs the assets into "asset lumps," which are
collections of assets, and stores them on disk as individual files to be loaded
into RAM as needed. Finally, the metadata about an asset bundle and its lumps
is stored in the "registry."

For instance, the `v0/Sponza` example from the
[examples repository](https://github.com/mondradiko/mondradiko-examples) can be
bundled like this:

```
mars@sol ~/Code/mondradiko/builddir $ bundler/mondradiko-bundler ~/Code/mondradiko-examples/v0/Sponza/
[DBG bundler/Bundler.cc:26]                       Bundler manifest: /home/mars/Code/mondradiko-examples/v0/Sponza/bundler-manifest.toml
[DBG bundler/Bundler.cc:27]                       Bundler source dir: /home/mars/Code/mondradiko-examples/v0/Sponza
[DBG bundler/Bundler.cc:28]                       Bundler bundle dir: ./
[DBG assets/saving/AssetBundleBuilder.cc:18]      Building asset bundle at ./
[ ... bundler log ... ]
[DBG assets/saving/AssetBundleBuilder.cc:98]      Writing lump 0
[DBG assets/saving/AssetBundleBuilder.cc:99]      Lump size: 131546440
[DBG assets/saving/AssetBundleBuilder.cc:116]     Lump has checksum 0x9cb538e2ef0fad90
[DBG assets/saving/AssetBundleBuilder.cc:98]      Writing lump 1
[DBG assets/saving/AssetBundleBuilder.cc:99]      Lump size: 134200584
[DBG assets/saving/AssetBundleBuilder.cc:116]     Lump has checksum 0x19626caad0b327d0
[DBG assets/saving/AssetBundleBuilder.cc:98]      Writing lump 2
[DBG assets/saving/AssetBundleBuilder.cc:99]      Lump size: 7680288
[DBG assets/saving/AssetBundleBuilder.cc:116]     Lump has checksum 0xc509d4f579abc26e
[DBG assets/saving/AssetBundleBuilder.cc:22]      Cleaning up asset bundle ./
mars@sol ~/Code/mondradiko/builddir $ ls -l
...
-rw-r--r-- 1 mars mars 131546440 Jan 31 16:29 lump_0000.bin
-rw-r--r-- 1 mars mars 134200584 Jan 31 16:29 lump_0001.bin
-rw-r--r-- 1 mars mars   7680288 Jan 31 16:29 lump_0002.bin
-rw-r--r-- 1 mars mars      2968 Jan 31 16:29 registry.bin
...
mars@sol ~/Code/mondradiko/builddir $ server/mondradiko-server & client/mondradiko-client
```

After a world's assets are bundled, they will be packaged into a
compressed format like LZMA, and made available for download to clients
who wish to connect to a server. More information on the content model, lump
compression, and the bundler system will be posted in a future devlog.

Here's some improvements that we can still make to the bundler:
- Converted asset caching
- Manual prefab building
- Script instantiation

# Graphics

After loading the Crytek Sponza model into the engine, we can start
working on adding materials and proper shading. Here's a screenshot
demonstrating simple diffuse lighting with some point lights:

![Diffuse lighting](/assets/images/materials-diffuse-lighting.png)

Now we're getting somewhere! Just having these simple point lights placed
around the scene livens it up quite a bit. However, there is still a lot of
room for improvement. Here are some things that we'll be working on this
February:

### Aliasing Reduction
- Texture mip-mapping
- Multisampling
- Anisotropic filtering

### Features
- Alpha transparency
- Normal mapping
- Physically-correct PBR
- Model skinning
- Blend targets

### Performance Improvements
- Depth prepass
- Staging ring buffer for GPU transfers
- Mesh data pool
- Compute shader culling
- Shader specialization constants

# VRM Avatars

[VRM](https://vrm.dev/en/vrm_about/) is a popular model format that
standardizes all of the components that you may expect in a 3D virtual
avatar. This includes bones, materials, and facial animation blend targets.
VRM avatar support is a big priority for us, and this is one reason why
we're aiming for full glTF compliance so early.

Here is a VRM model created in [VRoid Studio](https://vroid.com/en/studio/)
that we are using to test VRM importing:

![Complete avatar](/assets/images/example-avatar-complete.png)

Credits to Korakoe for making and sharing the model. Thank you Korakoe! :)

This is what the avatar currently looks like when imported into the engine:

![Incomplete avatar](/assets/images/example-avatar-incomplete.png)

There are obviously several problems with the current VRM loader.
The eyes, eyebrows, and eyelash primitives are missing their textures,
and the hair is missing altogether. However, the main body and its textures
are loading correctly, so this is a step in the right direction!

# Scripting

Unfortunately, the scripting API did not get fleshed out as much as we'd like
in January, so next month we'll be developing script bindings in conjunction
with the UI subsystem. Keep an eye out for future devlogs about them!

# Console Variables (CVars)

A "console variable," or "CVar," is a global variable that controls an aspect of
the engine at runtime, such as graphics settings, login information, or anything
else that a user would have to configure themselves. The CVars are currently
loaded from a [TOML file](https://toml.io/en/) at runtime, but in the future,
they will also be modifiable from an actual console, and saved back to disk.

Here's an example `config.toml`:

```toml
[server]
max_tps = 50.0
update_rate = 20.0

[client]
username = "ExampleUsername"
metaverse_provider = ""

[glyphs]
font_path = "/usr/share/fonts/mononoki/mononoki-Regular.ttf"
sdf_scale = 2.0
sdf_border = 1.0
sdf_range = 4.0

[renderer.debug]
enabled = true
draw_lights = true
draw_transforms = true
```

# User Interface

This month, we've implemented basic TrueType font rendering, using
a graphics technique called a
[signed distance field](https://en.wikipedia.org/wiki/Signed_distance_function),
or SDF for short.

![SDF demo](/assets/images/sdf-demo.png)

(The font is [Mononoki](https://madmalik.github.io/mononoki) by Matthias Tellen)

The SDF raster images are generated during client initialization using
[Chlumsky's msdfgen library](https://github.com/Chlumsky/msdfgen), then
packed into a 4-channel texture atlas that looks like this:

![SDF atlas](/assets/images/sdf-atlas.png)

The texture contains RGB data representing the hard edges of the glyphs, as
well as an alpha channel encoding a basic SDF that can be used for soft
glyph effects. Finally, individual glyphs from the texture are mapped onto
quads, and rendered into the viewport with a special SDF glyph shader.

Here is a close-up of one of the font glyphs rendered using this method:

![SDF close-up](/assets/images/sdf-close-up.png)

Although there are a couple of artifacts in the glyph rendering, such as the
flat line segments on the end of the arm of the "e," the glyph as a whole is
remarkably smooth, and should be more than sufficient in quality for typical text
rendering. If for some reason you need the quality to be higher, you can
increase the SDF atlas's resolution by modifying the `glyphs.sdf_scale` CVar.

Text rendering is an essential component of any user interface, but we still
have a long ways to go before we have anything particularly useful or impressive.
Here's some features that we can work on implementing in coming months:

- Proper font glyph alignment
- A text console for changing CVars at runtime
- Networked UI panel synchronization
- UIML parsing and DOM
- Support for drawing SDF glyphs from other sources, like SVGs
- A variety of UI widget classes (buttons, labels, sliders, etc.)
- User interaction with VR controllers

# Contributing

## Suggestions

Any ideas or suggestions for Mondradiko are welcome! Please
join our [Discord server](https://discord.gg/CsDqbFw), where
I and the other developers are active, and drop any thoughts
you have. As a potential future user, your opinions are important.

## Funding

If you'd like to help support the development of Mondradiko, please consider
[becoming my patron on Patreon](https://patreon.com/marcelinecramer)!
A large part of Mondradiko's development relies
on cloud services to function, such as automated
builds, testing, and packaging, along with hosting
the world servers themselves. I also save up the donations
to spend on VR and PC hardware, so that I can optimize
the engine for a variety of platforms. Anything helps!

## Programming

If you have any coding experience, either from making
games in Unreal Engine or Unity, or low-level C++
programming, we need your help!

Please join the [Discord server](https://discord.gg/CsDqbFw)
to get in touch with the project maintainers and
discuss how you can help.
