# GPU-Kit

A native GPU Kit for Haiku OS.

> Multimedia is Haiku’s natural state.

GPU-Kit is an architectural research project for a native Haiku GPU subsystem. Its goal is not to bolt Mesa, OpenGL, Vulkan or SDL onto Haiku as the public shape of graphics acceleration. Its goal is to define a Haiku-native way to treat the GPU as a protected coprocessor service.

Applications do not talk to the GPU. Applications talk to GPU-Kit. GPU-Kit talks to a system service and driver backend. Compatibility layers such as OpenGL or SDL may be built on top of GPU-Kit later, but they must not define the architecture.

## Core idea

```text
Haiku application
  ↓
GPU-Kit C++ API
  ↓
gpu_server
  ↓
Haiku GPU backend / driver
  ↓
reference GPU
```

The GPU is treated as a black box with deterministic, validated interfaces. Applications submit work. The system owns access, memory, surfaces, scheduling, synchronization and presentation.

## Why this exists

Haiku should not become a Linux graphics stack clone. Haiku already has a strong architectural language: kits, servers, messages, responsiveness, media streams, app_server integration and small native APIs. GPU acceleration should be assimilated into that language.

The project starts from a BeOS/Haiku principle:

```text
Foreign systems may provide experience.
Haiku provides the form.
```

Mesa, Vulkan, OpenGL, SDL and VideoStreams are useful research mines. They are not the public truth of GPU-Kit.

## Non-goals

GPU-Kit does not try to:

- support all graphics cards at the beginning;
- replace Mesa for every existing program;
- expose Vulkan as the native Haiku API;
- expose OpenGL as the foundation;
- let applications access GPU hardware directly;
- become a generic cross-platform rendering library;
- reproduce Linux DRM/KMS/GBM/EGL/Wayland as public Haiku concepts.

## Initial goals

The first useful target is not a game or an OpenGL demo. The first target is system behavior:

```text
fast startup
first frame quickly visible
zero avoidable copies
asynchronous command submission
explicit capabilities
system-managed surfaces
clean error paths
```

A minimal proof of concept may begin with a software backend. The API and data model should be tested before entering hardware driver complexity.

## Documentation

Start here:

- [Manifest](docs/00_manifest.md)
- [Architecture overview](docs/01_architecture_overview.md)
- [Control plane and data plane](docs/02_control_plane_data_plane.md)
- [Public API draft](docs/03_public_api_draft.md)
- [Capabilities](docs/04_capabilities.md)
- [Surface, stream and media model](docs/05_surface_stream_media_model.md)
- [OpenGL as compatibility layer](docs/06_opengl_as_compatibility_layer.md)
- [Reference hardware](docs/07_reference_hardware.md)
- [Roadmap](docs/08_roadmap_milestones.md)
- [Risks and Red Team review](docs/09_risks_red_team.md)
- [Research mines](docs/10_research_mines.md)
- [Glossary](docs/11_glossary.md)

Architecture decisions are recorded in [docs/adr](docs/adr).

## Status

Concept phase. No stable API. No driver implementation. No promises.

The purpose of this repository is to make the idea precise enough that it can be discussed, criticized, reduced and eventually prototyped.
