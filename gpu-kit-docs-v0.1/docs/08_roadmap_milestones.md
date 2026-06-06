# Roadmap and milestones

This roadmap is intentionally conservative.

The goal is to prove the architecture in layers, not to chase impressive demos too early.

## Phase 0: Documentation and boundaries

Deliverables:

```text
manifest
architecture overview
API sketch
control/data plane model
capability model
risk document
research notes
```

Exit criteria:

```text
core terms are named
non-goals are explicit
Mesa/OpenGL/Vulkan roles are defined
hot path rules are documented
```

## Phase 1: Skeleton repository

Deliverables:

```text
src/libgpu
src/gpu_server
src/backends/null
src/backends/software
src/tools/gpu_info
demos/first_surface
```

Exit criteria:

```text
builds on Haiku
library links
server starts
gpu_info prints null/software device
```

## Phase 2: Null backend

The null backend accepts API calls and validates lifecycle without rendering.

Deliverables:

```text
device enumeration
capability reporting
context creation
resource handles
command buffer recording
submit
fence signaling
```

Exit criteria:

```text
application can create device, queue, surface placeholder, command buffer and fence
no rendering yet
debug trace visible
```

## Phase 3: Software backend

The software backend renders into memory or BBitmap-compatible surfaces.

Deliverables:

```text
ClearImage
CopyBuffer
CopyImage
BlitImage
Present to visible Haiku surface
Fence
basic timing output
```

Exit criteria:

```text
first window shows first frame
surface can be resized
fence semantics work
no per-draw BMessage design
```

## Phase 4: Multimedia surface demo

Deliverables:

```text
four synthetic video surfaces
independent scaling
basic color conversion path
animated surface
responsive window movement
```

Exit criteria:

```text
demo shows BeOS-style multimedia flow
Tracker/Deskbar remain responsive
startup remains fast
```

This is the first philosophical milestone.

## Phase 5: Trace backend and diagnostics

Deliverables:

```text
command trace dump
resource lifetime logging
capability dump
submission timing
copy count instrumentation
server bottleneck metrics
```

Exit criteria:

```text
hot path costs can be measured
every copy can be found
fence timing visible
```

## Phase 6: Reference GPU research backend

Deliverables:

```text
device detection
basic initialization
memory allocation experiment
queue experiment
no-op submit
fence/interrupt experiment
```

Exit criteria:

```text
real hardware command submission is understood enough to define next step
system can recover or fail cleanly
```

## Phase 7: First hardware image operation

Deliverables:

```text
clear render target
copy or blit image
present or readback
```

Exit criteria:

```text
hardware-produced pixels are visible or verifiable
fence signals completion
no app has direct GPU access
```

## Phase 8: Minimal 3D

Deliverables:

```text
fixed triangle path
vertex buffer
simple render target
present
```

Exit criteria:

```text
native GPU-Kit triangle demo
no OpenGL
no Vulkan as public API
```

## Phase 9: Compatibility experiments

Only after native primitives work.

Deliverables:

```text
minimal OpenGL compatibility mapping
SDL renderer backend experiment
possibly VideoStreams-inspired surface bridge
```

Exit criteria:

```text
compatibility layers use GPU-Kit
they do not change GPU-Kit into their own shape
```

## Phase 10: Stabilization discussion

Deliverables:

```text
review API
review memory model
review app_server integration
review media integration
review security model
```

Exit criteria:

```text
decision whether project remains research, becomes library, or proposes Haiku integration
```

## Anti-roadmap

Do not start with:

```text
full OpenGL
full Vulkan
full Mesa integration
Steam
AAA games
all GPUs
large HAL
shader language design
```

Those are not first milestones.
