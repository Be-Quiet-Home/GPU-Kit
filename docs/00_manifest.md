# Manifest

## The sentence

```text
Multimedia is Haiku’s natural state.
```

GPU-Kit exists to make this sentence true again.

The goal is not simply to make OpenGL work. The goal is to give Haiku a native GPU and media path that feels as direct, responsive and integrated as the classic BeOS multimedia ideal.

## The problem

Modern graphics stacks are powerful, but they often arrive as complete foreign ecosystems. They bring their own vocabulary, assumptions, build systems, compatibility burdens, memory models, surface models and debugging culture.

For a small operating system this can become architectural debt.

A bolted-on stack may produce a demo, but it can also make the system feel less like itself. Each flange adds capability, but also cost:

```text
RAM
startup time
maintenance
abstractions
foreign assumptions
debugging surface
identity loss
```

GPU-Kit starts from the opposite direction. The question is not:

```text
How do we make Haiku compatible with a foreign graphics stack?
```

The question is:

```text
How should Haiku speak to a GPU if Haiku defines the conversation?
```

## Core principles

### 1. The GPU is a protected coprocessor

The GPU is not treated as a dumb framebuffer and not as something applications may control directly. It is a protected coprocessor service.

Applications submit work. The system validates, schedules and presents the result.

### 2. Applications never talk to the GPU

Applications talk to GPU-Kit objects:

```text
BGPUDevice
BGPUBuffer
BGPUImage
BGPUSurface
BGPUQueue
BGPUCommandBuffer
BGPUFence
```

The driver and the GPU remain behind the system boundary.

### 3. Haiku defines the public shape

OpenGL, Vulkan, SDL, Mesa and similar systems may be studied, adapted or wrapped. They must not define the public GPU-Kit model.

```text
Foreign APIs are guests.
Haiku Kits are architecture.
```

### 4. Control plane and data plane are separate

Haiku messaging is excellent for lifecycle, coordination and notification. It is not the hot path for draw commands.

```text
Control plane:
  BMessage, ports, BLooper, BHandler, server coordination

Data plane:
  shared memory, command buffers, mapped resources, fences
```

### 5. Capabilities are explicit

GPU-Kit does not pretend that every device supports every feature. It reports what the current backend can do. Applications adapt.

### 6. Performance is a design constraint, not a later optimization

Haiku boots quickly. GPU-Kit should preserve that spirit:

```text
quick first frame
low latency
few copies
few context switches
asynchronous submission
predictable behavior
```

### 7. Multimedia comes before API vanity

The first meaningful demonstration is not a synthetic benchmark. It is system behavior:

```text
multiple video surfaces
fast blits and scaling
window movement remains fluid
Tracker and Deskbar remain responsive
3D appears as a natural extension
```

OpenGL compatibility becomes easier once the native surface, buffer, queue and presentation model is correct.

## What GPU-Kit is not

GPU-Kit is not a game engine.

GPU-Kit is not a cross-platform abstraction library.

GPU-Kit is not a public Vulkan clone.

GPU-Kit is not a public OpenGL clone.

GPU-Kit is not Mesa with Haiku naming.

GPU-Kit is not a promise to support every GPU.

GPU-Kit is an attempt to express GPU acceleration in native Haiku terms.

## The BeOS line

BeOS did not feel impressive because it exposed the same layers as everyone else. It felt impressive because multimedia behaved like a built-in system property.

GPU-Kit should continue that line:

```text
small APIs
system services
asynchronous media flow
responsiveness
clear ownership
no accidental framework empire
```

The aim is not nostalgia. The aim is continuity.
