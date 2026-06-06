# Glossary

## GPU-Kit

The proposed native Haiku kit for GPU access as a protected system service.

## GPU as black box

The principle that applications do not manipulate GPU hardware directly. They submit validated work through system objects.

## Coprocessor

A processor-like device that performs work for the CPU. In GPU-Kit, the GPU is treated as a protected coprocessor service.

## Control plane

The slower coordination path for lifecycle, ownership, policy, messages and notifications.

Examples:

```text
create device
create context
create buffer
submit command buffer
wait for fence
```

## Data plane

The high-volume path for command buffers, images, vertex data and other performance-sensitive data.

## Command buffer

A recorded list of GPU or backend commands submitted as a unit.

## Fence

A synchronization object that signals when submitted work has completed.

## Surface

A target for presentation or image exchange. It may be attached to a window, view, compositor, offscreen target or media consumer.

## Image

Pixel storage used for textures, render targets, video frames or presentation.

## Buffer

Linear memory used for vertex data, index data, uniform data, command data or transfers.

## Queue

A submission channel for work. Some devices may have graphics, transfer, compute or present queues.

## Capability

A reported feature, limit or supported format of a device or surface.

## Reference GPU

The first deliberately chosen hardware target used to prove the architecture.

## Backend

Internal implementation behind GPU-Kit. Examples: null backend, software backend, Nvidia backend.

## gpu_server

The proposed Haiku system service that owns GPU resources, validates access, coordinates surfaces and routes submissions.

## Compatibility layer

A layer that maps foreign APIs such as OpenGL or SDL onto GPU-Kit.

## Research mine

A project, API or codebase studied for concepts and knowledge without adopting its architecture as the public model.

## Flange

Informal term for bolting a foreign subsystem onto Haiku without assimilating it into Haiku's native system model.

## Assimilation

The process of understanding a foreign idea and expressing it in native Haiku terms.

## Hot path

The performance-critical path executed frequently during rendering or presentation. The hot path must avoid unnecessary IPC, copies and blocking.

## First frame

The first visible output after application startup. GPU-Kit should make this fast and predictable.
