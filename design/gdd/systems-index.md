# Systems Index: The Meta-Forge

> **Status**: Draft
> **Created**: 2026-04-13
> **Last Updated**: 2026-04-13
> **Source Concept**: [design/gdd/game-concept.md](game-concept.md)

---

## Overview

**The Meta-Forge** es un mobile RPG idle donde las armas son el protagonista: el jugador forja espadas combinando materiales y ejecutando un minijuego estratégico, luego las manda a PvP asíncrono contra builds de otros jugadores.

El scope técnico se caracteriza por: **data-driven** en todo (sistemas de materiales, fórmulas, afijos como resources), **combat simulation determinista headless** (para PvP asíncrono cross-platform iOS/Android/Web), **networking ligero cliente-servidor** (snapshot submission, no estado en tiempo real compartido), y **pipeline HD-2D pixel** (3 parallax layers max mobile, atlas 2048×2048 hard cap). La arquitectura debe soportar sesiones cortas (15-30 min/día) con meta-game profundo, respetando los pilares: **Estética primer impacto**, **Variabilidad emergente**, **Sesión corta profundidad infinita**, **Meta emergente no impuesto**.

**40 sistemas identificados** distribuidos en 5 tiers (MVP: 23, Vertical Slice: 12, Full Vision: 5).

---

## Systems Enumeration

| # | System Name | Category | Priority | Status | Design Doc | Depends On |
|---|-------------|----------|----------|--------|------------|------------|
| 1 | Material System | Gameplay | MVP | Not Started | — | Data-Driven Config, Save/Load |
| 2 | Forge System | Gameplay | MVP | Not Started | — | Material, Weapon Entity, Affix, Audio, VFX, Input |
| 3 | Affix/Mutation System | Gameplay | MVP | Not Started | — | Data-Driven Config, Weapon Entity |
| 4 | Weapon Entity System | Core | MVP | Not Started | — | Data-Driven Config, Save/Load, Material |
| 5 | Hero/Wielder System | Gameplay | MVP | Not Started | — | Data-Driven Config, Save/Load, Weapon Entity |
| 6 | Enemy System | Gameplay | MVP | Not Started | — | Data-Driven Config (data consumed by Combat Sim) |
| 7 | Combat Simulation | Core | MVP | Not Started | — | Weapon Entity, Hero, Enemy |
| 8 | Replay System | Gameplay | MVP | Not Started | — | Combat Simulation |
| 9 | PvP Async System | Gameplay | MVP | Not Started | — | Combat Sim, Replay, Networking, Save/Load |
| 10 | Matchmaking System | Gameplay | MVP (basic) / VS (refined) | Not Started | — | PvP Async, Weapon Entity, Networking |
| 11 | Player Progression | Progression | MVP | Not Started | — | Data-Driven Config, Save/Load |
| 12 | Liga/Rankings System | Progression | Vertical Slice | Not Started | — | Matchmaking, Save/Load, Player Progression |
| 13 | Economy System | Economy | MVP | Not Started | — | Data-Driven Config, Save/Load |
| 14 | Idle/Offline Progression | Progression | Vertical Slice | Not Started | — | Save/Load, Material, Economy |
| 15 | Recipe Discovery | Progression | Vertical Slice | Not Started | — | Forge System, Save/Load |
| 16 | Collection/Signature (inferred) | Progression | Vertical Slice | Not Started | — | Weapon Entity, Save/Load |
| 17 | Data-Driven Config (inferred) | Core | MVP | Designed | [data-driven-config.md](data-driven-config.md) | — |
| 18 | Save/Load & Persistence (inferred) | Persistence | MVP (local) / VS (cloud) | Not Started | — | — |
| 19 | Networking Layer | Core | MVP | Not Started | — | — |
| 20 | Anti-Cheat Validation (inferred) | Core | Vertical Slice | Not Started | — | Networking, Combat Simulation |
| 21 | Audio System (inferred) | Audio | MVP | Not Started | — | — |
| 22 | VFX Framework (inferred) | Core | MVP | Not Started | — | — |
| 23 | Input System (inferred) | Core | MVP | Not Started | — | — |
| 24 | Scene Management (inferred) | Core | MVP | Not Started | — | — |
| 25 | Analytics/Telemetry (inferred) | Meta | Vertical Slice | Not Started | — | Networking |
| 26 | Localization (inferred) | Meta | Vertical Slice | Not Started | — | — |
| 27 | Hub HUD | UI | MVP | Not Started | — | Economy, Hero, Material, Player Progression |
| 28 | Forge Minigame UI | UI | MVP | Not Started | — | Forge System |
| 29 | Material Inventory UI | UI | MVP | Not Started | — | Material System |
| 30 | Weapon Roster UI | UI | MVP | Not Started | — | Weapon Entity, Hero |
| 31 | PvP Replay Viewer UI | UI | MVP | Not Started | — | Replay System, PvP Async |
| 32 | Victory/Defeat Screens | UI | MVP | Not Started | — | PvP Async, Player Progression |
| 33 | Meta Menus | UI | MVP (basic) / VS (expanded) | Not Started | — | All systems (low coupling) |
| 34 | Tutorial/Onboarding UI (inferred) | UI | Vertical Slice | Not Started | — | Forge UI, Material UI, Weapon Roster UI |
| 35 | Accessibility Suite (inferred) | Meta | Vertical Slice | Not Started | — | All UI systems |
| 36 | Battle Pass System (inferred) | Economy | Full Vision | Not Started | — | Economy, Player Progression |
| 37 | Cosmetic/Skin System (inferred) | Economy | Full Vision | Not Started | — | Weapon Entity, Economy |
| 38 | Guild/Clan System (inferred) | Gameplay | Full Vision | Not Started | — | Networking, Player Progression, Save/Load (cloud) |
| 39 | Dungeon Cooperativo (inferred) | Gameplay | Full Vision | Not Started | — | Combat Sim, Networking, Guild |
| 40 | Seasonal Events (inferred) | Meta | Full Vision | Not Started | — | Materials, Economy, Collection |

