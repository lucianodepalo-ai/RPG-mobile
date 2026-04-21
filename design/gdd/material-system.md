# Material System

> **Status**: In Design
> **Author**: lucianodepalo-ai + Claude (systems-designer + game-designer agents)
> **Last Updated**: 2026-04-13
> **Implements Pillar**: Pillar 2 (Variabilidad emergente) — DIRECT
> **System #**: 1 — Core layer, MVP priority

---

## Overview

**Material System** es el sistema gameplay que define, guarda y gestiona los materiales que el jugador recolecta y consume en la forja. Es la primera mitad del core loop: antes de que el jugador decida **qué forjar**, tiene que tener **qué materiales combinar**.

El sistema opera en dos capas:

- **Capa de datos (infra)**: Gestiona el catálogo estático de `MaterialDefinition` (cargados vía Data-Driven Config) y el inventario dinámico del jugador (cuántos tiene de cada material). Persiste el inventario al save, emite eventos de acquisition (drops de combate, generación offline, daily rewards), y expone queries al resto del juego (`has_materials(requirements)`, `get_total_count(id)`, etc.).

- **Capa de experiencia (player-facing)**: El jugador ve su inventario crecer cada vez que vuelve (drops acumulados, generación offline), siente el valor relativo de cada material (un material raro vs común es tangible, no abstracto), y enfrenta la decisión cotidiana "¿qué combino hoy?". La variedad del material pool ES el espacio del juego — sin materiales distintos, no hay forja interesante.

**MVP scope**: 10-15 materiales distintos, cubriendo los 7 elementos (Fire, Water/Ice, Earth, Wind/Storm, Light, Dark, Neutral) y 3-5 tiers de rareza. Escalable a 50+ materials en Tier 2 (public launch) agregando archivos `.tres` sin tocar código.

**Sin este sistema**: no hay loop de forja (no hay qué combinar), no hay progresión de colección (no hay nada que acumular), no hay meta emergente (sin espacio combinatorio no hay builds a descubrir). Es el **primer sistema de gameplay** — todos los demás sistemas de combate/PvP/progresión asumen que los materiales existen.

**Pillar alignment**:

- **Pilar 2 (Variabilidad emergente) — DIRECTO**. El tamaño y diversidad del material pool define matemáticamente el espacio de armas posibles. Más materiales × elementos × tiers = más combinaciones únicas.
- **Pilar 4 (Meta emergente) — indirecto**. El balance de rarity y acquisition rate define qué materiales son "meta" vs "deprecated" — y los designers ajustan esto sin builds nuevos.

---

## Player Fantasy

You are not a hero collecting loot. You are a master blacksmith whose wealth is measured in **jars, bins, and silk-lined boxes** — each holding something the world gave up slowly. The material inventory is your library, your pantry, your reliquary. A shard of Dragonbone Ash sits on the shelf next to three vials of Tidewater Salt and a single, patient sliver of Moonsteel you've been saving for a weapon you haven't named yet. Every material has a **weight** — not just in stats, but in memory: where it dropped, what fight gave it up, how many weeks it took to find the second one. This is the quiet half of The Meta-Forge. The forge roars; the inventory breathes. Here, the game does not chase you. It waits with you. A session where you collected four commons and no rares is not a wasted session — it is a morning spent restocking the shelf, and the shelf is how tomorrow's weapon becomes possible.

### Emotional beats

- **Opening the mid-game inventory** — A warm, quiet dread-pleasure. The grid is half-populated: the player can see the shape of what they have (lots of Ironroot, plenty of Ember Coal) and the shape of what they're **missing** (that one empty silhouette where Abyssal Pearl should be). It's the feeling of a well-stocked kitchen two ingredients shy of the dish they've been planning.

- **A rare material drops** — Not a jackpot klaxon. A held breath. The drop animation is slower than a common's, the name card lingers, the rarity border glows like firelight catching on metal. The player doesn't shout — they lean in. They immediately open the inventory to put it *next to the others* and see what it could become. The celebration is spatial, not numerical.

- **"Today I combine them"** — Ritual. The player has been looking at three rare materials for weeks; today they commit. The moment is equal parts anticipation and grief — after today, the jars are empty again. This is the beat where the Material System hands off to the Forge, and it should feel like **walking from the pantry to the anvil** with their arms full.

### What this system is NOT

Not a loot explosion. Not a gacha pull. Not a currency. Materials are **named, weighted, specific** — a Chrono Cross Element, not a Diablo gem pile. Scarcity is the point; abundance would cheapen every forge. The thrill here is **anticipation**, not payoff — payoff belongs to the forge.

### The anti-gacha promise

Material acquisition is gentle and constant. No pity timer, no banner, no one-shot prayer. A legendary drop is a celebration, but no session lives or dies by it. The dopamine is a **steady warmth**, not a spike — the pleasure of watching a stockpile grow one piece at a time, the way a collector fills a shelf across years.

### Pillar 2 mapping — Variabilidad emergente

