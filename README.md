# The Meta-Forge

> Mobile RPG idle game where **weapons are the protagonist**. Forge unique weapons by combining materials and a strategic crafting minigame, then battle them in asynchronous PvP against other players' builds.

**Engine** Godot 4.6 · **Language** GDScript · **Platforms** iOS · Android · Web (HTML5) · **Status** Pre-production

---

## The Pitch

Como Forge Master, **AND ALSO** cada arma tiene un ADN único no repetible que emerge de la combinación de materiales + ejecución del minijuego de forja. Vos no apretás botones durante la batalla: vos **diseñás** el arma, y el arma pelea sola contra snapshots de otros jugadores. El meta del servidor lo construye la comunidad descubriendo qué combos ganan contra qué arquetipos de defensa.

**Core Fantasy** — Ser el herrero legendario cuyas armas definen el meta del servidor.

## Game Pillars

| # | Pillar | Design test |
|---|---|---|
| 1 | **Estética es el primer impacto** | Entre 5 armas feas vs 3 hermosas → elegimos las 3 hermosas |
| 2 | **Variabilidad emergente** | Si una mecánica es divertida pero determinista → agregamos variabilidad o la cortamos |
| 3 | **Sesión corta, profundidad infinita** | Si una feature requiere >5 min sin stopping point → se rediseña o se corta |
| 4 | **Meta emergente, no impuesto** | Si queremos dar "build recomendada" al jugador → la cortamos |

**Anti-pillars** — NO combate en tiempo real · NO pay-to-win absoluto · NO narrativa lineal obligatoria · NO PvP sincrónico

## Visual Identity

> **Chrono Revival (HD-2D Pixel)** — "Every asset must read as a frame from a 1999 JRPG cutscene that survived into 2026 with its soul intact but its resolution tripled."

**Three principles**: Weighted Pigment (painterly color, not PBR) · Legible Silhouette at Thumbnail (100px readable) · Kinetic Honesty (anticipation + overshoot + settle)

**Architecture** — Volcanic Romanesque (Byzantine foundry + Romanesque ecclesiastical)

**Primary palette**

| Color | Hex | Role |
|---|---|---|
| 🟧 Forge Ember | `#C4501A` | Heat, fire element, critical warning |
| 🟫 Anvil Iron | `#4A4060` | Neutral UI ground, shadow base |
| 🟨 Dawn Brass | `#D4962A` | Legendary rarity, victory |
| 🟦 Cold Quench | `#2A5F8C` | Water/ice element, rare rarity |
| ⬜ Ashen Bone | `#E8D9B8` | Text, cooled metal, UI surface |
| 🟩 Verdant Temper | `#3D7A4A` | Earth element, common rarity |
| ⬛ Void Seam | `#1A1228` | Deep shadows, frames, corruption |

Full visual specification in [`design/art/art-bible.md`](design/art/art-bible.md).

## Project Roadmap

```
Tier 0 — Prototype     ○  4-6 weeks       │ Validate: "is the forge minigame fun?"
Tier 1 — MVP           ○  3-4 months      │ Full loop + PvP async basic + soft-launch
Tier 2 — Launch        ○  6-9 months      │ Ligas, accessibility, F2P balanced, public launch
Tier 3 — Live Service  ○  9-18 months     │ Guilds, dungeons, seasonal events, battle pass
```

## Pre-Production Status

| | Step | Status |
|---|---|---|
| ✓ | Game concept ([`game-concept.md`](design/gdd/game-concept.md)) | Done |
| ✓ | Engine setup (Godot 4.6 + GDScript) | Done |
| ✓ | Art bible — 9 sections ([`art-bible.md`](design/art/art-bible.md)) | Done |
| ✓ | Systems index — 40 systems ([`systems-index.md`](design/gdd/systems-index.md)) | Done |
| ○ | Individual GDDs | 0 / 23 MVP |
| ○ | Master architecture (`docs/architecture/`) | Pending |
| ○ | Pre-production gate check | Pending |
| ○ | Tier 0 prototype | Pending |

## Systems Map

40 systems mapped — full breakdown in [`systems-index.md`](design/gdd/systems-index.md).

| Layer | MVP | Vertical Slice | Full Vision |
|---|:---:|:---:|:---:|
| Foundation (Config, Save, Audio, VFX, Input, Scene, Net) | 7 | — | — |
| Core (Material, Weapon, Affix, Hero, Enemy, Combat, Economy, Progression) | 8 | — | — |
| Feature (Forge, Replay, PvP Async, Matchmaking) | 4 | 5 | — |
| Presentation (Hub HUD, Forge UI, Inventory, Roster, Replay, Result, Menus) | 4 | 2 | — |
| Polish (Battle pass, Cosmetics, Guilds, Dungeons, Events) | — | 5 | 5 |

## Project Structure

```
RPG-mobile/
├── design/
│   ├── gdd/
│   │   ├── game-concept.md      ← The "what and why"
│   │   ├── systems-index.md     ← The "how it decomposes"
│   │   └── [individual GDDs]    ← Pending
│   └── art/
│       └── art-bible.md         ← The "how it looks"
├── docs/
│   └── engine-reference/godot/  ← Godot 4.6 API reference (version-pinned)
├── production/
│   ├── session-state/active.md  ← Resume from here
│   └── review-mode.txt          ← lean
├── src/                         ← Game code (empty — nothing built yet)
├── assets/                      ← Game assets (empty)
├── tests/                       ← Test suites (empty)
└── .claude/                     ← Framework: skills, agents, hooks
```

## How to Resume Work

When opening a new Claude Code session:

1. **Read** [`production/session-state/active.md`](production/session-state/active.md) — full state checkpoint with current task, files modified, decisions made, and next steps.
2. **Or run** `/start` — guided onboarding will detect the project state and route you correctly.

## Workflow Used (Pre-Production Pipeline)

This project follows the Claude Code Game Studios pre-production pipeline:

```
/brainstorm            →  Game concept (done)
/setup-engine          →  Engine + tech stack (done)
/art-bible             →  Visual identity (done)
/map-systems           →  Systems decomposition (done)
/design-system         →  Individual GDDs (next — start with `data-driven-config`)
/create-architecture   →  Master technical blueprint
/prototype forge-core  →  Tier 0 prototype (validate core hypothesis)
/gate-check pre-production → Ready to enter production
```

**Review mode**: `lean` (per-skill director gates skipped; enforced at `/gate-check` transitions).

## Credits

Built using the [Claude Code Game Studios](https://github.com/Donchitos/Claude-Code-Game-Studios) framework — framework documentation in [`FRAMEWORK.md`](FRAMEWORK.md).