---

## Categories

| Category | Description | Systems in this game |
|----------|-------------|----------------------|
| **Core** | Foundation systems everything depends on | Data-Driven Config, Save/Load, Networking, VFX Framework, Input, Scene Mgmt, Weapon Entity, Combat Sim, Anti-Cheat |
| **Gameplay** | The systems that make the game fun | Material, Forge, Affix, Hero/Wielder, Enemy, Replay, PvP Async, Matchmaking, Guild, Dungeon |
| **Progression** | How the player grows over time | Player Progression, Liga/Rankings, Idle/Offline, Recipe Discovery, Collection |
| **Economy** | Resource creation and consumption | Economy, Battle Pass, Cosmetic/Skin |
| **Persistence** | Save state and continuity | Save/Load & Persistence (local + cloud) |
| **UI** | Player-facing information displays | Hub HUD, Forge UI, Material Inventory, Weapon Roster, PvP Replay Viewer, Victory/Defeat, Meta Menus, Tutorial |
| **Audio** | Sound and music systems | Audio System |
| **Meta** | Systems outside the core game loop | Analytics, Localization, Accessibility, Seasonal Events |

Categoría **Narrative** se omite — el concept define explícitamente que NO hay narrativa lineal obligatoria. El lore existe como condimento ambiental en descripciones de items, no requiere sistema dedicado.

---

## Priority Tiers

| Tier | Definition | Target Milestone | Count |
|------|------------|------------------|-------|
| **MVP (Tier 1)** | Required para validar core hypothesis "forjar es divertido + PvP async engancha" | Soft-launch 1-2 países | **23** |
| **Vertical Slice (Tier 2)** | Launch público completo con ligas, retención idle, accesibilidad | Launch global | **12** |
| **Full Vision (Tier 3)** | Live-service post-launch | 9-18 meses post-launch | **5** |

---

## Dependency Map

### Foundation Layer (no dependencies)

