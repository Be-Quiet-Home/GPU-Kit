# ADR-0002: Start with one reference GPU family

## Status

Proposed.

## Context

Supporting every GPU family is unrealistic for an early Haiku GPU-Kit project. A broad hardware promise would force the design toward a large HAL and delay proof of the core concept.

## Decision

GPU-Kit will initially target:

```text
null backend
software backend
trace backend
one reference hardware backend
```

The first hardware backend may focus on a narrow Nvidia Turing+ class if that remains the most practical research path.

## Consequences

Positive:

```text
bounded problem
repeatable target
faster learning
less false abstraction
```

Negative:

```text
limited user base
hardware-specific work
risk of overfitting internal backend to one vendor
```

## Guardrail

The public API remains vendor-neutral. Vendor-specific details remain behind the backend boundary.
