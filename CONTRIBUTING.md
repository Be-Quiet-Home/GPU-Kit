# Contributing

GPU-Kit is in concept phase. Contributions should preserve the architecture before expanding the scope.

## Project rules

1. Do not expose foreign graphics stack concepts as required public GPU-Kit concepts.
2. Keep OpenGL, Vulkan, SDL and Mesa as research, backend or compatibility material.
3. Do not put BMessage in the rendering hot path.
4. Prefer command buffers, shared memory and explicit fences.
5. Keep capabilities explicit.
6. Avoid hidden fallbacks.
7. Document every major design decision as an ADR.
8. Start small.

## Before adding code

Ask:

```text
Does this make the native Haiku model clearer?
Does it keep apps away from direct GPU access?
Does it preserve the control/data plane split?
Does it avoid accidental Mesa/OpenGL/Vulkan leakage?
Can it be tested with null or software backend?
```

## Recommended contribution types

During early phases, useful contributions include:

```text
documentation reviews
terminology improvements
API sketch criticism
Haiku-specific integration notes
software backend experiments
trace/debug tooling
research notes
small demos
```

## Research notes

When studying another project, add a note under `docs/research/` using this structure:

```markdown
# Research note: <topic>

## Source

## What problem does it solve?

## Useful concepts

## Concepts to reject

## Haiku translation

## Open questions
```

## Style

Use direct language. Prefer small APIs. Prefer explicit constraints. Do not hide uncertainty.

GPU-Kit should read like Haiku: clear, native, responsive.