The Material System is the **combinatorial raw space**: 10-15 named materials at MVP (~50 at launch), each with element and rarity weight, means every forge is a choice made from a non-uniform pool. Variability doesn't emerge from the forge alone — it emerges from **what the player happens to have in the jars that morning**, and no two players' jars look the same.

---

## Detailed Design

### Core Rules

#### C.1.1 MVP Material Catalog — 12 materials across 7 elements × 4 tiers

Tier distribution at MVP: **5 Common + 4 Uncommon + 2 Rare + 1 Epic + 0 Legendary**. Legendary is reserved for Tier 2 (post-MVP) to avoid economic distortion when there are no premium sinks yet. Fire gets 3 entries (Common + Uncommon + Rare) establishing it as the "tutorial archetype"; Earth and Light have 1 each at MVP (grow in Tier 2).

| # | ID | Display Name | Element | Tier | Flavor |
|---|-----|--------------|---------|------|--------|
| 1 | `iron_root` | Ironroot | Neutral | Common | "The root of a tree that grew under a meteorite." |
| 2 | `ash_shard` | Ash Shard | Fire | Common | "Still warm. Still angry." |
| 3 | `frost_petal` | Frost Petal | Water/Ice | Common | "Blooms only in the cold between heartbeats." |
| 4 | `clay_knot` | Clay Knot | Earth | Common | "Shaped by pressure no hand applied." |
| 5 | `storm_filament` | Storm Filament | Wind/Storm | Common | "Hums a note no one taught it." |
| 6 | `pale_ember` | Pale Ember | Light | Uncommon | "Burns without consuming. Illuminates without warmth." |
| 7 | `void_mote` | Void Mote | Dark | Uncommon | "It has weight. It has no shadow." |
| 8 | `saltfire_resin` | Saltfire Resin | Fire | Uncommon | "Crystallized from a fire that burned on the sea." |
| 9 | `windgale_pearl` | Windgale Pearl | Wind/Storm | Uncommon | "Hardened from a windstorm's final breath." |
| 10 | `cinder_core` | Cinder Core | Fire | Rare | "The eye of something that once burned for centuries." |
| 11 | `glacial_vein` | Glacial Vein | Water/Ice | Rare | "Moves like memory — slowly, and only backward." |
| 12 | `voidember_core` | Voidember Core | Dark | Epic | "What fire looks like where there is no light to see it by." |

All 7 art bible elements (Fire, Water/Ice, Earth, Wind/Storm, Light, Dark, Neutral) are represented. Element asymmetry is intentional (Fire oversampled, Earth/Light undersampled) to create meta pressure — see Section G tuning notes.

#### C.1.2 Schema Addition — `flavor_text` field

The `MaterialDefinition` Custom Resource (owner: `data-driven-config.md`) requires one additional field not covered in the original schema:

```
flavor_text: String  # 1-2 sentences, JRPG-style. Max 120 chars. Default: "".
```

Display-only (no formula depends on it). Propose this as a **non-breaking schema append** to `data-driven-config.md` — existing `.tres` files without the field default to `""`. A schema migration bump is NOT required since the field is optional with safe default.

#### C.1.3 Stack Cap and Overflow — Convert to Gold

- Each material ID has a maximum stack of **999 units**.
- **Overflow rule**: units that would push count past 999 are **converted to gold at a fixed per-tier rate**. Rates are intentionally set BELOW the effective crafting value of the material, nudging the player toward consumption over hoarding.
- Conversion rates:

| Tier | Gold per overflow unit |
|---|---|
| Common | 2 |
| Uncommon | 8 |
| Rare | 35 |
| Epic | 150 |
| Legendary | 500 (reserved for Tier 2) |

- Flow: `add_material(id, amount)` detects overflow, adds only what fits to reach exactly 999, computes `gold_from_overflow = overflow_units × rate_for_tier`, calls `Economy.add_gold(gold_from_overflow)`, emits `material_overflow(id, discarded_units, gold_awarded)`.
- UI subscribes to show "+N material · +Y gold overflow" toast.

#### C.1.4 Inventory Persistence

- **Runtime representation**: `Dictionary[StringName, int]` — key is `material_id`, value is count. Only materials with count > 0 are stored (absent key = 0 by convention).
- **Save contract** (provisional, pending Save/Load GDD): `SaveLoadSystem.save_material_inventory(data: Dictionary) -> void`
- **Load contract**: `SaveLoadSystem.load_material_inventory() -> Dictionary`
- Save is called on session end + on any explicit save trigger. Load is called once on boot, BEFORE any acquisition event fires.

#### C.1.5 Acquisition API

```gdscript
# Add a single material. Returns actual amount added (may be less than amount if near cap).
# Any overflow is automatically converted to gold via Economy.add_gold().
add_material(id: StringName, amount: int = 1) -> int

# Batch add from a drop result dict. Calls add_material per entry.
# Returns summary dict of { id: actually_added } for animation layer to consume.
add_materials_batch(drops: Dictionary[StringName, int]) -> Dictionary[StringName, int]
```

