# Research mines

This project uses the term "research mine" deliberately.

A research mine is a place to extract knowledge from. It is not a place to copy architecture from uncritically.

## Rules for research mines

```text
Read.
Understand.
Extract concepts.
Translate into Haiku terms.
Do not let the source define the public API.
```

## Mesa

Useful for:

```text
format knowledge
driver architecture experience
shader compiler ideas
hardware workarounds
OpenGL compatibility behavior
Vulkan driver experience
```

Danger:

```text
Mesa becomes the architecture
Haiku adapts to Mesa instead of Mesa being adapted to Haiku
```

GPU-Kit stance:

```text
Mesa is a knowledge source and possible internal component.
Mesa is not the public GPU-Kit model.
```

## OpenGL

Useful for:

```text
compatibility with existing applications
understanding old 3D state models
mapping legacy concepts
```

Danger:

```text
OpenGL state machine defines GPU-Kit
native API becomes OpenGL with different names
```

GPU-Kit stance:

```text
OpenGL belongs above GPU-Kit.
```

## Vulkan

Useful for:

```text
modern GPU concepts
explicit synchronization
queues
command buffers
memory visibility
capability reporting
```

Danger:

```text
GPU-Kit becomes a Vulkan wrapper
Haiku exposes foreign WSI concepts
native API becomes too large
```

GPU-Kit stance:

```text
Vulkan is a vocabulary mine, not the native language.
```

## SDL

Useful for:

```text
portability bridge
future game compatibility
input/window/audio backend examples
```

Danger:

```text
SDL becomes the way Haiku applications think about windows, rendering and input
```

GPU-Kit stance:

```text
SDL may get a GPU-Kit backend later.
SDL does not define GPU-Kit.
```

## VideoStreams

Useful for:

```text
producer/consumer model
buffer exchange
swap chain thinking
compositor/screen/recorder consumers
Media Kit-like 3D acceleration discussion
```

Danger:

```text
starts from OpenGL/Vulkan producers
solves rendered-buffer distribution more than native GPU command model
```

GPU-Kit stance:

```text
VideoStreams is useful for the surface/stream layer.
It is not the GPU-Kit core.
```

Suggested repository placement:

```text
vendor/VideoStreams
docs/research/videostreams-notes.md
```

## Haiku Media Kit

Useful for:

```text
media buffer culture
stream processing
node-based thinking
audio/video system integration
```

Danger:

```text
copying Media Kit mechanics where GPU command submission needs different primitives
```

GPU-Kit stance:

```text
Learn from Media Kit's system integration.
Do not force GPU work into an audio/video node model if it does not fit.
```

## BeOS demonstrations

Useful for:

```text
system feeling
responsiveness target
multimedia as natural state
demo design
```

Danger:

```text
nostalgia replaces engineering
```

GPU-Kit stance:

```text
The feeling is the target.
The implementation must be modern.
```

## Nvidia open kernel modules and Haiku Nvidia experiments

Useful for:

```text
reference hardware path
Turing+ research
firmware and backend learning
real hardware experiments
```

Danger:

```text
vendor-specific internal design leaks into public API
```

GPU-Kit stance:

```text
A vendor-specific backend is acceptable.
A vendor-specific public API is not.
```

## Research notes format

Each research item should be documented as:

```text
source
what it solves
concepts worth extracting
concepts to reject
possible GPU-Kit translation
open questions
```

Template:

```markdown
# Research note: <topic>

## Source

## What problem does it solve?

## Useful concepts

## Concepts to reject

## Haiku translation

## Open questions
```
