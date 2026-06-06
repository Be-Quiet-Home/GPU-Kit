# Control plane and data plane

## Rule

```text
BMessage is not the hot path.
```

Haiku messaging is useful and idiomatic, but GPU command traffic must not be serialized as one message per draw operation.

GPU-Kit separates the system into a control plane and a data plane.

## Control plane

The control plane manages lifecycle, ownership, policy and notification.

Good candidates for control-plane operations:

```text
FindDevices
OpenDevice
CreateContext
CreateSurface
CreateBuffer
CreateImage
MapResource
SubmitCommandBuffer
WaitFence
PollFence
DestroyResource
ReportDeviceLost
ReportError
```

Control operations may use:

```text
BMessage
BMessenger
BLooper
BHandler
ports
area IDs
server-side handles
```

This is where Haiku messaging belongs.

## Data plane

The data plane carries high-volume or high-frequency work.

Data-plane objects:

```text
command buffers
vertex/index/uniform buffers
image data
texture uploads
surface images
fence state
mapped areas
```

The data plane should use:

```text
shared memory
mapped areas
validated handles
ring buffers
command buffer memory
DMA-capable memory when available
```

## Bad design

```text
DrawTriangle -> BMessage -> gpu_server
SetVertex    -> BMessage -> gpu_server
BindTexture  -> BMessage -> gpu_server
Present      -> BMessage -> gpu_server
```

This design is easy to understand and easy to kill with overhead.

It causes:

```text
too many context switches
too much serialization
too many copies
too much scheduler overhead
too little batching
poor latency
```

## Good design

```text
Application writes many commands into BGPUCommandBuffer.
Application submits once.
gpu_server validates ownership and schedules work.
GPU or backend runs asynchronously.
Fence reports completion.
```

A command buffer is the unit of GPU work.

## Submission model

Example:

```cpp
BGPUCommandBuffer commands = queue.CreateCommandBuffer();

commands.Begin();
commands.SetRenderTarget(surface);
commands.Clear(BGPUColor(0.02f, 0.03f, 0.05f, 1.0f));
commands.SetVertexBuffer(vertices);
commands.Draw(B_GPU_PRIMITIVE_TRIANGLES, 0, vertexCount);
commands.Present(surface);
commands.End();

BGPUFence fence = queue.Submit(commands);
```

The key is that `Draw()` records commands. It does not synchronously talk to the server for every call.

## Validation

Validation should happen at submission boundaries or resource boundaries, not at every tiny API call if avoidable.

Examples:

```text
resource handle belongs to process/context
buffer usage matches command
surface is alive
image format is supported
command buffer is well-formed
no illegal memory references
queue supports command type
```

Validation is essential because applications do not get direct hardware access.

## Synchronization

GPU-Kit should expose explicit synchronization:

```text
BGPUFence
BGPUEvent
possibly BGPUSemaphore later
```

Initial rules:

```text
Submit is asynchronous.
Wait is explicit.
Polling is possible.
Implicit blocking is considered a bug unless documented.
```

Bad:

```cpp
queue.Submit(commands); // secretly blocks until GPU idle
```

Good:

```cpp
BGPUFence fence = queue.Submit(commands);
// application continues
fence.Wait(timeout);
```

## Zero-copy preference

The system should prefer direct sharing of validated buffers over copying data between app, server and backend.

Copying is allowed when needed for safety or hardware constraints, but it must be visible as an architectural cost.

Rule:

```text
Every copy must justify itself.
```

## Server as traffic controller

`gpu_server` should act as:

```text
owner registry
permission checker
scheduler
surface coordinator
error dispatcher
```

It should not become:

```text
software renderer
copy hub
draw interpreter
global lock
```

If the server becomes the hot path bottleneck, GPU-Kit has failed its own purpose.

## Performance test

A good early test:

```text
Can an application submit 10,000 trivial command buffers without pathological CPU cost?
Can one command buffer contain thousands of draw/blit operations without IPC per operation?
Can four surfaces present independently without blocking each other?
```

This should be measurable from the beginning.