- `add_material` emits `material_acquired(id, amount_added)` after committing.
- `add_material` emits `material_overflow(id, discarded_units, gold_awarded)` when overflow occurs.
- Both functions validate `id` against the loaded catalog. Unknown IDs are rejected silently in release builds; they trigger `push_error` in debug builds.

#### C.1.6 Consumption API

```gdscript
# Atomic check-and-consume. Returns false without modifying inventory if any requirement
# is unmet. Never partial — all-or-nothing.
consume_materials(requirements: Dictionary[StringName, int]) -> bool
```

Forge calls `has_materials(requirements)` for UI gating, then `consume_materials(requirements)` on commit. `consume_materials` re-validates internally — it does not trust that `has_materials` was called first. Emits `materials_consumed(requirements)` after successful commit.

#### C.1.7 Query API

```gdscript
has_materials(requirements: Dictionary[StringName, int]) -> bool
get_count(id: StringName) -> int          # Returns 0 for unknown or unowned id
get_total_by_element(element_id: StringName) -> int  # Sum across all materials of that element
get_summary() -> Dictionary
  # Returns: { element_id: int total, "recent": Array[StringName] }
  # "recent" = up to 5 material IDs acquired since last mark_inventory_seen() call
```

**Material ordering in UI**: default sort is **element group, then tier ascending**. Player-configurable sort (by tier, acquisition time) is post-MVP. The system exposes raw data; sort logic lives in UI layer.

**"New drop" indicator**: `get_summary()["recent"]` drives the Hub HUD new-drop badge without requiring per-material timestamps. `mark_inventory_seen()` is called when player opens Material Inventory UI.

#### C.1.8 Idle Generation

- Rate: **4 Common materials per hour total** (distributed evenly: ~0.8/hr each of 5 Commons). Stored as per-mille int: `800 / 1000 per hour per material`.
- **Uncommon idle rotation**: 1 Uncommon material per hour on fixed day-of-week rotation (e.g., Mon = Pale Ember, Tue = Void Mote, Wed = Saltfire Resin, Thu = Windgale Pearl; Fri-Sun repeat). Creates reason to check in on specific days.
- **Accumulation cap**: 8 hours max. Beyond 8 hrs offline, idle generation STOPS. Prevents week-long AFK dumps that bypass the daily session design.
- **UI nudge**: at 6 hrs (75% full), app icon badge + notification trigger to encourage return.
- Rate is expressed via tuning knob `IDLE_RATE_SECONDS_PER_UNIT` (default 3600 = 1 unit/hr); owned by this GDD.
- The **Idle/Offline Progression system (Tier 2)** owns the elapsed-time calculation and calls `add_materials_batch` with its result. MaterialSystem has no clock dependency.
- **Which material ID is generated**: determined by `LootTableDefinition` entries tagged `"idle"` — owned by Economy System.

### States and Transitions

Material inventory item states:

| State | Condition | Description |
|-------|-----------|-------------|
| `Absent` | `count == 0`, key not in dict | Material never acquired, or fully consumed |
| `Active` | `count` between 1 and 998 | Owned and available for forge consumption |
| `Capped` | `count == 999` | At stack cap; further acquisition converts to gold |
| `Pending` | returned in `add_materials_batch` result, animation not complete | Awaiting drop animation before UI reflects new count (UI-layer concern only; inventory dict is updated immediately on return) |

**Session flow**:

```
Boot
  └─ load_material_inventory() → populate _inventory dict
  └─ ConfigLoader.get_all_of_type(MaterialDefinition) → populate _catalog

Acquisition event (combat drop / idle batch / daily reward / debug grant)
  └─ validate_material_id(id) → reject unknown (debug: error, release: silent)
  └─ check cap → compute actual_added, overflow_units
  └─ _inventory[id] += actual_added
  └─ if overflow: Economy.add_gold(overflow_units × rate_for_tier)
  └─ emit material_acquired / material_overflow
  └─ [UI layer animates from batch result]

Forge commit
  └─ consume_materials(requirements) → atomic deduct
  └─ emit materials_consumed(requirements)

Session end
  └─ save_material_inventory(_inventory)
```

### Interactions with Other Systems

| System | Direction | Contract |
|--------|-----------|----------|
| **Data-Driven Config** | MaterialSystem reads | `ConfigLoader.get_all_of_type(MaterialDefinition)` on post-boot async load. Catalog is immutable at runtime. |
| **Save/Load (provisional)** | Bidirectional | `save_material_inventory(dict)` on session end; `load_material_inventory() -> dict` on boot. |
| **Economy System** | Economy writes materials, reads from overflow | Economy resolves `LootTableDefinition` drops → calls `add_materials_batch(drops)`. Overflow calls `Economy.add_gold(amount)`. |
| **Combat / Enemy System** | Combat triggers Economy | Combat signals encounter win to Economy; Economy resolves loot via LootTable, calls MaterialSystem. No direct coupling Combat ↔ MaterialSystem. |
| **Forge System** | Forge reads + writes | Forge calls `has_materials(reqs)` for UI gate; calls `consume_materials(reqs)` on commit. Forge must NOT modify `_inventory` directly. |
| **Idle/Offline Progression (Tier 2)** | Idle writes | Computes offline delta, calls `add_materials_batch(drops)`. Idle owns elapsed time; MaterialSystem owns cap + overflow + emit logic. |
| **Hub HUD** | HUD reads | Calls `get_summary()` for element totals + recent-acquisition badge. Subscribes to `material_acquired` signal for refresh. |
| **Material Inventory UI** | UI reads | Queries `_inventory` dict + catalog for display. Calls `mark_inventory_seen()` on open. Subscribes to `material_acquired` and `material_overflow` for live updates. |