1. **Data-Driven Config** — Godot `.tres` Resources para materiales, afijos, fórmulas. Base de todo lo data-driven.
2. **Save/Load & Persistence** — serialización de estado completo. Local MVP, cloud Tier 2.
3. **Networking Layer** — REST client, auth, session management.
4. **Audio System** — music player + SFX bus con ducking.
5. **VFX Framework** — flipbook controller + límites de particle systems (4 emitters × 150 particles, del art bible §8.5).
6. **Input System** — touch primary + mouse fallback web, left/right-handed toggle.
7. **Scene Management** — transiciones + streaming de atlases 2048×2048.
8. **Analytics/Telemetry** — event tracking (para Tier 2, stub en MVP).
9. **Localization** — string tables, planning para RTL (para Tier 2, passthrough en MVP).

### Core Layer (depends on Foundation)

1. **Material System** — depends on: Data-Driven Config, Save/Load
2. **Weapon Entity System** — depends on: Data-Driven Config, Save/Load, Material *(bottleneck: 7+ sistemas dependen de la estructura del arma)*
3. **Affix/Mutation System** — depends on: Data-Driven Config, Weapon Entity
4. **Hero/Wielder System** — depends on: Data-Driven Config, Save/Load, Weapon Entity
5. **Enemy System** — depends on: Data-Driven Config (datos estáticos consumidos por Combat Sim)
6. **Combat Simulation** — depends on: Weapon Entity, Hero, Enemy *(bottleneck: Replay, Enemy behavior, PvP, Anti-Cheat, Dungeon dependen de esta)*
7. **Economy System** — depends on: Data-Driven Config, Save/Load
8. **Player Progression** — depends on: Data-Driven Config, Save/Load

### Feature Layer (depends on Core)

1. **Forge System** — depends on: Material, Weapon Entity, Affix, Audio, VFX, Input *(el core loop)*
2. **Replay System** — depends on: Combat Simulation
3. **PvP Async System** — depends on: Combat Sim, Replay, Networking, Save/Load
4. **Matchmaking System** — depends on: PvP Async, Weapon Entity (para build power rating), Networking
5. **Liga/Rankings System** — depends on: Matchmaking, Save/Load, Player Progression *(VS)*
6. **Idle/Offline Progression** — depends on: Save/Load, Material, Economy *(VS)*
7. **Recipe Discovery** — depends on: Forge System, Save/Load *(VS)*
8. **Anti-Cheat Validation** — depends on: Networking, Combat Simulation *(VS)*
9. **Collection/Signature** — depends on: Weapon Entity, Save/Load *(VS)*

### Presentation Layer (depends on features + core)

1. **Hub HUD** — depends on: Economy, Hero/Wielder, Material, Player Progression
2. **Forge Minigame UI** — depends on: Forge System
3. **Material Inventory UI** — depends on: Material System
4. **Weapon Roster UI** — depends on: Weapon Entity, Hero/Wielder
5. **PvP Replay Viewer UI** — depends on: Replay System, PvP Async
6. **Victory/Defeat Screens** — depends on: PvP Async, Player Progression
7. **Meta Menus** — depends on: many (low coupling)
8. **Tutorial/Onboarding UI** — depends on: Forge UI, Material UI, Weapon Roster UI *(VS)*
9. **Accessibility Suite** — depends on: todas las UI *(VS)*

### Polish / Late Layer

1. **Battle Pass System** — depends on: Economy, Player Progression *(FV)*
2. **Cosmetic/Skin System** — depends on: Weapon Entity, Economy *(FV)*
3. **Guild/Clan System** — depends on: Networking, Player Progression, Save/Load cloud *(FV)*
4. **Dungeon Cooperativo** — depends on: Combat Sim, Networking, Guild *(FV)*
5. **Seasonal Events** — depends on: Materials, Economy, Collection *(FV)*

---

## Recommended Design Order

GDDs se escriben en este orden. Sistemas independientes en la misma layer pueden paralelizarse.

### MVP — Foundation

