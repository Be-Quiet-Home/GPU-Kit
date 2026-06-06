# OpenGL as compatibility layer

## Principle

```text
OpenGL must not be the foundation.
OpenGL may be a compatibility layer.
```

GPU-Kit exists so Haiku can define native GPU behavior first. OpenGL exists to run existing software later.

## Why not OpenGL first

OpenGL brings a large historical state machine. It was not designed as a Haiku kit and should not define Haiku's internal GPU model.

If OpenGL becomes the foundation, GPU-Kit becomes only a wrapper around old assumptions.

GPU-Kit should instead define:

```text
Device
Buffer
Image
Surface
Queue
CommandBuffer
Fence
Capabilities
```

OpenGL can then map its state changes and draw calls to those objects.

## Compatibility direction

Correct direction:

```text
OpenGL call
  ↓
OpenGL compatibility library
  ↓
GPU-Kit commands
  ↓
gpu_server / backend
```

Wrong direction:

```text
GPU-Kit API
  ↓
OpenGL concepts
  ↓
Mesa worldview
```

## Minimal mapping

Examples:

```text
glClear
  → BGPUCommandBuffer::ClearImage

glTexImage2D
  → BGPUImage creation + upload

glDrawArrays
  → BGPUCommandBuffer::Draw

SwapBuffers
  → BGPUSurface::Present

glFenceSync
  → BGPUFence
```

## What OpenGL layer needs from GPU-Kit

A useful OpenGL layer will eventually need:

```text
buffer objects
textures
render targets
samplers
pipelines or equivalent render state
shader support
fences
surface presentation
format conversion
```

But GPU-Kit does not need to implement all of these on day one.

## Early OpenGL compatibility is not required

The first GPU-Kit milestone can succeed without OpenGL.

Early success means:

```text
native surface creation
clear
blit
present
command submission
fence
capability reporting
```

OpenGL belongs after these are stable.

## Mesa role

Mesa may be used as:

```text
reference implementation
source of hardware knowledge
format list
shader compiler source
compatibility layer material
```

Mesa should not become:

```text
public API definition
architecture owner
required mental model for Haiku applications
```

## Debug rule

When OpenGL exists, it must be clear when an application is using:

```text
native GPU-Kit
OpenGL compatibility on GPU-Kit
software fallback
```

A developer tool should report this.

Example:

```text
OpenGL provider:
  implementation: Haiku OpenGL Compatibility
  backend: GPU-Kit
  device: NVIDIA T400 experimental
  fallback: no
```

## Long-term goal

If GPU-Kit is correct, OpenGL support becomes a translation problem rather than an architecture problem.

That is the intended inversion.