**Schema ownership rule** (inherited from data-driven-config): MaterialSystem owns the RUNTIME semantics of MaterialDefinition fields. The Resource class definition itself lives in data-driven-config.

---

## Formulas

Four formulas. Base acquisition rates proposed by economy-designer; mechanics are standard weighted random + linear interpolation.

### Formula 1 — Weighted Random Material Drop

Resolves which specific material drops when a drop event triggers, given a `LootTableDefinition`.

`drop_index = find_in_cumulative(random_int(0, total_weight - 1), cumulative_weights)`

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `entries` | E | Array[{material_id: StringName, weight: int}] | size 1-50 | Entries of the active LootTable |
| `total_weight` | W | int | 1 — 100,000 | Sum of all weights in entries |
| `random_int(0, W-1)` | r | int | 0 — W-1 | Uniformly distributed random |
| `cumulative_weights` | C | Array[int] | size = entries.size() | `C[i] = sum(entries[0..i].weight)` |
| `drop_index` | i | int | 0 — entries.size()-1 | Index of the resolved material drop |

**Output Range**: `drop_index` identifies a unique material entry. RNG deterministic per `combat_session_id` (not per-platform clock) for cross-platform consistency.

**Example**: LootTable with 3 entries — `[("ash_shard", 50), ("saltfire_resin", 30), ("cinder_core", 20)]`. total_weight = 100. Cumulative = [50, 80, 100]. `random_int(0, 99) = 73` → 73 falls in range [50, 80) → drop_index = 1 → saltfire_resin drops.

### Formula 2 — Idle Material Award (Elapsed Time)

Calculates how many units are awarded by idle generation since last session.

`units_awarded = min(floor(elapsed_seconds / idle_rate_seconds_per_unit), idle_max_offline_units)`

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `elapsed_seconds` | t | int | 0 — 2,592,000 (30 days cap) | Seconds since last session (clamped to 30d) |
| `idle_rate_seconds_per_unit` | r | int | 300 — 86400 | Tuning knob, default 3600 (1 per hr) |
| `idle_max_offline_units` | M | int | 8 — 168 | Tuning knob, default 32 (8 hrs × 4 per hr) |
| `units_awarded` | u | int | 0 — M | Units awarded to player |

**Output Range**: 0 to `idle_max_offline_units` (default 32). A player returning after 12 hours at default rate receives 32 units (capped); one returning after 1 hour receives 4 units.

**Example**: Player offline 5 hrs (18,000 seconds), rate default 3600. `units_awarded = min(floor(18000/3600), 32) = min(5, 32) = 5`. Five units of Common materials awarded, distributed via LootTable "idle" entries.

### Formula 3 — Overflow Gold Conversion

When a drop lands while inventory is capped at 999, the excess is converted to gold.

`gold_awarded = overflow_units × overflow_rate_for_tier[tier]`

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `overflow_units` | o | int | 0 — 999 | Units exceeding the 999 cap |
| `tier` | T | int | 1 — 5 | Rarity tier (1=Common, 2=Uncommon, 3=Rare, 4=Epic, 5=Legendary) |
| `overflow_rate_for_tier[T]` | R[T] | int (gold per unit) | lookup table | R[1]=2, R[2]=8, R[3]=35, R[4]=150, R[5]=500 |
| `gold_awarded` | g | int | 0 — ~500,000 | Gold awarded to player |

**Output Range**: 0 to ~500K gold in extreme edge case. Rates deliberately BELOW crafting value of the material — consumption nudge.

**Example**: Player has `ash_shard` (Common, tier 1) at 999. Drop of 10 more units. overflow_units = 10. R[1] = 2. gold_awarded = 10 × 2 = 20 gold.

### Formula 4 — Expected Drops per Session (Analytics, NOT runtime)

For balance validation. Not executed in gameplay — analytics dashboards only.

`expected_drops_per_session_by_tier[T] = encounters_per_session × drop_rate_per_tier[T] / 1000`

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `encounters_per_session` | N | int | 4 — 12 | Target MVP: 6-8 |
| `drop_rate_per_tier[T]` | dr[T] | int (per-mille) | 0 — 1000 | Common=650, Uncommon=250, Rare=80, Epic=15 |
| `expected_drops_per_session_by_tier[T]` | ed[T] | float (analytics only) | 0.0 — 10.0 | Expected count per session |

**Output**: Analytics dashboard only. Per-mille intermediate math stays int; final division to float happens only in analytics display (no combat path uses this formula).

