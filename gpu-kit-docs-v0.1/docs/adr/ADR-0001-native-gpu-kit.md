# ADR-0001: GPU-Kit is a native Haiku kit, not a Mesa/OpenGL/Vulkan frontend

## Status

Proposed.

## Context

Haiku needs GPU acceleration and modern media/graphics capability. Existing ecosystems such as Mesa, OpenGL and Vulkan contain enormous value, but they also carry architectural assumptions from other operating systems and communities.

Haiku has its own system language: kits, servers, messaging, app_server, Media Kit, Interface Kit and responsiveness as a design goal.

## Decision

GPU-Kit will define a native Haiku public API centered on:

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

Mesa, OpenGL, Vulkan and SDL may be used as research sources, compatibility layers or internal backend material. They must not define the public GPU-Kit architecture.

## Consequences

Positive:

```text
Haiku keeps architectural identity.
Applications can use a native GPU model.
OpenGL and SDL become adapters instead of foundations.
```

Negative:

```text
Existing software does not automatically benefit.
More original design work is required.
Compatibility layers must be built later.
```

## Notes

This decision does not reject Mesa, OpenGL or Vulkan as technology. It rejects them as the public shape of Haiku's native GPU model.
