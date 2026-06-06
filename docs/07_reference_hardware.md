# Reference hardware

## Principle

GPU-Kit does not begin by supporting every graphics card.

It begins by proving a native Haiku GPU model on a narrow, explicit reference target.

```text
A working reference path is better than a universal promise.
```

## Why a reference GPU

Haiku is a small project. Broad hardware support multiplies complexity:

```text
firmware differences
driver differences
memory models
command formats
power management
display quirks
reset behavior
debugging complexity
```

A reference GPU limits the problem.

The goal is not market coverage. The goal is architectural proof.

## Candidate class

A practical first hardware target should be:

```text
low power
low profile if possible
available used or new
no external power connector
modern enough for an open or partially open driver path
not expensive
not a monster workstation card
```

A small Nvidia Turing+ card is a plausible candidate class because current experimental Haiku Nvidia work and Nvidia's open kernel module path target Turing and newer generations. Exact hardware support must be verified before purchase or development.

## Example reference profile

```text
CPU: x86_64 Haiku system
Machine: small desktop such as ThinkCentre-class hardware
GPU: small Nvidia Turing+ low-profile card
VRAM: enough for surfaces and simple tests
Power: no auxiliary PCIe power
Goal: first surface, first blit, first command buffer, first fence
```

## Why not define hardware too late

If hardware is left open, the API may become vague.

If hardware is chosen too early as public identity, the project may become vendor-locked in the wrong way.

GPU-Kit should therefore distinguish:

```text
public GPU-Kit model:
  vendor-neutral Haiku objects

first backend:
  vendor-specific reference implementation
```

## HAL later

Do not start with a large hardware abstraction layer.

Start with one real backend and let the internal abstraction grow from actual needs.

Bad:

```text
design universal HAL first
implement nothing
```

Good:

```text
software backend
trace backend
one hardware backend
derive internal interfaces from what was learned
```

## Software backend is still useful

Before hardware:

```text
software_backend
```

can prove:

```text
API shape
surface behavior
command recording
fence semantics
debug tooling
media demo flow
```

It cannot prove GPU performance, but it can prevent architecture mistakes before driver work begins.

## Hardware milestones

For a real GPU backend:

```text
1. detect device
2. initialize backend
3. allocate safe memory
4. create command queue
5. submit no-op command
6. signal fence
7. clear image
8. present image
9. blit image
10. draw simple geometry
```

Do not jump directly to OpenGL.

## Purchasing rule

Do not buy hardware because it is fast.

Buy hardware because it is:

```text
documentable
repeatable
cheap enough
small enough
close to existing research
debuggable
```

## Future hardware

After the reference path works, additional backends may be explored.

They must conform to GPU-Kit, not the other way around.