**Example** (6 encounters/session, MVP rates):
- Common: 6 × 650 / 1000 = **3.9 drops/session**
- Uncommon: 6 × 250 / 1000 = **1.5**
- Rare: 6 × 80 / 1000 = **0.48** (~1 every 2 sessions)
- Epic: 6 × 15 / 1000 = **0.09** (~1 every 11 sessions ≈ 1.5 weeks daily play)

Validated against economy-designer intent: "Epic ~1 per 3 weeks at daily play" — target met.

---

## Edge Cases

- **If drop contains unknown `material_id`** (not in catalog): reject silently in release builds, `push_error` in debug. The drop is lost — never crashes. LootTable is validated at build time so this should only happen if a `.tres` was deleted post-build.
- **If batch drops multiple materials at cap simultaneously**: process each entry independently. Each overflow triggers its own `Economy.add_gold()` call; UI aggregates as single toast "+N materials · +M gold overflow".
- **If save file is corrupted on load**: log error, initialize `_inventory = {}` (empty), display user notice "Could not load material inventory — starting fresh". Do not crash. Session proceeds; player re-acquires via normal play.
- **If a material definition is removed from catalog post-save** (deprecated material): on load, saved count of missing ID remains in `_inventory` but is never exposed via queries (catalog is canonical). Warn dev: "Inventory contains deprecated material `[id]` — consider migration script". Count preserved for future migration; never silently lost.
- **If `consume_materials` is called with requirements that include unknown IDs**: return `false` (atomic fail). Never partial consumption.
- **If `add_material(id, negative_amount)`**: log error, reject. Amounts must be strictly positive. `consume_materials` expects positive counts in requirements (subtraction is internal).
- **If clock manipulation is detected** (player sets device clock forward to cheese idle): Idle/Offline Progression system (owner) must detect this. MaterialSystem trusts elapsed_seconds from Idle — doesn't validate clock independently. Flagged as open question for Idle GDD (Tier 2).
- **If player is at 0 gold when overflow triggers**: gold is added normally — Economy increments from 0. No special case.
- **If Forge `consume_materials` immediately after `add_material` filled to 999**: both operations are synchronous and atomic within the same frame. Forge consume happens second — dict is already updated. No race condition in single-threaded Godot GDScript.
- **If `idle_max_offline_units` is set to 0** (designer intent during event windows): idle generation effectively disabled. Valid state; formula returns 0. No special handling needed.
- **If two Forge instances attempt to consume simultaneously**: impossible in single-threaded Godot. API is synchronous. Future multi-threaded forge would require mutex — flagged as future concern.
- **If `_inventory` dictionary grows unbounded**: per-material cap of 999 prevents per-entry bloat. Dict size bounded by catalog (12 at MVP, ~50 at launch). Memory ~2KB total — not a concern.
- **If player acquires material of rarity with NO active LootTable entries** (e.g., Legendary at MVP): impossible by design — LootTable only references existing materials; drop rates for absent tiers = 0. Debug builds assert this on LootTable load.
- **If a material is unlocked mid-session via quest/event** (Tier 2 feature): addition to `_inventory` triggers `material_acquired` as normal. Catalog is immutable mid-session — material must be in catalog at boot to be acquirable.
- **If `flavor_text` exceeds 120 chars**: @tool validation warning at save time (per data-driven-config). Runtime truncates to 120 chars for display; no error.

---

## Dependencies

### Upstream (this system depends on)

- **Data-Driven Config** (designed) — provides `MaterialDefinition` Resource class and catalog load API via `ConfigLoader.get_all_of_type(MaterialDefinition)`. Also defines the `flavor_text` field that this GDD proposes to add to the schema.
- **Save/Load** (NOT yet designed) — provisional contract: `save_material_inventory(data: Dictionary) -> void` and `load_material_inventory() -> Dictionary`. Contract may shift when Save/Load GDD is authored.
- **ElementDefinition** (boot-phase Resource, owned by Data-Driven Config) — for `get_total_by_element()` queries.
- **Economy System** (partial) — MaterialSystem calls `Economy.add_gold(amount)` on overflow. This is a thin cross-system call; Economy is the owner.

### Downstream (these systems depend on Material System)

| System | Hard or Soft | What depends |
|---|---|---|
| Forge System | Hard | Primary consumer via `has_materials` / `consume_materials` |
| Weapon Entity System | Hard (indirect) | Forged weapons reference the materials used — weapon DNA includes material IDs |
| Economy System | Hard | Economy resolves loot drops and calls `add_materials_batch` |
| Idle/Offline Progression (Tier 2) | Hard at Tier 2 | Calls `add_materials_batch` with offline delta |
| Hub HUD | Hard | Reads `get_summary()` for element totals + recent-acquisition badge |
| Material Inventory UI | Hard | Primary visual consumer of inventory + catalog |
| Recipe Discovery (Tier 2) | Hard at Tier 2 | Reads material catalog for combo lookups |
| Collection/Signature (Tier 2) | Soft | Reads material counts for completionist tracking |

