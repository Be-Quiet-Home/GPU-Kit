# Research note: VideoStreams

## Source

VideoStreams is a Media Kit-like 3D hardware acceleration experiment for Haiku by X547.

## What problem does it solve?

It explores how rendered buffers can flow between producers and consumers in a Haiku-like way.

It introduces concepts such as:

```text
BufferProducer
BufferConsumer
SwapChain
Compositor consumer
Screen consumer
Recorder or network consumers
```

## Useful concepts

GPU-Kit should study:

```text
producer/consumer separation
rendered buffer ownership
swap chain management
cross-process surface flow
compositor integration
VSync and presentation concerns
```

These are especially relevant for the surface/stream layer above GPU-Kit.

## Concepts to reject or avoid

VideoStreams appears to begin from OpenGL/Vulkan producers. That is too high-level for GPU-Kit core.

GPU-Kit should not begin with:

```text
Vulkan as conceptual center
OpenGL/Vulkan-produced frames as the starting point
rendered buffers without native command model
```

## Haiku translation

Possible assimilation:

```text
BGPUSurface
BGPUImage
BGPUSwapChain
BGPUProducer
BGPUConsumer
```

But these should sit above or beside the lower GPU primitives:

```text
Device
Memory
Buffer
Queue
CommandBuffer
Fence
```

## Open questions

```text
Should producer/consumer belong to GPU-Kit or a separate Surface/Stream Kit?
Can Media Kit nodes expose GPU images safely?
How should app_server consume GPU surfaces?
How much of swap chain management should be hidden behind BGPUSurface?
```
