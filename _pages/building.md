---
title: Building
permalink: /building
---

# Dependencies

- [CMake](https://cmake.org/)
- [OpenXR SDK](https://github.com/KhronosGroup/OpenXR-SDK)
- [Vulkan](https://www.lunarg.com/vulkan-sdk/)
- [Assimp](http://assimp.org/)
- [GLM](https://github.com/g-truc/glm)
- [glslang](https://github.com/KhronosGroup/glslang)
- [LZ4](https://lz4.github.io/lz4/)
- [xxHash](https://cyan4973.github.io/xxHash/)
- [Flatbuffers](https://google.github.io/flatbuffers/)
- [libktx](https://github.com/KhronosGroup/KTX-Software)

GameNetworkingSockets dependencies:
- [Protobuf](https://github.com/protocolbuffers/protobuf)

libktx dependencies:
- [zstd](https://github.com/facebook/zstd)

Most of these packages should be available through your package manager, but if not, links are provided for convenience.

# Installing the OpenXR SDK

If your OS does not package the OpenXR SDK, you can install it manually (More info at https://github.com/KhronosGroup/OpenXR-SDK).

## Debian/Ubuntu OpenXR Dependencies

```bash
sudo apt install cmake libgl1-mesa-dev libx11-xcb-dev libxcb-dri2-0-dev \
                     libxcb-glx0-dev libxcb-icccm4-dev libxcb-keysyms1-dev \
                     libxcb-randr0-dev libxrandr-dev libxxf86vm-dev \
                     mesa-common-dev
```

## OpenXR SDK Building

```bash
# Clone somewhere useful
git clone https://github.com/KhronosGroup/OpenXR-SDK.git
cd OpenXR-SDK
mkdir -p build/linux_release
cd build/linux_release
cmake -GNinja -DCMAKE_BUILD_TYPE=Release ../..
ninja
sudo ninja install
```

# Debian/Ubuntu

```bash
sudo apt install cmake pkg-config libvulkan-dev libglm-dev \
                     libassimp-dev libsdl2-dev glslang-tools \
                     liblz4-dev libxxhash-dev flatbuffers-compiler \
                     libprotobuf-dev libzstd-dev
```

Also see [Installing the OpenXR SDK](#installing-the-openxr-sdk).

# Compiling

Built with [CMake](https://cmake.org/):

```
mkdir builddir
cd builddir
cmake ..
make
```