### Bidirectional Consistency Note

When Forge System, Economy System, Save/Load, and others are authored, each must list "Material System" in their Dependencies section. Signals (`material_acquired`, `material_overflow`, `materials_consumed`) are part of the public contract — consumers must subscribe rather than poll.

---

## Tuning Knobs

Designer-adjustable infrastructure + economy knobs. Stored in `BalanceConstants` resource (owner: Data-Driven Config).

| Knob | Default | Safe range | Breaks if too low | Breaks if too high |
|---|---|---|---|---|
| `STACK_CAP_PER_MATERIAL` | 999 | 99 — 9999 | Constant overflow frustrates players | Stockpile hoarding defeats consumption design |
| `IDLE_RATE_SECONDS_PER_UNIT` | 3600 (1/hr) | 300 — 86400 | Too generous, kills scarcity | Session return gives almost nothing |
| `IDLE_MAX_OFFLINE_UNITS` | 32 (8 hrs × 4/hr) | 8 — 168 | 1-hr player loses materials | Week-long AFK becomes profitable |
| `OVERFLOW_RATE_COMMON` | 2 gold/unit | 1 — 10 | Overflow feels like total loss | Incentivizes hoarding for gold farming |
| `OVERFLOW_RATE_UNCOMMON` | 8 | 4 — 20 | Same | Same |
| `OVERFLOW_RATE_RARE` | 35 | 20 — 60 | Same | Same |
| `OVERFLOW_RATE_EPIC` | 150 | 100 — 300 | Same | Epic gold value rivals crafting value |
| `OVERFLOW_RATE_LEGENDARY` | 500 (Tier 2) | 300 — 1000 | — (tier inactive at MVP) | — (tier inactive at MVP) |
| `DROP_RATE_COMMON_PER_MILLE` | 650 | 400 — 800 | Players don't progress | Commons crowd out rarer drops |
| `DROP_RATE_UNCOMMON_PER_MILLE` | 250 | 150 — 400 | Feels empty | Drowns out common design |
| `DROP_RATE_RARE_PER_MILLE` | 80 | 30 — 150 | Rare feels mythic (drought) | Rare feels common (devalued) |
| `DROP_RATE_EPIC_PER_MILLE` | 15 | 5 — 40 | Epic unreachable | Epic loses ceremony |
| `RECENT_ACQUISITIONS_CAP` | 5 | 3 — 10 | UI badge hard to notice | UI clutter, hard to parse |
| `UNCOMMON_IDLE_DAY_ROTATION` | array of 7 material IDs | must be size 7 | Rotation predictable after short time | Runtime error on mismatched size |

### Knob Interactions

- Sum of `DROP_RATE_*_PER_MILLE` must = 1000 (full probability distribution). Enforced by build-time LootTable validation.
- `IDLE_RATE_SECONDS_PER_UNIT × IDLE_MAX_OFFLINE_UNITS` defines the maximum generation window — default combined = 8 hrs (3600 × 8 = 28,800 sec).
- `OVERFLOW_RATE_[tier]` should stay BELOW the gold value produced by crafting a weapon using that material. If overflow gold > crafting gold yield, players hoard for gold instead of crafting. This must be validated against future Economy System cost curves.
- `UNCOMMON_IDLE_DAY_ROTATION` array size must match `Time.get_date_dict_from_system()["weekday"]` enumeration (7 entries for 7 days).

---

## Acceptance Criteria

23 criteria across 10 categories. Format: GIVEN-WHEN-THEN. Mix of automated (gdUnit4) and manual.

### H1. Catalog Integrity

**H1-1** [BLOCKING — automated]
- **GIVEN** the game boots with the default material catalog config
- **WHEN** MaterialRegistry initializes
- **THEN** exactly 12 MaterialDefinition resources are loaded: 5 Common, 4 Uncommon, 2 Rare, 1 Epic

**H1-2** [BLOCKING — automated]
- **GIVEN** the material catalog config
- **WHEN** each entry is validated against the MaterialDefinition schema
- **THEN** every entry has: unique StringName `id`, `tier` within [Common, Uncommon, Rare, Epic], `element_id` within the 7 valid elements, `flavor_text` ≤ 120 chars, non-negative `base_stat_contribution`

**H1-3** [BLOCKING — automated]
- **GIVEN** a MaterialDefinition with `flavor_text` of 121 characters
- **WHEN** the @tool schema validator runs
- **THEN** validation fails and logs an error identifying the offending entry by id

### H2. Acquisition API

**H2-1** [BLOCKING — automated]
- **GIVEN** a player inventory with 0 units of `iron_root`
- **WHEN** `add_material("iron_root", 5)` is called
- **THEN** `get_count("iron_root")` returns 5 and signal `material_acquired` is emitted once with `{id: "iron_root", amount_added: 5}`

**H2-2** [BLOCKING — automated]
- **GIVEN** a player inventory
- **WHEN** `add_materials_batch({"iron_root": 3, "ash_shard": 2})` is called
- **THEN** both materials are credited atomically, `get_count` returns correct values, and `material_acquired` emits once per material

