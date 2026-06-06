# Surface, stream and media model

## Why this layer matters

GPU-Kit should not begin and end with triangles.

The BeOS/Haiku multimedia ideal is about responsive media flow:

```text
video
audio
windows
surfaces
compositing
recording
preview
effects
```

A native GPU foundation should make these flows natural.

## Surface

A `BGPUSurface` is a presentation or exchange target. It may represent:

```text
a window-attached surface
a view-attached surface
an offscreen render target
a video frame destination
a compositor input
a recorder input
```

A surface is not necessarily a screen.

## Image

A `BGPUImage` is pixel storage.

A surface may provide images; a decoder may produce images; a command buffer may read from or write to images.

```text
Media decoder → BGPUImage → Blit/Scale/Convert → BGPUSurface
```

## Producer and Consumer

VideoStreams uses producer/consumer concepts for rendered buffers. GPU-Kit should treat this as an ideas mine, not a foundation.

The useful concepts are:

```text
Producer creates images or surfaces.
Consumer accepts images or surfaces.
SwapChain manages image rotation.
Presentation is asynchronous.
```

GPU-Kit can assimilate these concepts into Haiku terms.

## Swap chain

A swap chain manages multiple images used for presentation.

Conceptually:

```text
Acquire image
Render or blit into image
Present image
Repeat
```

GPU-Kit should expose this only if it improves clarity. A `BGPUSurface` may hide the swap chain initially.

Possible API:

```cpp
class BGPUSwapChain {
public:
    status_t AcquireNextImage(BGPUImage* outImage, BGPUFence* outFence);
    status_t Present(const BGPUImage& image);
};
```

Alternative:

```cpp
class BGPUSurface {
public:
    status_t Acquire(BGPUImage* outImage);
    status_t Present(const BGPUImage& image);
};
```

The simpler surface API may be better for early Haiku integration.

## First multimedia demo

The first meaningful demo should show system character:

```text
four video-like surfaces
independent scaling
window movement remains responsive
one small 3D or animated surface
Tracker and Deskbar remain usable
```

A real video decoder is not required for the first demo. Synthetic moving images are enough to prove surface flow.

## Operations needed early

Before complex 3D, implement:

```text
ClearImage
CopyBuffer
CopyImage
BlitImage
ScaleImage
ColorConvert
Present
```

These operations directly support:

```text
video preview
image compositing
window surfaces
simple effects
media editor timelines
```

## Color conversion

Video is not always RGBA.

Early color formats should include common video formats:

```text
NV12
YUY2
BGRA8
RGBA8
```

Even if the first backend implements conversion in software, the API should acknowledge that video surfaces are first-class.

## Relationship to Media Kit

Haiku's Media Kit already defines a culture of media buffers and streams. GPU-Kit should integrate with that culture rather than invent a parallel universe.

Possible future bridge:

```text
BBuffer / media node output
  ↓
BGPUImage import
  ↓
GPU color conversion / scaling
  ↓
BGPUSurface
```

The exact API requires careful Haiku integration. The principle is clear: media frames should become GPU resources without framework drama.

## Relationship to app_server

Window presentation must feel native.

Bad:

```text
App creates foreign display object.
App creates foreign surface.
App manually coordinates with window server concepts from another OS.
```

Good:

```text
App attaches BGPUSurface to BWindow or BView.
Haiku manages presentation rules.
```

## Surface ownership

Surfaces must be owned and tracked.

Questions:

```text
Which team owns the surface?
Which window or view is attached?
What happens on resize?
What happens when the window closes?
What happens when the device is lost?
Can the surface be recorded?
Can the surface be shared?
```

These questions belong in the system design from day one.

## Presentation modes

Potential modes:

```text
immediate
vsync
mailbox
fifo
offscreen only
recording
```

Do not expose too many modes too early. Begin with safe defaults.

## Rule

The surface/stream layer should make the BeOS-style multimedia demo easy.

If it only makes OpenGL swap buffers possible, it has failed to aim high enough.
