# Public API draft

This document is a sketch, not a stable API.

The purpose is to name the objects and force architectural discussion.

## Naming

All public names are tentative.

Preferred prefix:

```text
BGPU*
```

Reason: the lower layer is broader than 3D. It covers images, surfaces, copies, blits, presentation, video interop and later 3D. A future `B3D*` layer may sit above `BGPU*`.

## Core objects

### BGPUDevice

Represents a usable GPU-Kit device.

```cpp
class BGPUDevice {
public:
    status_t GetInfo(BGPUDeviceInfo* info) const;
    status_t GetCapabilities(BGPUCapabilities* capabilities) const;

    status_t CreateBuffer(const BGPUBufferDescriptor& descriptor,
        BGPUBuffer* outBuffer);

    status_t CreateImage(const BGPUImageDescriptor& descriptor,
        BGPUImage* outImage);

    status_t CreateQueue(const BGPUQueueDescriptor& descriptor,
        BGPUQueue* outQueue);

    bool Supports(BGPUFeature feature) const;
};
```

### BGPUDeviceInfo

Static descriptive information.

```cpp
struct BGPUDeviceInfo {
    BString name;
    BString vendorName;
    uint32 vendorID;
    uint32 deviceID;
    uint32 driverVersion;
    bool software;
};
```

### BGPUCapabilities

Explicit feature and limit reporting.

```cpp
struct BGPUCapabilities {
    uint64 features;
    uint32 maxTextureWidth;
    uint32 maxTextureHeight;
    uint32 maxCommandBufferSize;
    uint32 maxColorAttachments;
    uint32 queueFlags;
    uint64 deviceLocalMemory;
    uint64 hostVisibleMemory;
};
```

### BGPUBuffer

A typed memory allocation.

```cpp
class BGPUBuffer {
public:
    size_t Size() const;
    uint32 Usage() const;

    status_t Map(uint32 access, void** outAddress);
    status_t Unmap();

    status_t GetHandle(BGPUHandle* outHandle) const;
};
```

Buffer usage flags:

```cpp
enum BGPUBufferUsage {
    B_GPU_BUFFER_VERTEX       = 1 << 0,
    B_GPU_BUFFER_INDEX        = 1 << 1,
    B_GPU_BUFFER_UNIFORM      = 1 << 2,
    B_GPU_BUFFER_TRANSFER_SRC = 1 << 3,
    B_GPU_BUFFER_TRANSFER_DST = 1 << 4,
    B_GPU_BUFFER_COMMAND      = 1 << 5
};
```

### BGPUImage

Represents image or texture memory.

```cpp
class BGPUImage {
public:
    uint32 Width() const;
    uint32 Height() const;
    BGPUFormat Format() const;
    uint32 Usage() const;
};
```

Image usage flags:

```cpp
enum BGPUImageUsage {
    B_GPU_IMAGE_SAMPLED        = 1 << 0,
    B_GPU_IMAGE_RENDER_TARGET  = 1 << 1,
    B_GPU_IMAGE_TRANSFER_SRC   = 1 << 2,
    B_GPU_IMAGE_TRANSFER_DST   = 1 << 3,
    B_GPU_IMAGE_PRESENTABLE    = 1 << 4,
    B_GPU_IMAGE_VIDEO_FRAME    = 1 << 5
};
```

### BGPUSurface

A presentation target.

A surface may be attached to:

```text
BWindow
BView
app_server surface
offscreen image
video consumer
recorder
```

Initial sketch:

```cpp
class BGPUSurface {
public:
    status_t AttachToWindow(BWindow* window);
    status_t AttachToView(BView* view);

    status_t Resize(uint32 width, uint32 height);
    status_t GetCurrentImage(BGPUImage* outImage);
};
```

The final API must respect Haiku threading and window locking rules.

### BGPUQueue

A queue receives submitted command buffers.

```cpp
class BGPUQueue {
public:
    status_t CreateCommandBuffer(BGPUCommandBuffer* outCommands);
    status_t Submit(const BGPUCommandBuffer& commands, BGPUFence* outFence);
    status_t WaitIdle(bigtime_t timeout);
};
```

Queue types:

```cpp
enum BGPUQueueType {
    B_GPU_QUEUE_GRAPHICS,
    B_GPU_QUEUE_TRANSFER,
    B_GPU_QUEUE_COMPUTE,
    B_GPU_QUEUE_PRESENT
};
```

Not every device exposes every queue.

### BGPUCommandBuffer

Records work.

```cpp
class BGPUCommandBuffer {
public:
    status_t Begin();
    status_t End();
    status_t Reset();

    status_t ClearImage(const BGPUImage& image, BGPUColor color);
    status_t CopyBuffer(const BGPUBuffer& src, const BGPUBuffer& dst,
        const BGPUCopyRegion& region);
    status_t CopyImage(const BGPUImage& src, const BGPUImage& dst,
        const BGPUImageCopyRegion& region);
    status_t BlitImage(const BGPUImage& src, const BGPUImage& dst,
        const BGPUBlitRegion& region);

    status_t SetRenderTarget(const BGPUSurface& surface);
    status_t SetVertexBuffer(uint32 slot, const BGPUBuffer& buffer);
    status_t Draw(uint32 vertexCount, uint32 firstVertex);

    status_t Present(const BGPUSurface& surface);
};
```

Initial command set should be deliberately small:

```text
Clear
CopyBuffer
CopyImage
BlitImage
Present
DrawFixedTriangle or minimal Draw
```

### BGPUFence

Signals completion.

```cpp
class BGPUFence {
public:
    status_t Wait(bigtime_t timeout);
    bool IsSignaled() const;
    status_t Reset();
};
```

## Example: first frame

```cpp
class DemoWindow : public BWindow {
public:
    DemoWindow()
        : BWindow(BRect(100, 100, 740, 460), "GPU-Kit Demo",
            B_TITLED_WINDOW, B_ASYNCHRONOUS_CONTROLS)
    {
        Show();
    }
};

int
main()
{
    BApplication app("application/x-vnd.gpukit-demo");

    BGPUDevice device;
    BGPU::OpenDefaultDevice(&device);

    DemoWindow* window = new DemoWindow();

    BGPUSurface surface;
    surface.AttachToWindow(window);

    BGPUQueue queue;
    device.CreateQueue({ B_GPU_QUEUE_GRAPHICS }, &queue);

    BGPUCommandBuffer commands;
    queue.CreateCommandBuffer(&commands);

    commands.Begin();
    commands.SetRenderTarget(surface);
    commands.ClearImage(surface.CurrentImage(), BGPUColor(0.02f, 0.03f, 0.05f, 1.0f));
    commands.Present(surface);
    commands.End();

    BGPUFence fence;
    queue.Submit(commands, &fence);

    app.Run();
    return 0;
}
```

This is illustrative only. It ignores lifetime, thread-safety and actual Haiku window-locking details.

## Design rule

If an API call exposes a foreign concept such as EGL display, Vulkan surface, DRM file descriptor, GBM buffer or GL context as a required native concept, it should be rejected from the core API.

Such concepts belong in compatibility layers or backend internals.