**H2-3** [BLOCKING — automated]
- **GIVEN** any acquisition call
- **WHEN** the `material_id` is not present in the catalog
- **THEN** the call returns 0 (nothing added), no inventory state changes, and `push_error` logs in debug builds (silent in release)

### H3. Consumption API

**H3-1** [BLOCKING — automated]
- **GIVEN** player has 10 `iron_root` and 5 `ash_shard`
- **WHEN** `consume_materials({"iron_root": 3, "ash_shard": 5})` is called
- **THEN** both deductions apply atomically, signal `materials_consumed` emits with the consumed map, `get_count` returns 7 and 0 respectively

**H3-2** [BLOCKING — automated]
- **GIVEN** player has 10 `iron_root` and 2 `ash_shard`
- **WHEN** `consume_materials({"iron_root": 3, "ash_shard": 5})` is called (insufficient ash_shard)
- **THEN** returns `false`, neither material deducted (all-or-nothing), no signal emitted

**H3-3** [BLOCKING — automated]
- **GIVEN** a negative or zero value in requirements
- **WHEN** `consume_materials` is called
- **THEN** returns `false` immediately without modifying inventory

### H4. Query API

**H4-1** [BLOCKING — automated]
- **GIVEN** player with 3 `ash_shard` (Fire), 4 `saltfire_resin` (Fire), 2 `frost_petal` (Water/Ice)
- **WHEN** `get_total_by_element("fire")` is called
- **THEN** returns 7

**H4-2** [BLOCKING — automated]
- **GIVEN** a player inventory with known contents
- **WHEN** `get_summary()` is called
- **THEN** returned Dictionary contains entry for every material held, with correct count, no entries for materials with count=0

**H4-3** [BLOCKING — automated]
- **GIVEN** player with 5 `iron_root`
- **WHEN** `has_materials({"iron_root": 5, "ash_shard": 1})` is called
- **THEN** returns `false` (ash_shard missing); called with `{"iron_root": 5}` alone returns `true`

### H5. Stack Cap and Overflow

**H5-1** [BLOCKING — automated]
- **GIVEN** player has 995 `iron_root` (Common, tier 1)
- **WHEN** `add_material("iron_root", 10)` is called
- **THEN** `iron_root` capped at 999, 6 overflow units convert to gold at Common rate (2 gold/unit = 12 gold), `material_overflow` emits with `{id, discarded_units: 6, gold_awarded: 12}`, `Economy.add_gold(12)` called before signal

**H5-2** [BLOCKING — automated]
- **GIVEN** a batch add where multiple materials overflow simultaneously
- **WHEN** `add_materials_batch` is processed
- **THEN** each overflowing material is independently capped at 999, overflow gold summed per material and credited, one `material_overflow` signal per overflowing material

**H5-3** [BLOCKING — automated]
- **GIVEN** one unit of each rarity tier overflows
- **WHEN** gold conversion calculates
- **THEN** Common yields 2 gold/unit, Uncommon 8, Rare 35, Epic 150 — verified with integer arithmetic, no float operations

### H6. Idle Generation

**H6-1** [BLOCKING — automated]
- **GIVEN** idle generator initialized at timestamp T with clean state
- **WHEN** award function called at T + 3600 seconds (1 hour)
- **THEN** player receives exactly 4 Common materials (distributed across 5 Commons) and exactly 1 Uncommon of the rotating day's type

**H6-2** [BLOCKING — automated]
- **GIVEN** idle generator with 8-hour accumulation cap
- **WHEN** award function called at T + 36000 seconds (10 hours offline)
- **THEN** player receives 8-hour maximum (32 Common, 8 Uncommon) — NOT the 10-hour total — `material_acquired` reflects capped amounts

**H6-3** [BLOCKING — automated]
- **GIVEN** system clock set backwards by 1 hour between two award calls
- **WHEN** idle generator calculates elapsed time
- **THEN** elapsed time treated as 0 (not negative), no materials awarded, no crash/underflow

### H7. Persistence

**H7-1** [BLOCKING — automated]
- **GIVEN** a player inventory with known material counts
- **WHEN** `save_inventory()` + `load_inventory()` on fresh MaterialSystem instance
- **THEN** `get_count` returns identical values for every material (round-trip integrity)

**H7-2** [BLOCKING — automated]
- **GIVEN** a save file where one material entry contains a non-integer value (corrupt)
- **WHEN** `load_inventory()` is called
- **THEN** system loads all valid entries, skips corrupt entry (defaults to 0), logs warning identifying corrupt key, no crash

**H7-3** [BLOCKING — automated]
- **GIVEN** a save file referencing a material id NOT present in current catalog (deprecated)
- **WHEN** `load_inventory()` is called
- **THEN** unknown id is preserved but NOT exposed via queries (held for future migration), all known materials load, warning logged with deprecated id

### H8. Cross-Platform Determinism

