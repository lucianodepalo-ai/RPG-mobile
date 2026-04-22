# Active Session State

*Last updated: 2026-04-22 (GDD #4 Forge System COMPLETED — all 11 sections written)*

## Current Task

**Camino B (prototype-first) ALL 4 STEPS COMPLETED ✓.**

GDD #1 of 4: **Data-Driven Config** — DESIGNED ✓
GDD #2 of 4: **Material System** — DESIGNED ✓
GDD #3 of 4: **Weapon Entity System** — DESIGNED ✓ (1388 lines, 11 sections, 0 placeholders)
GDD #4 of 4: **Forge System** — DESIGNED ✓ (896 lines, 11 sections, 0 placeholders)
- File: `design/gdd/forge-system.md`
- Sections: Overview ✓ Player Fantasy ✓ Detailed Design (12 rules + 5-state FSM + 12 interactions) ✓ Formulas (6: D.1–D.6) ✓ Edge Cases (30) ✓ Dependencies (9 upstream + 7 downstream) ✓ Tuning Knobs (30 + 5 LOCKED) ✓ Acceptance Criteria (28 GWT) ✓ + Visual/Audio ✓ + UI ✓ + Open Questions (10) ✓
- Registry updated: 6 formulas + 10 constants + ForgeConfig resource + 3 signals + 4 referenced_by updates
- Player Fantasy: "The Duet that settles into a seal" (C3 primary + C2 graft)
- Anti-spam model: counter+time (n + grace window + recovery), floor 45, free count 3
- Grade split: 80 Duet / 20 Seal (LOCKED)
- Systems-index updated: 4/23 MVP designed
- File: `design/gdd/weapon-entity-system.md`
- All sections complete: A ✓ B ✓ C ✓ D ✓ E ✓ F ✓ G ✓ H ✓ + Visual/Audio ✓ + UI ✓ + Open Questions ✓
- Registry updated: WeaponArchetypeDefinition + WeaponInstance + 6 formulas + 9 constants + MaterialDefinition schema extensions requested
- systems-index updated: marked Designed, progress 3/23 MVP
- **Schema extensions requested to Material System** (non-breaking append, apply on next retrofit):
  1. `stat_bonuses: Dictionary[StringName, int]` — per_mille bonus per stat_axis
  2. `damage_type_affinity: StringName` — slash/pierce/blunt/&""
- **10 Open Questions flagged** for downstream GDDs (Save/Load migration, AffixSystem signature, Combat Sim determinism, Matchmaking weights, Tier 2 Vault, Cosmetic pipeline, Anti-spam forge, Signature moderation, ORPHANED UX, PvP dict schema drift)

**Current**: GDD #4 of 4 — `/design-system forge-system` IN PROGRESS
- File: `design/gdd/forge-system.md` (skeleton created, 13 sections pending)
- Review mode: `lean`
- Section in progress: **A — Overview**
- Caveman mode: OFF for this skill (GDDs require full prose)

**Provisional contracts flagged** (4 undesigned upstream deps):
1. AffixSystem.roll_affixes(seed, slot_count, slot_tiers, rarity) — Affix GDD pending
2. Audio event bus (forge_strike_played, forge_heat_changed, forge_reveal_played) — Audio GDD pending
3. VFXFramework.trigger_flipbook / emit_particles (4×150 cap) — VFX GDD pending
4. InputSystem.get_timestamped_touch (≤50ms precision) — Input GDD pending

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
