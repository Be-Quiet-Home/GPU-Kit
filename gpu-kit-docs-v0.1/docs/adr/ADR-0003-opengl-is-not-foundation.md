# ADR-0003: OpenGL is compatibility, not foundation

## Status

Proposed.

## Context

OpenGL compatibility is useful for existing software, games and demos. However, OpenGL's state-machine architecture should not determine the native Haiku GPU model.

## Decision

GPU-Kit will not expose OpenGL as its foundation.

OpenGL support, if implemented, will be a compatibility layer above GPU-Kit.

## Consequences

Positive:

```text
native API can be smaller and cleaner
OpenGL cannot force old assumptions into GPU-Kit
compatibility remains possible
```

Negative:

```text
OpenGL software will not work until a compatibility layer exists
mapping OpenGL to GPU-Kit may be difficult
```

## Rule

If an API decision only exists to satisfy OpenGL, it belongs in the OpenGL compatibility layer unless it also serves native GPU-Kit semantics.