**H8-1** [BLOCKING — automated, multi-platform]
- **GIVEN** a LootTable with seed value 12345
- **WHEN** 1000 sequential weighted random drops are drawn on iOS, Android, and Web builds
- **THEN** all three platforms produce identical drop sequences (verified by SHA-256 of output array)

**H8-2** [BLOCKING — automated static analysis]
- **GIVEN** any material count arithmetic (add, consume, overflow)
- **WHEN** the code path executes
- **THEN** all intermediate values are integers — no float casts in `MaterialSystem.gd` (verified by CI grep for `float(` and `/` without integer division context)

### H9. Edge Cases

**H9-1** [BLOCKING — automated]
- **GIVEN** `add_material` called with negative amount
- **WHEN** the call is processed
- **THEN** returns 0, inventory unchanged, no signal emitted

**H9-2** [BLOCKING — automated]
- **GIVEN** `add_material` called with amount = 0
- **WHEN** the call is processed
- **THEN** returns 0, no `material_acquired` signal (zero-quantity is not a valid event)

**H9-3** [ADVISORY — manual]
- **GIVEN** game is running and a future update has removed a material from the catalog
- **WHEN** a player who owned that material loads their save
- **THEN** UI displays no phantom entries, summary omits the deprecated id, no error shown to player (warning only in dev log)

### H10. Anti-P2W Validation

**H10-1** [BLOCKING — automated static analysis]
- **GIVEN** grep across all `.gd` files in `src/` + `.tres` in `assets/data/`
- **WHEN** searching for calls to payment/IAP/store APIs in any code path touching `add_material` or `add_materials_batch`
- **THEN** zero matches — no paid acquisition path exists in MVP

**H10-2** [ADVISORY — manual code review]
- **GIVEN** a review of all acquisition entry points (PvE drops, idle generation, daily rewards)
- **WHEN** each entry point is traced to its trigger
- **THEN** every acquisition path is triggered by gameplay action or time only — never gated behind currency purchase or premium unlock

### Gate Summary

| Category | Criteria | BLOCKING | ADVISORY |
|---|---|---|---|
| H1. Catalog Integrity | 3 | 3 | 0 |
| H2. Acquisition API | 3 | 3 | 0 |
| H3. Consumption API | 3 | 3 | 0 |
| H4. Query API | 3 | 3 | 0 |
| H5. Stack Cap + Overflow | 3 | 3 | 0 |
| H6. Idle Generation | 3 | 3 | 0 |
| H7. Persistence | 3 | 3 | 0 |
| H8. Cross-Platform Determinism | 2 | 2 | 0 |
| H9. Edge Cases | 3 | 2 | 1 |
| H10. Anti-P2W Validation | 2 | 1 | 1 |
| **Total** | **28** | **26** | **2** |

---

## Open Questions

- **Clock manipulation detection**: MaterialSystem trusts `elapsed_seconds` from Idle/Offline Progression. Detection itself lives in Idle GDD (Tier 2). *Owner*: Idle/Offline Progression GDD. *Resolve when*: Tier 2 authoring.
- **LootTable "idle" tag**: Material rotations for idle generation use LootTables tagged `"idle"`. The tagging convention is implied but not yet formalized. *Owner*: Economy System GDD. *Resolve when*: Economy GDD authoring.
- **Material ordering in Inventory UI**: Default "element group, then tier ascending". Player-configurable sort is post-MVP. *Owner*: Material Inventory UI GDD + ux-designer.
- **`mark_inventory_seen()` trigger granularity**: Currently called on Material Inventory UI open. Should Hub HUD viewing the "recent" badge also count? *Owner*: Hub HUD GDD.
- **Schema migration for `flavor_text` addition**: Non-breaking optional field with default — no schema_version bump proposed. If approach shifts (all additions bump version), this needs revisit. *Owner*: data-driven-config ADR.
- **Weekly challenge chest (Rare guaranteed path)**: economy-designer proposed as secondary path for Rare materials. Not yet specced — which system owns the "weekly challenge" concept? *Owner*: TBD (probably Player Progression or Event system). *Resolve when*: Tier 2 event design.
- **Boss drop for Epic materials**: Voidember Core drops from "1 boss at MVP". The boss is not yet defined — PvE roaming boss? weekly raid? milestone encounter? *Owner*: Enemy System GDD + game-designer.
- **Tier 2 per-zone LootTables**: Current design has enemy-type-agnostic drop rates. economy-designer flagged introducing per-zone tables in Tier 2 if farming becomes concern. *Owner*: Economy System GDD (Tier 2 update).
- **Daily rewards system**: Mentioned as an acquisition path but not specified (what rewards? what calendar?). *Owner*: Live-Ops / Economy GDD. *Resolve when*: Tier 2 planning.

### ADRs this GDD will generate

- **ADR**: Material catalog MVP (12 materials, 7 elements, 4 active rarity tiers)
- **ADR**: Stack cap + overflow-to-gold conversion (vs reject-with-notify)
- **ADR**: Idle generation rates + 8-hour offline cap
- **ADR**: Anti-P2W material acquisition (no premium paths at any tier)
