# Capabilities

## Why capabilities matter

GPU-Kit must not pretend that every device can do everything.

The initial project may support exactly one reference GPU family or even one reference card. That is acceptable. The public API must still be explicit about what is available.

Capabilities prevent hidden emulation, unclear failure modes and accidental universal promises.

## Principle

```text
Ask the device.
Do not guess the device.
Do not emulate silently.
```

## Feature model

A device exposes a set of features.

Examples:

```cpp
enum BGPUFeature {
    B_GPU_FEATURE_TRANSFER,
    B_GPU_FEATURE_BLIT,
    B_GPU_FEATURE_PRESENT,
    B_GPU_FEATURE_RENDER_TARGET,
    B_GPU_FEATURE_TEXTURE_SAMPLING,
    B_GPU_FEATURE_FIXED_PIPELINE_TRIANGLE,
    B_GPU_FEATURE_SHADER_MODULES,
    B_GPU_FEATURE_COMPUTE,
    B_GPU_FEATURE_VIDEO_IMAGE_IMPORT,
    B_GPU_FEATURE_VIDEO_COLOR_CONVERT,
    B_GPU_FEATURE_MULTI_QUEUE,
    B_GPU_FEATURE_TIMELINE_FENCE
};
```

Early backends may support only:

```text
TRANSFER
BLIT
PRESENT
RENDER_TARGET
```

This is enough to test the system model.

## Limits

Capabilities are not only boolean features. Limits matter.

Examples:

```text
maximum image width
maximum image height
maximum image layers
maximum buffer size
maximum command buffer size
number of queues
supported formats
supported tiling modes
alignment requirements
maximum surfaces
maximum frames in flight
```

API sketch:

```cpp
struct BGPUDeviceLimits {
    uint32 maxImageWidth;
    uint32 maxImageHeight;
    uint64 maxBufferSize;
    uint32 maxCommandBufferBytes;
    uint32 maxQueues;
    uint32 maxFramesInFlight;
    uint32 bufferAlignment;
    uint32 imageRowAlignment;
};
```

## Formats

GPU-Kit should define a small native format set first.

Initial candidates:

```text
B_GPU_FORMAT_RGBA8_UNORM
B_GPU_FORMAT_BGRA8_UNORM
B_GPU_FORMAT_R8_UNORM
B_GPU_FORMAT_NV12
B_GPU_FORMAT_YUY2
B_GPU_FORMAT_DEPTH24_STENCIL8
```

Video-related formats should be considered early because the goal is not only 3D. It is multimedia as a system property.

## Queues

A device may expose queue capabilities:

```text
graphics
transfer
compute
present
video
```

For early work, one queue may do everything. The API should not require that this remains true.

## Memory capabilities

A memory type may be:

```text
host visible
device local
coherent
cached
mappable
shareable
presentable
video importable
```

API sketch:

```cpp
struct BGPUMemoryType {
    uint32 flags;
    uint64 heapSize;
    uint32 alignment;
};
```

## Surface capabilities

Surfaces also have capabilities:

```text
present modes
supported image formats
maximum size
resize behavior
composition behavior
vsync support
tearing allowed
window-attached
offscreen
recordable
```

Example:

```cpp
struct BGPUSurfaceCapabilities {
    uint32 minImageCount;
    uint32 maxImageCount;
    uint32 supportedFormats;
    uint32 presentModes;
    bool canResize;
    bool canBeRecorded;
};
```

## Compatibility layers use capabilities

OpenGL-on-GPU-Kit should not assume a complete GPU.

It should ask:

```text
Can I create render targets?
Can I sample textures?
Can I draw indexed triangles?
Can I compile or load shader modules?
Can I present to this surface?
```

If not, the OpenGL compatibility layer may fail gracefully or use a documented software fallback.

## Avoid hidden software cliffs

Bad:

```text
Feature appears supported.
Implementation silently falls back to slow CPU path.
User does not know why performance collapses.
```

Good:

```text
Feature is unsupported in hardware.
Software fallback is explicit.
Debug output says which path is active.
```

## Debug visibility

Capabilities should be printable.

A command-line tool should exist early:

```sh
gpu_info
```

Example output:

```text
GPU-Kit device 0:
  name: Software Backend
  vendor: Haiku
  type: software
  features:
    transfer
    blit
    present
  formats:
    BGRA8
    RGBA8
  queues:
    transfer/present
```

For a reference Nvidia backend:

```text
GPU-Kit device 0:
  name: NVIDIA T400
  vendor: NVIDIA
  type: hardware
  features:
    transfer
    blit
    present
    render-target
    texture-sampling
  notes:
    experimental backend
```

## Rule

Capabilities are part of the identity of GPU-Kit.

They are not optional metadata. They are the contract.
