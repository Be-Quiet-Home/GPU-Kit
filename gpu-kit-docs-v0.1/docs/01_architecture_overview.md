# Architecture overview

## System layers

GPU-Kit is split into visible Haiku API, system service, backend and driver layers.

```text
Application
  ↓
libgpu.so / GPU-Kit C++ API
  ↓
gpu_server
  ↓
backend interface
  ↓
software backend or reference GPU backend
  ↓
kernel driver
  ↓
GPU hardware
```

The public API must remain Haiku-native. The backend may contain hardware-specific, vendor-specific or research-derived logic. That logic must not leak into the application model.

## Main components

### GPU-Kit API

A C++ kit used by applications and higher-level Haiku components.

Primary types:

```text
BGPUDevice
BGPUDeviceInfo
BGPUCapabilities
BGPUBuffer
BGPUImage
BGPUSurface
BGPUQueue
BGPUCommandBuffer
BGPUPipeline
BGPUFence
BGPUError
```

This API must feel like a Haiku kit: small, explicit, object-based, system-integrated.

### gpu_server

A userland system service responsible for ownership, routing and policy.

Possible responsibilities:

```text
device discovery
context creation
resource ownership
permission checks
surface association
command submission routing
basic validation
scheduling policy
error reporting
device-loss handling
integration with app_server and Media Kit
```

The server is not a renderer. It is a coordinator and guardrail.

### Backend interface

A narrow internal abstraction between `gpu_server` and actual implementations.

Early backends:

```text
software_backend
nvidia_turing_backend
null_backend
trace_backend
```

The backend interface is not the public API. It may change freely while the idea is being tested.

### Kernel driver

The kernel driver owns the low-level hardware boundary:

```text
PCI discovery
interrupt handling
DMA-safe memory
hardware queues
firmware communication
device reset
memory mapping
security boundaries
```

Applications never depend on driver details.

## Data flow

A typical frame:

```text
1. Application creates or reuses buffers and images.
2. Application writes commands into a command buffer.
3. Application submits the command buffer through GPU-Kit.
4. gpu_server validates ownership and routes submission.
5. Backend/driver submits work to hardware or software backend.
6. GPU executes asynchronously.
7. A fence is signaled.
8. The surface is presented through the Haiku display path.
```

The hot path should avoid per-command IPC.

## Control flow

Control operations use Haiku-style mechanisms:

```text
create device
create context
create surface
allocate buffer
register image
submit command buffer
wait or poll fence
receive device-lost notification
destroy resources
```

The implementation may use `BMessage`, ports or lower-level Haiku primitives. The public API should hide those details unless exposing them is useful and safe.

## Relationship to app_server

GPU-Kit must not treat Haiku windows as foreign display handles. A GPU surface should attach naturally to Haiku windows and views.

Conceptually:

```cpp
BWindow* window = new BWindow(...);
BGPUSurface surface(window);
BGPUContext context(device, surface);
```

The actual implementation may require a more explicit contract with app_server. The principle remains: window integration is native, not a foreign WSI layer exposed to applications.

## Relationship to Media Kit

Haiku's Media Kit already contains the idea of media data flowing through system-defined objects. GPU-Kit should learn from that model.

Media surfaces, video frames and GPU images should become natural partners:

```text
Media decoder
  ↓
BGPUImage
  ↓
scale / convert / composite
  ↓
BGPUSurface
  ↓
window / screen / recorder
```

This is why GPU-Kit should be considered a GPU/media foundation, not just a 3D API.

## Public API vs compatibility APIs

GPU-Kit is the native layer.

Compatibility layers sit above it:

```text
OpenGL-on-GPU-Kit
SDL renderer backend
possible Vulkan-on-GPU-Kit experiment
software fallback renderer
```

They translate foreign expectations into GPU-Kit primitives. They do not define GPU-Kit.

## First backend

The first backend should be chosen for learning, not market coverage.

Recommended sequence:

```text
1. null backend
2. software backend
3. trace backend
4. reference Nvidia backend
```

The software backend proves the API and command model. The trace backend proves observability. The reference GPU backend proves hardware viability.