| Order | System | Priority | Layer | Agent(s) | Est. Effort | Why |
|-------|--------|----------|-------|----------|-------------|-----|
| 1 | Data-Driven Config | MVP | Foundation | technical-director + godot-specialist | S | Base de todo lo data-driven; define el schema de Resources |
| 2 | Save/Load & Persistence | MVP | Foundation | godot-specialist | M | Serialización del estado del jugador (armas forjadas, materiales, progreso) |
| 3 | Scene Management | MVP | Foundation | godot-specialist | S | Transiciones de escena con streaming de atlases del art bible |
| 4 | Input System | MVP | Foundation | ui-programmer + ux-designer | S | Touch-first; respeta la zona de reach del art bible §7.7 |
| 5 | Audio System | MVP | Foundation | audio-director + sound-designer | M | Pilar "Estética primer impacto" exige mix con ducking adaptativo |
| 6 | VFX Framework | MVP | Foundation | technical-artist + godot-specialist | M | Flipbook controller + límites de particles (art bible §8.5) — crítico para el forge juice |
| 7 | Networking Layer | MVP | Foundation | network-programmer | L | Bottleneck — define API para PvP/Matchmaking/Anti-cheat. Diseño correcto acá ahorra refactor después |

### MVP — Core

| Order | System | Priority | Layer | Agent(s) | Est. Effort | Why |
|-------|--------|----------|-------|----------|-------------|-----|
| 8 | Material System | MVP | Core | systems-designer + game-designer | M | Primer sistema de gameplay — define el espacio combinatorio del core loop |
| 9 | Weapon Entity System | MVP | Core | systems-designer + game-designer | L | "Las armas son el protagonista" — schema ADN es la identidad del juego. **Bottleneck** — 7+ sistemas dependen |
| 10 | Affix/Mutation System | MVP | Core | systems-designer + economy-designer | M | El eje de "variabilidad emergente" (pilar 2); define el RNG acotado |
| 11 | Hero/Wielder System | MVP | Core | game-designer | S | 1 archetype MVP (Melee Brute). Datos mínimos; los wielders son subordinados al weapon |
| 12 | Enemy System | MVP | Core | ai-programmer + game-designer | S | PvE farm pool; enemy silhouettes telegrafían weapon weakness (art bible §5.3) |
| 13 | Combat Simulation | MVP | Core | systems-designer + gameplay-programmer | L | **Bottleneck crítico** — determinismo cross-platform (iOS/Android/Web mismo resultado). Fórmulas aquí definen el meta |
| 14 | Economy System | MVP | Core | economy-designer | M | Gold faucets/sinks, pilar "anti P2W" se decide acá |
| 15 | Player Progression | MVP | Core | economy-designer + game-designer | M | XP, unlocks, curva que sostiene "10 min diarios + 10 hrs profundidad" (pilar 3) |

### MVP — Feature

| Order | System | Priority | Layer | Agent(s) | Est. Effort | Why |
|-------|--------|----------|-------|----------|-------------|-----|
| 16 | **Forge System** | MVP | Feature | game-designer + systems-designer | L | **EL core loop**. Minijuego estratégico con timing + materiales → afijo. Si no engancha, el juego muere |
| 17 | Replay System | MVP | Feature | gameplay-programmer | M | Graba output determinista del Combat Sim; el "teatro" del PvP async |
| 18 | PvP Async System | MVP | Feature | network-programmer + game-designer | L | Submit/receive snapshots; defense queue; el hook de retención del concept |
| 19 | Matchmaking (basic) | MVP | Feature | systems-designer | M | MVP: rating simple por nivel/poder. VS: refinamiento por "poder de build" (pilar 4) |

### MVP — Presentation (UI)

| Order | System | Priority | Layer | Agent(s) | Est. Effort | Why |
|-------|--------|----------|-------|----------|-------------|-----|
| 20 | Hub HUD | MVP | Presentation | ui-programmer + ux-designer | M | Persistente; pilar 3 "sesión corta" exige instantly legible on re-open |
| 21 | Forge Minigame UI | MVP | Presentation | ui-programmer + ux-designer | L | Diegetic timing (art bible §7.5); heat gauge diegetic; el reto UX más complejo del juego |
| 22 | Material Inventory UI + Weapon Roster UI | MVP | Presentation | ui-programmer | M | Paralelo — ambos son list browsers. Silhouette readability a 100px |
| 23 | PvP Replay Viewer UI + Victory/Defeat + Meta Menus (basic) | MVP | Presentation | ui-programmer + ux-designer | M | Paralelo — escenas post-combat + settings básico |

