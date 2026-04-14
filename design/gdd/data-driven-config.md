# Data-Driven Config

> **Status**: In Design
> **Author**: lucianodepalo-ai + Claude (systems-designer agent)
> **Last Updated**: 2026-04-13
> **Implements Pillar**: Pillar 2 (Variabilidad emergente, indirecto) + Pillar 4 (Meta emergente, indirecto)
> **System #**: 17 — Foundation layer, MVP priority

---

## Overview

**Data-Driven Config** es la capa de infraestructura que define cómo todos los sistemas del juego almacenan, cargan y validan sus datos de configuración fuera del código. Es el "schema standard" que el resto de los sistemas (Material, Weapon Entity, Affix, Hero/Wielder, Enemy, Combat, Economy, Player Progression, etc.) usan para representar sus entidades como recursos editables.

En Godot 4.6, esto se implementa a través de **Custom Resources** (`extends Resource`) serializadas como archivos `.tres`. Cada tipo de entidad del juego (un material de forja, un afijo, una receta) es una clase que extiende `Resource` con propiedades anotadas como `@export`. Los `.tres` viven en `assets/data/` organizados por categoría, son editables tanto en el inspector de Godot como en text editor (lo que los hace version-control friendly), y se cargan vía `ResourceLoader` con soporte para streaming async.

El sistema también define las **convenciones contractuales** que los demás GDDs deben respetar: estructura de carpetas, naming conventions de archivos, estrategia de validación al cargar, y el patrón de versionado para evolucionar schemas sin romper saves antiguos.

Sin esta capa, cada sistema reinventaría cómo guardar sus datos (uno usaría JSON, otro CSV, otro hardcodearía constantes), los designers no podrían tunear sin tocar código, y el meta del juego se pudriría porque rebalancear requeriría builds nuevos. Con esta capa, agregar un nuevo material o ajustar la fórmula de XP es editar un archivo de texto que el game reload puede levantar en hot.

---

## Player Fantasy

The player never sees a config file, never edits a `.tres` resource, never thinks about data schemas — and that's exactly the point. What they feel instead is a **world that answers back**. When the dominant meta build starts to suffocate experimentation, a patch lands overnight and suddenly a forgotten material is viable again. When a new season drops, the material catalog has grown, recipes have shifted, and the forge floor feels different without the game feeling *rewritten*. The meta breathes. The designers listen. The blacksmith's craft is never "solved" for long — because the rules of the craft themselves are alive, tuned by unseen hands in response to how the server is actually playing. This is the quiet magic of data-driven config: it's the infrastructure that lets the game feel like a living workshop instead of a frozen artifact.

### How each pillar is served (indirectly)

- **Pillar 2 — Variabilidad emergente**: New materials, affixes, and recipes can be added to the catalog without a code release. The player feels an expanding sandbox where "I haven't tried that combo yet" stays true for months.
- **Pillar 4 — Meta emergente, no impuesto**: When a build dominates the async PvP ladder, designers rebalance in hours, not weeks. The player feels a meta that evolves with them — never stagnant, never dictated, always responsive to the community's discoveries.
- **Pillar 1 — Estética es el primer impacto** (polish proxy): When a balance bug or broken recipe slips through, the fix ships as a hotfix, not a tentpole patch. The player feels a game that is *cared for* — the aesthetic promise extends to the sensation that nothing stays broken for long.

---

## Detailed Design

### Core Rules

#### 1. Resource Type Catalog (MVP)

| Resource Type | Represents | Key Fields (conceptual) |
|---|---|---|
| `MaterialDefinition` | A craftable ingredient | id, display_name, element_id, tier (int 1–5), base_stat_contribution (int), rarity_weight (int), tags |
| `AffixDefinition` | A stat modifier attached to weapons | id, display_name, stat_key, magnitude_min (int), magnitude_max (int), affix_tier (int 1–5), incompatible_affix_ids |
| `ElementDefinition` | An element and its relationships | id, display_name, strong_against, weak_against, neutral_against |
| `WeaponArchetypeDefinition` | Template for a weapon class | id, display_name, base_stats (dict of int), allowed_affix_tiers, element_id, attack_pattern_id |
| `HeroArchetypeDefinition` | Wielder class template | id, display_name, stat_scaling (dict of int x1000 fixed-point), passive_affix_ids |
| `EnemyDefinition` | An enemy in PvE encounters | id, display_name, power_rating (int), stat_block (dict of int), element_id, loot_table_id |
| `RecipeHintDefinition` | A discoverable forge recipe fragment | id, required_material_ids, required_affix_hint_ids, result_archetype_id, unlock_condition |
| `BalanceConstants` | Global tuning values (single file) | schema_version, combat_round_cap (int), crit_base_chance_per_mille (int), dodge_cap_per_mille (int), power_rating_formula_weights |
| `LigaDefinition` | A competitive tier band | id, display_name, power_rating_min (int), power_rating_max (int), reward_table_id, promotion_threshold_per_mille (int) |
| `LootTableDefinition` | Drop probability table | id, entries (Array of {material_id, weight_int}); validation enforces non-zero sum |

