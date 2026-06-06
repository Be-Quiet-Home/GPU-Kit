# GPU-Kit documentation index

GPU-Kit is documented as an architecture first and as code second. This is intentional. The main risk is not that the first code will be imperfect. The main risk is that early code accidentally imports the shape of a foreign graphics stack and forces Haiku to adapt to it.

## Reading order

1. [Manifest](00_manifest.md)
2. [Architecture overview](01_architecture_overview.md)
3. [Control plane and data plane](02_control_plane_data_plane.md)
4. [Public API draft](03_public_api_draft.md)
5. [Capabilities](04_capabilities.md)
6. [Surface, stream and media model](05_surface_stream_media_model.md)
7. [OpenGL as compatibility layer](06_opengl_as_compatibility_layer.md)
8. [Reference hardware](07_reference_hardware.md)
9. [Roadmap and milestones](08_roadmap_milestones.md)
10. [Risks and Red Team review](09_risks_red_team.md)
11. [Research mines](10_research_mines.md)
12. [Glossary](11_glossary.md)

## Architecture Decision Records

- [ADR-0001: GPU-Kit is a native Haiku kit, not a Mesa/OpenGL/Vulkan frontend](adr/ADR-0001-native-gpu-kit.md)
- [ADR-0002: Start with one reference GPU family](adr/ADR-0002-reference-gpu-first.md)
- [ADR-0003: OpenGL is compatibility, not foundation](adr/ADR-0003-opengl-is-not-foundation.md)
