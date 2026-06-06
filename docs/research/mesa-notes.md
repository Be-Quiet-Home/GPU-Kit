# Research note: Mesa

## What problem does it solve?

Mesa implements major graphics APIs and drivers, including OpenGL and Vulkan paths on supported systems. It contains a large body of practical knowledge about formats, shaders, driver behavior and GPU limitations.

## Useful concepts

```text
format tables
shader compiler pipelines
driver/backend separation
capability reporting
hardware workaround culture
OpenGL compatibility behavior
```

## Concepts to reject or avoid

```text
Mesa as public Haiku API
Linux DRM assumptions as public Haiku concepts
OpenGL/Vulkan as required native application model
```

## Haiku translation

Mesa may inform:

```text
BGPUFormat
BGPUCapabilities
shader strategy
OpenGL compatibility layer
backend implementation details
```

Mesa must not define:

```text
GPU-Kit public API
Haiku surface model
application-visible device model
```

## Open questions

```text
Can parts of Mesa be used internally without importing its public architecture?
Which Mesa concepts map cleanly to BGPU objects?
Where does shader compilation belong?
```