#### 2. Folder Structure

```
assets/data/
  materials/          # MaterialDefinition .tres
  affixes/            # AffixDefinition .tres
  elements/           # ElementDefinition .tres
  weapon_archetypes/  # WeaponArchetypeDefinition .tres
  hero_archetypes/    # HeroArchetypeDefinition .tres
  enemies/            # EnemyDefinition .tres
  recipe_hints/       # RecipeHintDefinition .tres
  balance/            # BalanceConstants .tres (single file at MVP)
  ligas/              # LigaDefinition .tres
  loot_tables/        # LootTableDefinition .tres
  manifest.tres       # build-time generated catalog (see Rule #4)
```

#### 3. Naming Convention

`.tres` files use `snake_case` matching the entity's `id` field exactly. Example: `id: "iron_shard"` ↔ `iron_shard.tres`. Mismatches between filename and `id` are caught by `@tool` validation at save time.

#### 4. Build-Time Manifest (replaces folder scan)

A build script (`tools/build/generate_config_manifest.gd`) walks `assets/data/` at build time and writes `assets/data/manifest.tres` containing an `Array[String]` of all resource paths grouped by category. The `ConfigLoader` autoload reads ONLY this manifest at runtime — never `DirAccess.list_dir_*`.

**Why**: Godot 4.6's `DirAccess` returns empty in Web HTML5 exports (virtual filesystem is read-only with no directory enumeration). The manifest produces identical behaviour on mobile and Web from a single code path.

The manifest is regenerated automatically on `pre-export` hook; commit it to version control alongside the `.tres` files.

#### 5. Schema Versioning

Every Resource class declares `@export var schema_version: int = 1`. Migration scripts live in `tools/data_migrations/migrate_[ResourceType]_v[N]_to_v[N+1].gd`, each exposing `static func migrate(resource: Resource) -> Resource`.

A static migration utility (`ConfigMigrator`, plain GDScript class — NOT `@tool`) is invoked by `ConfigLoader` immediately after each load. If the resource's `schema_version` is below current, it runs migrations in sequence until current.

**Anti-pattern (forbidden)**: putting migration logic in `_init()`. `_init()` fires when creating new blank instances (e.g., `MaterialDefinition.new()` in code or via the editor "New Resource" action), NOT on load. Migration in `_init()` runs against default values and never matches a real old version.

#### 6. `@tool` Validation Pattern

Resource classes are annotated `@tool` and override `_get_configuration_warnings() -> PackedStringArray`. Warnings appear inline in the inspector while designers edit. Validation enforces: required string fields non-empty, int ranges within declared bounds, foreign-key `id` references resolve, `LootTableDefinition` weight sums non-zero, filename matches `id` field.

`@tool` scripts must guard scene-tree access:
```gdscript
func _get_configuration_warnings() -> PackedStringArray:
    if not Engine.is_editor_hint():
        return []
    # editor-safe validation only
```

Never call `get_tree()`, `get_node()`, or access autoloads from `@tool` resource scripts — Resources have no scene tree owner.

#### 7. Determinism Rule (cross-platform combat)

ALL combat-critical numeric values are stored as `int` or `int x1000` (per-mille fixed-point — e.g., `1250` represents `1.25x`). Floats are forbidden in stat blocks, formulas, and any value that feeds the Combat Simulation. Float conversion is permitted ONLY in the UI display layer.

