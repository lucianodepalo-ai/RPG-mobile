# Active Session State

*Last updated: 2026-04-13*

## Current Task

Pre-production — systems decomposition complete. Ready to author individual system GDDs.

## Progress Checklist

- [x] `/start` — onboarding (review mode: lean)
- [x] `/brainstorm open` — game concept authored
- [x] `/setup-engine` — Godot 4.6 + GDScript configured
- [x] `/art-bible` — 9 sections complete
- [x] `/map-systems` — 40 systems enumerated, dependencies mapped, priorities assigned
- [ ] `/design-system` — author individual GDDs in design order (MVP Foundation first)
- [ ] `/prototype forge-core` — Tier 0 prototype to validate core hypothesis
- [ ] `/create-architecture` — master architecture blueprint after MVP GDDs
- [ ] `/gate-check pre-production`

## Key Files

- [design/gdd/game-concept.md](../../design/gdd/game-concept.md) — The Meta-Forge concept
- [design/art/art-bible.md](../../design/art/art-bible.md) — Visual identity spec (9 sections)
- [design/gdd/systems-index.md](../../design/gdd/systems-index.md) — 40 systems mapped
- [CLAUDE.md](../../CLAUDE.md) — engine configured (Godot 4.6 + GDScript)
- [.claude/docs/technical-preferences.md](../../.claude/docs/technical-preferences.md) — perf budgets, naming, specialists

## Key Decisions Made

- **Engine**: Godot 4.6 + GDScript (Web + Mobile export)
- **Visual direction**: Chrono Revival (HD-2D Pixel) — Weighted Pigment / Legible Silhouette / Kinetic Honesty
- **Architecture tradition**: Volcanic Romanesque (Byzantine foundry + Romanesque ecclesiastical)
- **Typography**: Sans-serif primary for body; pixel stylized serif display-only (UX conflict resolution)
- **Timing indicator for Forge**: Diegetic-only (visual identity over accessibility overlay)
- **Character atlases**: 2 atlases per character to preserve 64×128 source size
- **VFX**: Flipbook preferred over GPU particles (4 emitters × 150 particles hard limit)
- **Player avatar**: Blacksmith does NOT appear as character — only "master's mark" seal stamp in tournament screens
- **PvP telegraphing**: Geometry-only (plate coverage / limb mass / cloth volume → blunt-weak / slash-weak / pierce-weak)

## Next Steps (Design Order for GDD Authoring)

Per [systems-index.md](../../design/gdd/systems-index.md):

**MVP Foundation**:
1. Data-Driven Config
2. Save/Load & Persistence
3. Scene Management
4. Input System
5. Audio System
6. VFX Framework
7. Networking Layer

**MVP Core** (8-15): Material → Weapon Entity → Affix → Hero/Wielder → Enemy → Combat Simulation → Economy → Player Progression.

**MVP Feature** (16-19): Forge System → Replay → PvP Async → Matchmaking basic.

**MVP Presentation** (20-23): Hub HUD → Forge UI → Material/Weapon UIs → PvP Replay/Victory/Meta menus.

## High-Risk Systems Flagged

1. **Forge System** — core hypothesis; prototype Tier 0 before full GDD
2. **Combat Simulation** — determinism cross-platform; fixed-point math from day 1
3. **Weapon Entity System** — schema bottleneck; lock before feature work
4. **Matchmaking build power** — anti-P2W formula; test 3 variants
5. **Networking Layer** — API shape constrains many systems

## Open Questions

- Backend stack: serverless (Supabase/Firebase) vs dedicated? → pending ADR
- Monetización F2P detallada (battle pass economics, cosmetic pricing) → pending ADR
- Simulación determinista: fixed-point lib a usar (GDScript native o GDExtension)? → pending ADR

## Review Mode

`lean` — Director gates skipped for per-skill reviews; enforced at `/gate-check` transitions.