### Vertical Slice (24-35)

Orden sugerido, paralelizable en gran parte: Enemy expandido → Liga/Rankings → Idle/Offline Progression → Recipe Discovery → Anti-Cheat → Analytics → Localization → Tutorial → Accessibility Suite → Collection/Signature → Matchmaking refinado → Save/Load cloud.

### Full Vision (36-40)

Battle Pass → Cosmetic/Skin → Guild/Clan → Seasonal Events → Dungeon Cooperativo.

**Effort estimates**: S = 1 session, M = 2-3 sessions, L = 4+ sessions.

---

## Circular Dependencies

**None found** — el graph es acíclico.

Verificación realizada:
- Enemy depende de Config (para stats), no de Combat Sim. Combat Sim consume Enemy (data), no lo define.
- Replay consume output de Combat Sim pero no lo afecta.
- PvP Async consume Replay pero no lo define.
- Matchmaking consume Weapon Entity (para build power) pero no lo define.

---

## High-Risk Systems

Sistemas que requieren prototipado temprano independientemente de su tier.

| System | Risk Type | Risk Description | Mitigation |
|--------|-----------|-----------------|------------|
| **Forge System** | Design | Core hypothesis del juego: si el minijuego de forja no engancha a los 5 min, todo lo demás es irrelevante | Prototipo Tier 0 (4-6 semanas) aislado con 5 materiales y minigame minimal. Testers externos antes de invertir en PvP. |
| **Combat Simulation** | Technical | Determinismo cross-platform (iOS/Android/Web deben producir mismo resultado). Floating point no-deterministic es el killer. | Spike técnico en el prototipo: usar fixed-point math lib desde día 1. Test cross-platform con seeds conocidas. |
| **Weapon Entity System** | Design | Schema del ADN define todo el juego; cambios tardíos cascada a 7+ sistemas | Diseño iterativo en prototipo; lock-in del schema ANTES de feature work |
| **Matchmaking (build power)** | Design | "Poder de build" es fórmula crítica para pilar "anti P2W"; si está mal, el meta se pudre | Spike en Vertical Slice: probar 3 fórmulas distintas con data real de soft-launch |
| **Networking Layer** | Technical | API design blindará/limitará muchos sistemas; backend choice (serverless vs dedicado) afecta scope | ADR temprano sobre backend stack; prototipo con stub local antes de invertir en backend real |
| **Art Production (indirectly)** | Scope | El pipeline HD-2D pixel requiere un artista pixel bottleneck para Tier 1+ | Art bible §8 ya restringió el scope artístico (2048 atlas, modular sprites); evaluar AI tooling para variaciones |

---

## Progress Tracker

| Metric | Count |
|--------|-------|
| Total systems identified | 40 |
| Design docs started | 1 |
| Design docs reviewed | 0 |
| Design docs approved | 0 |
| MVP systems designed | 1 / 23 |
| Vertical Slice systems designed | 0 / 12 |
| Full Vision systems designed | 0 / 5 |

---

## Next Steps

- [x] Review and approve this systems enumeration
- [ ] Design MVP-tier Foundation systems (1-7) first — start with `/design-system data-driven-config`
- [ ] Design MVP-tier Core systems (8-15)
- [ ] Design MVP-tier Feature systems (16-19) — **prototype Forge System as Tier 0 before full GDD**
- [ ] Design MVP-tier Presentation systems (20-23)
- [ ] Run `/design-review` on each completed GDD
- [ ] Run `/review-all-gdds` when MVP GDDs are complete (cross-consistency)
- [ ] Run `/gate-check pre-production` when all MVP GDDs are approved
- [ ] Prototype Forge System EARLY (`/prototype forge-core`) — risk mitigation