This satisfies the determinism requirement of Combat Simulation across iOS / Android / Web (the system's high-risk technical concern from the concept doc).

#### 8. Identity Types

IDs use `StringName` literals (`&""` syntax), not `String`. `StringName` is interned and faster for the dictionary key lookups that dominate config access patterns.

#### 9. Resource UIDs for Cross-References

Cross-references between Resources (e.g., `MaterialDefinition.element_id` pointing to an `ElementDefinition`) are stored using Godot's UID system (`uid://abc123`), not `resource_path` strings. UIDs survive file renames and moves; string paths break silently.

The `.godot/uid_cache.tres` must NOT be in `.gitignore`. The repo's `.gitignore` is verified at project setup and CI checks the cache is committed.

#### 10. Runtime Load Phases

The `ConfigLoader` autoload loads resources in three phases:

| Phase | When | What | How |
|---|---|---|---|
| **Boot** (sync, blocking) | Before main menu | `BalanceConstants`, `ElementDefinition`, `LigaDefinition` (~20 files total) | Synchronous `load()`. Fatal on any failure. |
| **Post-boot lazy batch** (async) | Main menu shown, before first gameplay | `WeaponArchetypeDefinition`, `HeroArchetypeDefinition`, `AffixDefinition`, `LootTableDefinition` | `ResourceLoader.load_threaded_request()` with progress polling. Systems gate on `data_ready` signal. |
| **On-demand** | First reference per session | `MaterialDefinition`, `EnemyDefinition`, `RecipeHintDefinition` | `load()` per ID, cached for the session. |

On Web HTML5, the initial bundle (< 15 MB) contains Boot-phase resources only (target: < 1 MB of `.tres` data). All other categories stream from the asset CDN via `ResourceLoader.load_threaded_request()`.

#### 11. Anti-Patterns Forbidden

1. `preload()` inside loops or runtime scan — `preload()` is parse-time, not runtime
2. Game logic inside `@tool` resource scripts (autoload access, signal emission, scene tree manipulation)
3. `resource_path` string cross-references (use UIDs)
4. Resource scatter beyond one entity per file (one `MaterialDefinition` per `.tres`, never one field per file)
5. Schema migration logic in `_init()`
6. Floats in any combat-critical field
7. Cross-file cyclic resource references (file A references file B which references file A) — Godot handles cycles within a single file but crashes on file-graph cycles

### States and Transitions

```
[Defined] → [Listed in manifest] → [Loaded] → [Migrated?] → [Validated] → [Active]
                                                  ↓              ↓
                                            (no-op if         [Invalid]
                                             schema OK)
```

| State | Meaning |
|---|---|
| **Defined** | `.tres` file exists in `assets/data/`. Authoring state. |
| **Listed** | Path appears in `manifest.tres`. Build-time guarantee. |
| **Loaded** | `ResourceLoader.load()` returned the Resource object. |
| **Migrated** | Schema was below current; migration script(s) ran. Proceeds to validation. |
| **Validated** | Passed `@tool` and runtime validation. Safe to expose to consumers. |
| **Invalid** | Validation failed. Held in `ConfigLoader.invalid_resources: Dictionary` (keyed by path). NOT exposed to consumers. |
| **Active** | Consumer system holds a reference and is using it in gameplay. |

**Behavior on validation failure**:

| Phase | Failure behavior |
|---|---|
| **Boot** (BalanceConstants, Element, Liga) | **FATAL** — game refuses to start. Logs path + error. Prevents silent corruption of combat-critical constants. |
| **Lazy / on-demand** | **WARNING** — log + skip the broken resource. Consumer requesting the missing ID receives `null` and must handle the absent-data path gracefully. Never a hard assert in production builds. |

### Interactions with Other Systems

**Schema ownership rule**: `data-driven-config` defines the Resource class and field contract. Consumer systems read from those classes but **never write back to `.tres` files at runtime**.

| Consumer System | How it accesses | Data type | Combat-critical notes |
|---|---|---|---|
| **Material System** | scan via manifest, `ConfigLoader.get_all(MaterialDefinition)` | `Array[MaterialDefinition]` | `base_stat_contribution` is int |
| **Weapon Entity System** | `get_by_id("weapon_archetypes", id)` | `WeaponArchetypeDefinition` | `base_stats` dict — int values only, floats forbidden |
| **Affix System** | full catalog at post-boot | `Array[AffixDefinition]` | `magnitude_min`/`magnitude_max` ints; UI scales to float |
| **Hero / Wielder System** | `get_by_id("hero_archetypes", id)` at hero select | `HeroArchetypeDefinition` | `stat_scaling` is int x1000 fixed-point (e.g., 1250 = 1.25x); integer math throughout |
| **Enemy System** | on-demand at encounter construction | `EnemyDefinition` | `stat_block` all ints. `loot_table_id` resolved via `LootTableDefinition` on the same on-demand path |
| **Combat Simulation** | injected at `_init()` from systems above | pre-resolved stat blocks (int / per-mille int) | NEVER reads `.tres` directly. `BalanceConstants` injected at boot. Float-free path. |
| **Economy System** | `get_loot_table(id)` | `LootTableDefinition` | weights are ints; RNG seeded deterministically per `combat_session_id` (not per-platform clock) |
| **Player Progression** | `get_liga(id)` | `LigaDefinition` | `promotion_threshold_per_mille` int (e.g., 850 = top 85.0% triggers promotion); no float comparisons |
| **Liga / Matchmaking** | scan all at boot | full `Array[LigaDefinition]` | uses int `power_rating_min`/`max` for bracket assignment; immutable during session |
| **Anti-Cheat** | `get_boot_manifest_hash() -> String` at session start | `String` hash | client hash vs server-expected hash mismatch flags session for review |

---

## Formulas

This system is infrastructure — **it contains no gameplay balance formulas of its own**. Balance formulas live in `BalanceConstants` (a downstream Resource defined here) and in downstream system GDDs (Combat Sim, Economy, Player Progression, Matchmaking, Forge, Affix, Liga).

Two utility formulas of the system itself are documented below.

### Utility 1 — Per-mille Fixed-Point Display Conversion

For displaying combat-critical values (stored as int x1000) in UI:

`display_value = stored_int / 1000.0`

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `stored_int` | s | int | -1,000,000 to 1,000,000 (per-mille) | Raw value from Resource (e.g., `stat_scaling = 1250`) |
| `display_value` | d | float | -1,000.0 to 1,000.0 | UI-only float for display (e.g., "1.25x") |

**Output Range**: -1,000.0 to 1,000.0 under normal play. Float division occurs **only in UI**, never in combat path.

**Example**: `stat_scaling = 1250` → display "**1.25×**". `crit_chance_per_mille = 175` → display "**17.5%**".

### Utility 2 — Boot Manifest Hash

For Anti-Cheat session integrity validation:

`boot_hash = SHA256(concat(sorted(boot_resource_paths) || sorted(boot_resource_content_hashes)))`

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `boot_resource_paths` | P | Array[String] | ~20 paths | Paths of Resources loaded in boot phase (BalanceConstants, ElementDefinition, LigaDefinition) |
| `boot_resource_content_hashes` | C | Array[String] | ~20 SHA256 hashes | SHA256 of the serialized binary content of each Resource |
| `boot_hash` | h | String (hex) | 64 chars | Final hash used to validate integrity |

**Output**: 64-char hex string. **Deterministic cross-platform**: sorted ordering + content hashing guarantees iOS, Android, and Web produce identical hashes if the Resources are identical.

**Example**: If client and server report different `boot_hash`, the server flags the session for review (the client may have tampered with the balance constants `.tres` files).

### Where Game Balance Formulas Live

| Formula type | Owner GDD (downstream) |
|---|---|
| Combat damage / mitigation | Combat Simulation |
| Power rating (build power) for matchmaking | Matchmaking |
| XP curves | Player Progression |
| Loot drop probability | Economy |
| Forge minigame timing windows | Forge System |
| Affix magnitude rolls | Affix System |
| Ladder promotion thresholds | Liga |

Data-Driven Config provides the **infrastructure** for those GDDs to declare their formulas as Resource fields (typically in `BalanceConstants` or per-category Resources).

---

## Edge Cases

- **If manifest is desynchronized from filesystem** (designer added/removed a `.tres` but didn't regenerate manifest): pre-export hook regenerates automatically; CI check fails the build if manifest hash differs from actual filesystem scan. Never load resources NOT listed in manifest.
- **If boot-phase Resource fails validation** (e.g., `BalanceConstants` missing required field): **FATAL** — game refuses to start, displays error screen with file path + field name. Prevents silent corruption of combat constants.
- **If lazy/on-demand Resource fails validation**: log warning, mark resource as Invalid, skip from consumer-facing registries. Consumer requesting that ID receives `null` and must handle absence (e.g., display "[material missing]" in UI, exclude from forge eligibility). Never hard-assert in production.
- **If schema migration script throws an error**: resource stays at original `schema_version`, marked Invalid, logged. Never write back partially-migrated state to disk.
- **If `schema_version` is HIGHER than `CURRENT_SCHEMA`** (designer edited `.tres` manually with future version, or downgraded the game build): treat as Invalid; log "Resource is from a newer version than this build supports — please update the game". Refuse to migrate downward.
- **If Resource UID cache is lost** (`.godot/uid_cache.tres` deleted or corrupted): cross-references using `uid://` will fail to resolve. Godot regenerates the cache on next editor open by re-scanning all `.tres` files, but cross-references remain broken until then. CI MUST verify the cache is committed; build fails if cache is missing.
- **If two `.tres` files reference each other cyclically across files** (file A → file B → file A): Godot's loader infinite-loops and crashes. Mitigation: build-time validator (part of manifest generator) detects cyclic graphs and fails the build with the cycle path printed.
- **If hot-reload in editor produces stale cache during play-from-editor**: `ConfigLoader` exposes a `_force_reload()` method available only when `OS.is_debug_build()` returns true; designers can trigger via in-game debug menu. Production builds have no hot-reload; the method is a no-op.
- **If `load_threaded_request()` fails in Web HTML5** (network drop during streaming load): retry up to 3 times with exponential backoff (1s, 2s, 4s). After 3 failures, surface a user-facing "connection lost — retry" UI; do not silently degrade.
- **If `LootTableDefinition.entries` has weight sum = 0** (designer typo or all entries marked weight 0): caught by `@tool` validation at save time. If somehow loaded (validation bypass): runtime treats as "no drop" — never divides by zero.
- **If filename does not match `id` field** (e.g., file `iron_shard.tres` but `id: "iron_shrd"` typo): caught by `@tool` validation at save. If escaped (loaded resource from external paste): runtime logs warning, uses the `id` field as the canonical key, ignores filename.
- **If Resource path contains non-ASCII or whitespace**: forbidden by validation at save time (filename pattern `^[a-z0-9_]+\.tres$` enforced). Avoids cross-platform filesystem inconsistencies (Windows/macOS/Linux/iOS/Android handle these differently).
- **If boot-phase loading is interrupted** (mobile app suspended mid-`load()`): `ConfigLoader._ready()` is idempotent — on resume from suspend, it checks which boot resources are already loaded and resumes from where it stopped. State stored in a per-session `_boot_progress: Dictionary` flushed only after all boot resources are validated.
- **If two `MaterialDefinition` files share the same `id`** (designer copy-paste error): build-time manifest generator detects duplicates and fails the build with both file paths printed. Runtime never has to handle this — caught at build.
- **If a downstream consumer requests an ID before the corresponding load phase has completed**: `ConfigLoader.get_by_id()` returns `null` and emits a warning "Requested [type:id] before [phase] load complete — check load order". Consumer code must defer requests until `data_ready` signal fires.

---

## Dependencies

### Upstream Dependencies (this system depends on)

**None.** Data-Driven Config is a Foundation-layer system. Its only "dependency" is Godot 4.6 itself (Custom Resources, `@tool` annotation, `ResourceLoader`, `DirAccess`, UID system) and the project filesystem.

### Downstream Dependents (systems that depend on this)

Listed by load phase to clarify the load-order contract:

| Phase | Consumer System | Resources requested | Hard or Soft? |
|---|---|---|---|
| **Boot (sync)** | Combat Simulation | `BalanceConstants` (injected at init) | Hard — combat cannot run without it |
| **Boot (sync)** | Liga / Matchmaking | full `Array[LigaDefinition]` | Hard — bracket assignment requires it |
| **Boot (sync)** | All systems doing element math | full `Array[ElementDefinition]` | Hard — element relationships referenced everywhere |
| **Post-boot (async)** | Weapon Entity System | `WeaponArchetypeDefinition` catalog | Hard — weapon creation requires archetype |
| **Post-boot (async)** | Hero / Wielder System | `HeroArchetypeDefinition` catalog | Hard — hero selection requires archetype |
| **Post-boot (async)** | Affix System | full `AffixDefinition` catalog | Hard — affix application validates incompatibilities across full set |
| **Post-boot (async)** | Economy System | `LootTableDefinition` catalog | Hard — drop resolution requires tables |
| **On-demand** | Material System | `MaterialDefinition` per id | Hard — forge requires materials |
| **On-demand** | Enemy System | `EnemyDefinition` per id | Hard — encounter requires enemy |
| **On-demand** | Recipe Discovery | `RecipeHintDefinition` per id | Hard — recipe lookup requires hint |
| **On-demand (Tier 2)** | Anti-Cheat Validation | `boot_manifest_hash` | Soft — validation layer; system functions without it |
| **On-demand (Tier 2)** | Player Progression | `LigaDefinition` (read for thresholds) | Hard at Tier 2 |
| **On-demand (Tier 3)** | Battle Pass | future `BattlePassDefinition` | Hard at Tier 3 |
| **On-demand (Tier 3)** | Cosmetic / Skin | future `CosmeticDefinition` | Hard at Tier 3 |
| **On-demand (Tier 3)** | Seasonal Events | future `SeasonalEventDefinition` | Hard at Tier 3 |

### Schema Ownership Contract

Data-Driven Config defines the **Resource class** (e.g., `MaterialDefinition extends Resource`) but does NOT define the **field semantics** (what `base_stat_contribution` means in combat). Field semantics are owned by the consumer system's GDD:

- `MaterialDefinition.base_stat_contribution` → semantics owned by Material System GDD
- `WeaponArchetypeDefinition.base_stats` → semantics owned by Weapon Entity GDD
- `AffixDefinition.magnitude_min`/`max` → semantics owned by Affix System GDD
- `BalanceConstants.combat_round_cap` → semantics owned by Combat Simulation GDD

This separation prevents Data-Driven Config from becoming a god-class GDD that knows everything.

### Bidirectional Consistency Note

When each downstream GDD is authored, that GDD must list "Data-Driven Config" in its own Dependencies section. The systems index records these relationships — verify on each new GDD.

---

## Tuning Knobs

These are designer-adjustable infrastructure values, NOT gameplay balance. Gameplay balance knobs live in `BalanceConstants` (a Resource defined here, but tuned in its own GDD).

| Knob | Default | Safe range | What breaks if too low | What breaks if too high |
|---|---|---|---|---|
| `BOOT_LOAD_TIMEOUT_MS` | 5000 | 2000–15000 | Resources that legitimately take long fail to load on slow devices | Splash screen freezes for 15s+ on broken builds |
| `LAZY_BATCH_LOAD_PARALLELISM` | 4 | 1–8 | Slower post-boot loading; gameplay starts later | Memory spike during load; mobile low-end may OOM |
| `WEB_LOAD_RETRY_COUNT` | 3 | 1–5 | Single network blip = user error screen | Long delays on persistent network failure (5×4s = 20s wait) |
| `WEB_LOAD_RETRY_BACKOFF_MS` | `[1000, 2000, 4000]` | array up to 4 entries | Rapid retries flood server | Long total wait time for users |
| `RESOURCE_CACHE_MAX_SIZE_MB` | 80 | 20–150 | Cache evictions cause repeated reload (perf hit) | Memory pressure on mobile (close to 100 MB Web ceiling, 512 MB mobile) |
| `MIGRATION_LOG_LEVEL` | `"warning"` | `"info"` / `"warning"` / `"error"` | Silent migrations hide bugs in dev | Logs spam in production |
| `VALIDATION_FAIL_BEHAVIOR_BOOT` | `"fatal"` | `"fatal"` / `"skip-with-error"` | `"skip-with-error"` risks silent corruption of combat constants | (no upper risk; `"fatal"` is the safest default) |
| `VALIDATION_FAIL_BEHAVIOR_LAZY` | `"skip-warn"` | `"skip-warn"` / `"skip-silent"` / `"fatal"` | `"skip-silent"` hides bugs from QA; `"fatal"` makes one bad recipe crash the game | `"fatal"` only acceptable in dev builds |
| `MANIFEST_PATH` | `res://assets/data/manifest.tres` | filesystem path | Build script must match | Build script must match |
| `BOOT_PHASE_RESOURCE_PATHS` | `["balance/", "elements/", "ligas/"]` | array of relative paths | Removing one breaks dependent system at boot | Adding one delays splash screen |

### Knob Interactions

- `WEB_LOAD_RETRY_COUNT × sum(WEB_LOAD_RETRY_BACKOFF_MS)` should not exceed 30,000 ms total — beyond that, users perceive the app as frozen
- `LAZY_BATCH_LOAD_PARALLELISM × average_resource_size_MB` should not exceed `RESOURCE_CACHE_MAX_SIZE_MB / 2` — leaves headroom for active gameplay
- `VALIDATION_FAIL_BEHAVIOR_BOOT = "skip-with-error"` requires `MIGRATION_LOG_LEVEL = "error"` minimum to surface failures

---

## Acceptance Criteria

23 criteria, all using GIVEN-WHEN-THEN format. Mix of automated (gdUnit4) and manual.

### 1. Build-Time Validation

**AC-BUILD-01** — Manifest generation, happy path | BLOCKING | Automated
- **GIVEN** a project with valid `.tres` config files in `assets/data/`
- **WHEN** the manifest generation tool runs (`tools/build/generate_config_manifest.gd`)
- **THEN** `assets/data/manifest.tres` is written with an entry for every `.tres` in scope, each with its UID and SHA-256 content hash; tool exits code 0.

**AC-BUILD-02** — Duplicate ID detection | BLOCKING | Automated
- **GIVEN** two `.tres` files declaring the same `id`
- **WHEN** the manifest tool runs
- **THEN** tool exits non-zero, logs both file paths, no manifest is written.

**AC-BUILD-03** — Cyclic cross-reference detection | BLOCKING | Automated
- **GIVEN** files A and B referencing each other via `uid://`
- **WHEN** the manifest tool runs
- **THEN** tool exits non-zero, logs the cycle chain, no manifest is written.

**AC-BUILD-04** — Manifest completeness vs filesystem | BLOCKING | Automated
- **GIVEN** N config `.tres` files exist
- **WHEN** the manifest is generated
- **THEN** `manifest.entries.size() == N`; CI assertion fails on mismatch.

### 2. Boot Phase Load

**AC-BOOT-01** — Success path | BLOCKING | Automated (gdUnit4)
- **GIVEN** a valid `manifest.tres` present
- **WHEN** `ConfigLoader.initialize_boot()` is called
- **THEN** all boot-phase resources load synchronously, `is_boot_ready()` returns `true`, no errors logged.

**AC-BOOT-02** — Validation failure is fatal | BLOCKING | Automated
- **GIVEN** a boot-phase `.tres` whose hash mismatches its manifest entry
- **WHEN** boot initializes
- **THEN** game halts before main menu, error screen displays human-readable message (path + field).

**AC-BOOT-03** — Missing manifest is fatal | BLOCKING | Automated
- **GIVEN** `manifest.tres` is absent or unreadable
- **WHEN** boot initializes
- **THEN** game halts with clear error; never silently proceeds with empty config.

**AC-BOOT-04** — Performance target on low-end mobile | BLOCKING | Manual (target device)
- **GIVEN** a production build on the minimum-spec mobile target (Adreno 505 / Mali-G51 tier Android)
- **WHEN** the game cold-boots
- **THEN** boot phase completes in **under 500 ms** (target) — measured via `Time.get_ticks_msec()` instrumentation.
- *Note*: This is the performance target. `BOOT_LOAD_TIMEOUT_MS` (5000 ms) is the fatal failure threshold — different from target.

### 3. Post-Boot Async Load

**AC-ASYNC-01** — Success path and `data_ready` signal | BLOCKING | Automated
- **GIVEN** boot phase complete and `ConfigLoader.load_post_boot()` is called
- **WHEN** all post-boot resources finish loading
- **THEN** `ConfigLoader` emits `data_ready` signal with payload (count of resources loaded), no errors.

**AC-ASYNC-02** — Web retry with backoff (3 retries) | BLOCKING | Automated
- **GIVEN** Web HTML5 build where `load_threaded_request` for a post-boot resource fails first attempt
- **WHEN** the loader retries
- **THEN** retries up to 3 times with exponential backoff (1s, 2s, 4s) before emitting `resource_load_failed(id)` signal; never crashes or hangs indefinitely.

**AC-ASYNC-03** — Timeout path | ADVISORY | Automated
- **GIVEN** a post-boot resource exceeding `BOOT_LOAD_TIMEOUT_MS`
- **WHEN** the timeout elapses
- **THEN** logs warning, skips that resource, returns `null` for that ID, continues loading remaining.

### 4. On-Demand Load

**AC-ONDEMAND-01** — First-call load | BLOCKING | Automated
- **GIVEN** a resource registered in manifest but not yet loaded
- **WHEN** `ConfigLoader.get_by_id(category, id)` is called for the first time
- **THEN** resource is loaded, cached, and returned.

**AC-ONDEMAND-02** — Cache hit does not re-read disk | BLOCKING | Automated
- **GIVEN** a resource already loaded via `get_by_id()`
- **WHEN** called again for same ID
- **THEN** no file I/O occurs; cache hit counter increments, load counter unchanged.

**AC-ONDEMAND-03** — Unknown ID returns null with warning | BLOCKING | Automated
- **GIVEN** an ID not in manifest
- **WHEN** `get_by_id(category, unknown_id)` is called
- **THEN** returns `null`, logs WARN naming the unknown ID, no crash.

**AC-ONDEMAND-04** — Request before phase loaded returns null + warning | BLOCKING | Automated
- **GIVEN** post-boot phase not yet completed
- **WHEN** consumer calls `get_by_id()` for a post-boot resource
- **THEN** returns `null`, logs warning indicating phase not ready.

### 5. Schema Migration

**AC-MIGRATE-01** — Up-migration success | BLOCKING | Automated
- **GIVEN** a `.tres` with `schema_version = N` and migration script `migrate_[Type]_v[N]_to_v[N+1].gd`
- **WHEN** `ConfigLoader` loads the file and detects version mismatch
- **THEN** migration script runs, fields updated to N+1 schema, resource usable at runtime.

**AC-MIGRATE-02** — Downgrade refused | BLOCKING | Automated
- **GIVEN** a `.tres` with `schema_version > CURRENT_SCHEMA`
- **WHEN** load is attempted
- **THEN** resource rejected, marked Invalid, WARN logged with both versions, `null` returned.

**AC-MIGRATE-03** — Migration error keeps original version | BLOCKING | Automated
- **GIVEN** a migration script that throws a runtime error
- **WHEN** migration runs
- **THEN** original `.tres` not modified, resource marked Invalid, descriptive error logged, game continues.

### 6. Cross-Platform Determinism

**AC-DETERM-01** — Per-mille integer math: no float in combat path | BLOCKING | Automated + Code Review
- **GIVEN** a combat stat with stored value `1500` (representing 1.500x)
- **WHEN** any combat formula consumes that stat
- **THEN** all intermediate calculations use integer arithmetic; conversion to float happens only at UI display via `stored_int / 1000.0`. Verified by code review + unit test asserting integer types throughout combat call chain.

**AC-DETERM-02** — `display_value` formula correctness | BLOCKING | Automated (gdUnit4)
- **GIVEN** stored integer values `0`, `1`, `1000`, `1500`, `999999`
- **WHEN** `display_value = stored_int / 1000.0` is evaluated
- **THEN** results are `0.0`, `0.001`, `1.0`, `1.5`, `999.999` (float comparison with epsilon `0.000001`).

**AC-DETERM-03** — `boot_hash` consistency cross-platform | BLOCKING | Manual (multi-device)
- **GIVEN** the same `manifest.tres` deployed to iOS, Android, and Web HTML5
- **WHEN** each platform computes `boot_hash = SHA256(concat(sorted(paths) || sorted(content_hashes)))`
- **THEN** all three platforms produce byte-identical hash strings; logged at boot for verification.

**AC-DETERM-04** — `boot_hash` changes when any file changes | BLOCKING | Automated
- **GIVEN** a baseline boot_hash
- **WHEN** any single byte in any boot-phase `.tres` changes and manifest is regenerated
- **THEN** new `boot_hash` differs from baseline.

### 7. Web HTML5-Specific

**AC-WEB-01** — Initial bundle size | ADVISORY | Manual (build measurement)
- **GIVEN** a production Web HTML5 export
- **WHEN** the initial page load is measured (before streaming assets arrive)
- **THEN** initial download (`.wasm` + `.pck` + `.html` + `.js`) is **under 15 MB**; verify via browser DevTools Network tab on clean cache.

**AC-WEB-02** — Manifest-only discovery (no folder scan) | BLOCKING | Automated
- **GIVEN** a Web HTML5 build where direct filesystem enumeration is unavailable
- **WHEN** `ConfigLoader` initializes
- **THEN** discovers all config resources exclusively from `manifest.tres`; no `DirAccess.list_dir_*` calls in `ConfigLoader` code path (verified by static grep in CI).

**AC-WEB-03** — Retry exhaustion emits failure signal | BLOCKING | Automated
- **GIVEN** Web build where all 3 retries fail for a resource
- **WHEN** final retry times out
- **THEN** `ConfigLoader` emits `resource_load_failed(id)` signal, logs ID + retry count, loader is not hung.

**AC-WEB-04** — Manifest desync vs filesystem doesn't crash | BLOCKING | Manual
- **GIVEN** a Web deployment where a `.tres` listed in manifest is absent from server (simulated)
- **WHEN** game attempts to load that resource
- **THEN** logs WARN, returns `null` for that ID, game continues; no unhandled exception to player.

### Gate Summary

| Category | Criteria | BLOCKING | ADVISORY |
|---|---|---|---|
| Build-Time Validation | AC-BUILD-01 to 04 | 4 | 0 |
| Boot Phase Load | AC-BOOT-01 to 04 | 4 | 0 |
| Post-Boot Async Load | AC-ASYNC-01 to 03 | 2 | 1 |
| On-Demand Load | AC-ONDEMAND-01 to 04 | 4 | 0 |
| Schema Migration | AC-MIGRATE-01 to 03 | 3 | 0 |
| Cross-Platform Determinism | AC-DETERM-01 to 04 | 4 | 0 |
| Web HTML5-Specific | AC-WEB-01 to 04 | 3 | 1 |
| **Total** | **26** | **24** | **2** |

---

## Open Questions

Items that surfaced during design but were not fully resolved:

- **`BalanceConstants` singleton vs per-category file**. Current design: 1 singleton (`assets/data/balance/balance_constants.tres`). Pro: simple, one source. Con: at 100+ fields it becomes hard to navigate. *Owner*: systems-designer + economy-designer. *Resolve when*: authoring the Combat Simulation or Economy GDD.
- **Consider `@abstract` annotation (Godot 4.5+) for a base `ConfigResource` class**. Pro: DRY, common validation. Con: verbosity, `@abstract` has serialization caveats. *Owner*: godot-specialist. *Resolve when*: 1-day technical spike before implementing the first Resource type.
- **Pre-commit hook vs pre-export only for manifest generator**. Current: pre-export only. Risk: dev forgets to regenerate before commit. *Owner*: devops-engineer. *Resolve when*: CI/CD setup.
- **`uid_cache.tres` and `.gitignore`**. Godot's default gitignores `.godot/` but this system needs the cache committed. *Owner*: devops-engineer. *Resolve when*: project setup. Document in README + add CI check "uid_cache.tres exists in repo".
- **Tier 3 Live Service: server-downloaded configs**. MVP decision was "editor only" hot reload. Post-launch, downloading updated `BalanceConstants.tres` from server avoids app store review for balance changes. Requires cryptographic signing. *Owner*: technical-director + security-engineer. *Resolve when*: Tier 3 architecture review.
- **Schema versioning across multi-version jumps**. Currently sequential migrations (v1→v2, v2→v3...). If a save is at v1 and current is v5, 4 migrations run. Failure mid-chain handling? *Owner*: systems-designer. *Resolve when*: first real migration (likely Tier 2).
- **Performance budget testing (AC-BOOT-04, 500 ms target)**. Needs low-end Android device (Adreno 505 / Mali-G51 tier). Buy one, use Firebase Test Lab, or estimate from specs? *Owner*: qa-lead + producer. *Resolve when*: Tier 1 MVP soft-launch prep.

### ADRs This GDD Will Generate

To be written after this GDD via `/architecture-decision`:

- **ADR**: Folder structure for `assets/data/`
- **ADR**: Schema versioning + migration script convention
- **ADR**: Build-time manifest generation (replaces folder scan; vital for Web)
- **ADR**: Per-mille fixed-point integers for combat-critical values (cross-platform determinism)
- **ADR**: Resource UID cross-reference convention
