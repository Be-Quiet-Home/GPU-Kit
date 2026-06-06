# Risks and Red Team review

This project is attractive because the idea is clean. Clean ideas can still fail.

This document lists failure modes.

## Risk 1: The architecture becomes too beautiful and too abstract

A GPU API can look elegant while ignoring hardware realities.

Danger signs:

```text
no memory alignment rules
no explicit synchronization
no ownership model
no device-loss story
no limits
no debug tooling
```

Mitigation:

```text
start with memory, queue, command buffer, fence, surface
keep API close to real primitives
build trace tools early
```

## Risk 2: gpu_server becomes the bottleneck

A central server is Haiku-like, but it can become a performance trap.

Danger signs:

```text
server copies vertex data
server parses every draw at runtime
server serializes all clients globally
server blocks on every submit
```

Mitigation:

```text
shared memory command buffers
submission batching
per-context queues where possible
explicit fences
instrumentation from Phase 2 onward
```

## Risk 3: BMessage is used in the hot path

Messaging is idiomatic but too expensive for per-draw operations.

Mitigation:

```text
BMessage for control plane
command buffers for data plane
document this as a hard rule
```

## Risk 4: Mesa/Vulkan/OpenGL sneaks in through the back door

The project may start native and slowly become a wrapper around foreign concepts.

Danger signs:

```text
public API exposes Vulkan handles
public API requires OpenGL-like state machine
surface model mimics foreign WSI
Mesa becomes required to understand native app code
```

Mitigation:

```text
compatibility layers live above GPU-Kit
foreign concepts stay in backend/research folders
architecture reviews reject leakage
```

## Risk 5: Reference hardware is not as simple as expected

One GPU reduces scope but does not remove complexity.

Remaining problems:

```text
firmware versions
PCIe initialization
interrupts
memory mapping
power management
resets
display output
driver crashes
```

Mitigation:

```text
software backend first
trace backend
hardware milestones extremely small
no OpenGL until native primitives work
```

## Risk 6: Security model is underestimated

A GPU can access memory. Applications must not be trusted.

Questions:

```text
Who owns each buffer?
Can one team access another team's surface?
How are handles validated?
Can malformed commands escape validation?
What happens if GPU hangs?
```

Mitigation:

```text
server-owned handles
validated resource tables
no raw hardware addresses exposed
device reset plan
clear error states
```

## Risk 7: No one writes native GPU-Kit applications

A beautiful native API may have no ecosystem.

Mitigation:

```text
small demos
OpenGL compatibility later
SDL backend later
Media Kit bridge
clear documentation
show visible multimedia advantage
```

## Risk 8: Project becomes too large emotionally

The vision is broad. The implementation must be tiny at first.

Mitigation:

```text
Phase 0 documentation
Phase 1 skeleton
Phase 2 null backend
Phase 3 software backend
only then hardware
```

## Risk 9: The first demo chases the wrong target

A spinning cube may impress less than a BeOS-style media demo.

Mitigation:

```text
first philosophical demo:
  multiple surfaces
  scaling
  present
  responsive desktop
```

## Risk 10: Hidden software fallback masks failure

If unsupported features silently fall back to CPU, the architecture becomes dishonest.

Mitigation:

```text
capability reporting
debug output
explicit fallback mode
gpu_info tool
```

## Risk 11: app_server integration is harder than rendering

Showing pixels inside Haiku windows may be harder than producing pixels.

Mitigation:

```text
surface model designed early
app_server integration documented separately
software backend tests window behavior before hardware
```

## Risk 12: The project becomes anti-Mesa ideology

Mesa is a valuable research mine. Rejecting its public shape does not mean rejecting its knowledge.

Mitigation:

```text
vendor/research notes
study formats, compiler ideas, driver constraints
no purity tests
Haiku form first, external knowledge welcome
```

## Red Team summary

The idea survives only if it stays disciplined:

```text
small first
measured hot path
explicit capabilities
no hardware access from apps
native surface model
foreign APIs as adapters
```

The project fails if it becomes either:

```text
a purity manifesto with no prototype
```

or

```text
another foreign graphics stack in Haiku clothing
```
