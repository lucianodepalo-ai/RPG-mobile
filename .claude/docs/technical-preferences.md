# Technical Preferences

<!-- Populated by /setup-engine. Updated as the user makes decisions throughout development. -->
<!-- All agents reference this file for project-specific standards and conventions. -->

## Engine & Language

- **Engine**: Godot 4.6
- **Language**: GDScript
- **Rendering**: Godot 2D renderer (HD-2D pixel art with parallax layers); Forward+ available for future 3D effects if needed
- **Physics**: Godot 2D physics (no es core del loop — el combate es logic-driven, no physics-based)

## Input & Platform

- **Target Platforms**: Mobile (iOS, Android), Web (HTML5)
- **Input Methods**: Touch, Mouse (web fallback)
- **Primary Input**: Touch
- **Gamepad Support**: None
- **Touch Support**: Full
- **Platform Notes**:
  - Portrait mode obligatorio.
  - Todas las interacciones touch-first; en Web el mouse emula touch.
  - Respetar safe areas iOS (notch, home indicator) y Android (display cutouts).
  - UI escalable para rango de resoluciones mobile: 360×640 (mínimo) → 430×932 (iPhone Pro Max) → tablets en formato portrait.
  - Target para Web: build HTML5 < 15 MB inicial (load rápido en 3G/4G); considerar asset streaming.

## Naming Conventions

- **Classes**: PascalCase — `ForgeController`, `WeaponArchetype`
- **Variables**: snake_case — `move_speed`, `material_count`
- **Signals/Events**: snake_case past tense — `weapon_forged`, `material_added`, `health_changed`
- **Files**: snake_case matching class — `forge_controller.gd`
- **Scenes/Prefabs**: PascalCase matching root node — `ForgeController.tscn`
- **Constants**: UPPER_SNAKE_CASE — `MAX_MATERIALS_PER_FORGE`, `DEFAULT_FORGE_DURATION`

## Performance Budgets

- **Target Framerate**: 60 fps (mobile high-end) / 30 fps (mobile low-end fallback)
- **Frame Budget**: 16.6 ms @ 60 fps / 33.3 ms @ 30 fps
- **Draw Calls**: < 200 por frame en mobile (Godot 2D renderer)
- **Memory Ceiling**: 512 MB en mobile; < 100 MB en Web HTML5 RAM footprint
- **Build Size**:
  - Mobile APK / IPA: target < 80 MB inicial, con asset streaming para contenido adicional
  - Web HTML5: target < 15 MB inicial (load rápido); resto streaming por loader tipo Addressables

## Testing

- **Framework**: gdUnit4 (Godot 4 — community-maintained, activo, integración CLI)
- **Minimum Coverage**: Por story type (ver `.claude/docs/coding-standards.md`):
  - Logic (formulas, combate sim, AI): **BLOCKING** — test unitario obligatorio
  - Integration (multi-system): **BLOCKING** — integration test o playtest documentado
  - Visual/Feel: **ADVISORY** — screenshot + sign-off
  - UI: **ADVISORY** — walkthrough doc o interaction test
  - Config/Data (balance tuning): **ADVISORY** — smoke check
- **Required Tests**: Fórmulas de combate, sistemas de forja (combinación materiales → stats), matchmaking por poder de build, simulación determinista cross-platform

## Forbidden Patterns

- [None configured yet — add as architectural decisions are made]

## Allowed Libraries / Addons

- [None configured yet — add as dependencies are approved]

## Architecture Decisions Log

- [No ADRs yet — use /architecture-decision to create one]

## Engine Specialists

- **Primary**: godot-specialist
- **Language/Code Specialist**: godot-gdscript-specialist (all .gd files)
- **Shader Specialist**: godot-shader-specialist (.gdshader files, VisualShader resources)
- **UI Specialist**: godot-specialist (no dedicated UI specialist — primary covers all UI)
- **Additional Specialists**: godot-gdextension-specialist (GDExtension / native C++ bindings only)
- **Routing Notes**: Invoke primary for architecture decisions, ADR validation, and cross-cutting code review. Invoke GDScript specialist for code quality, signal architecture, static typing enforcement, and GDScript idioms. Invoke shader specialist for material design and shader code. Invoke GDExtension specialist only when native extensions are involved.

### File Extension Routing

| File Extension / Type | Specialist to Spawn |
|-----------------------|---------------------|
| Game code (.gd files) | godot-gdscript-specialist |
| Shader / material files (.gdshader, VisualShader) | godot-shader-specialist |
| UI / screen files (Control nodes, CanvasLayer) | godot-specialist |
| Scene / prefab / level files (.tscn, .tres) | godot-specialist |
| Native extension / plugin files (.gdextension, C++) | godot-gdextension-specialist |
| General architecture review | godot-specialist |
