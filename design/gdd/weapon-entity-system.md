# Weapon Entity System

> **Status**: In Design
> **Author**: lucianodepalo-ai + Claude (systems-designer + game-designer agents)
> **Last Updated**: 2026-04-21
> **Implements Pillar**: Pillar 2 (Variabilidad emergente) — DIRECT · Pillar 4 (Meta emergente) — DIRECT · Pillar 3 (Sesión corta, profundidad infinita) — indirect · Pillar 1 (Estética primer impacto) — indirect
> **System #**: 4 — Core layer, MVP priority
> **High-Risk Flag**: YES — schema bottleneck (7+ downstream systems depend on this). Lock before feature work.

---

## Overview

**Weapon Entity System** es el sistema Core que define la identidad estructural del arma en *The Meta-Forge* — el esqueleto de datos que hace que una "espada forjada" no sea un ítem genérico sino un individuo irrepetible con historia. Opera en dos capas:

- **Capa de datos (infra)**: Define dos Resource classes complementarias. `WeaponArchetypeDefinition` es la plantilla inmutable — templates hard-coded en `.tres` que describen una **clase** de arma (Sword, Dagger, Hammer en MVP) con sus stats base, damage type, y allowed affix tiers. `WeaponInstance` es el individuo forjado — un Resource concreto `.tres` persistido en save con uid estable, que captura el ADN emergente de UNA combinación específica de materiales × archetype × ejecución de forja. Cada arma que el jugador posee es una `WeaponInstance` separada, rehidratable desde save, duplicable vía `duplicate_deep()` para variantes, y resolvible a un stat block integer-only para Combat Simulation cross-platform determinístico.

- **Capa de experiencia (player-facing)**: Este es el sistema donde **"las armas son el protagonista"** deja de ser tagline y pasa a ser mecánica. El jugador no colecciona héroes; colecciona armas con partida de nacimiento — cada `WeaponInstance` recuerda sus materiales madre, la firma del jugador que la creó, la fecha de su forja, y el conjunto de afijos únicos que emergieron de ESE acto de forja específico. Dos jugadores que usen los mismos materiales en el mismo archetype obtendrán **armas distintas**, porque la ejecución del minijuego de forja inyecta variabilidad acotada en el ADN. Las armas sobreviven al jugador: mientras no estás jugando, las mejores defienden tu ranking en el ladder asíncrono, y la comunidad las ve pelear en replays.

**MVP scope**: 3 archetypes (Sword / Dagger / Hammer), mapeados 1:1 a los 3 damage types del art bible §5.3 (slash / pierce / blunt) para que la silueta a 100px telegrafíe el matchup. Roster del jugador hasta 20 WeaponInstances (tuning knob). Campos ADN mínimos del schema cubren: archetype ref, stat block per-mille, affix slots, element affinity, firma del jugador, metadata de origen (materiales madre, forge_session_id).

**Sin este sistema**: no hay forja con resultado tangible (el forge escupe al aire), no hay combate simulado (no hay stat blocks que resolver), no hay PvP asíncrono (no hay snapshot que enviar al ladder), no hay colección ni meta emergente (no hay objeto que firmar ni que descubrir). Es **el bottleneck estructural del juego** — 7+ sistemas downstream (Affix, Hero/Wielder, Combat Sim, Replay, PvP Async, Matchmaking, Forge, Weapon Roster UI, Cosmetic Tier 3) importan directa o transitivamente el schema de este GDD. Cualquier cambio tardío acá cascada a todos ellos, por lo que el schema se **lockea acá antes que feature work**.

**Pillar alignment**:

- **Pilar 2 — Variabilidad emergente — DIRECTO**. El schema de `WeaponInstance` ES el espacio matemático de "no hay dos forjas iguales". Más slots, más rangos de magnitud, más campos ADN = más variabilidad.
- **Pilar 4 — Meta emergente — DIRECTO**. La comunidad descubre qué combinaciones archetype×element×affix-cluster dominan. Las builds sobreviven como `WeaponInstance` guardadas y peleando en ladder, no como descripciones teóricas.
- **Pilar 3 — Sesión corta, profundidad infinita — indirecto**. La "firma del jugador" y el roster persistente crean investment sin requerir tiempo (volvés al hub y tu arma ya peleó).
- **Pilar 1 — Estética primer impacto — indirecto**. El schema expone un `silhouette_icon_id` que el Weapon Roster UI resuelve a sprite 100px legible a simple vista (art bible §5.3 damage type telegraphing).

## Player Fantasy

A weapon is the moment a pile of materials stops being the player's and starts being the blade's. In that moment, the player signs it. Whatever name they type — into an empty field, not a pre-filled placeholder — is carved into the weapon's record forever. It appears on the stat screen, in the ladder readout, and, most importantly, on the loss screens of strangers the player will never meet. Every weapon carries a **legible birth**: the materials consumed, the grade of the forge minigame that shaped it, the date it came off the anvil, the rarity it rolled. The player can open any blade they have ever made and read it like a letter they wrote to themselves. Pride, not nostalgia — the record proves they were the one who did this.

Then the weapon leaves. The player sends it to the ladder to defend a position they are not present to defend, and the weapon fights **while they sleep**. In the morning it comes home with a record of matches the player can read like a letter home. The fantasy is not the weapon's power. It is the authorship — the particular satisfaction of knowing your name is carved into something that stood in rooms you'll never enter. The player is not the hero. The player is the one who stayed at the forge and signed the work well enough that it no longer needed them.

### Emotional beats

- **The first name.** The player finishes their first forge. The blade emerges and the game asks for a name. Not a pre-filled placeholder — an empty field, the cursor blinking. Whatever they type is carved into the weapon's record forever, including on the loss screens of strangers the player will never see.

- **The overnight letter home.** Player opens the game in the morning and finds their signed blade has fought five matches while they slept — won three, lost two. Each result is a line in a report the blade wrote for them. The weapon did this without them. The pride is the pride of a parent reading a school letter.

- **The re-read.** Player opens a weapon they forged three weeks ago and can still read its birth — materials, forge grade, date. The weapon remembers itself better than the player does. They didn't change the blade; *they* changed. The blade was waiting for them to catch up and notice what they'd done right.

### What this fantasy is NOT

- **NOT a gacha pull.** The weapon is not given by the house. It is **emitted** by the player's act. No "you got!", no jackpot audio, no rarity as the emotional event. The rarity card is how the blade is described, not why it matters.
- **NOT a cosmetic skin or flavor text.** The name is not decoration. It propagates through the ladder and is seen by strangers. Authorship has consequences.
- **NOT an MMO "legendary drop" fantasy.** A blade's worth is not its tier — it is the combination of the player's materials, the player's execution, and the player's name. A Common forged with intention outranks an Epic produced by accident.
- **NOT a wielded hero's weapon.** The player never swings it. The separation between the forger and the blade is the whole point.

### Tonal braid with Material System

Materials *breathe*; weapons *testify*. The pantry holds potential; the forge turns potential into evidence. Materials own anticipation ("held breath"); weapons own release ("the breath exhaled into an inscription"). One keeps time by restocking the shelf; the other keeps time by adding a new signed blade to the rack.

### Pillar mapping — 2 (Variabilidad) + 4 (Meta emergente)

The schema of a `WeaponInstance` **is** the substrate of Pillar 2: the legible birth — archetype × materials × forge-grade × affix cluster × element — is the mathematical space of variability made concrete as a readable object. And the **signature propagating** through other players' loss screens is how Pillar 4 becomes mechanical, not aspirational: the meta is what the community remembers by reading each other's blades. Names become reputation; reputations become the meta.

## Detailed Design

### C.1 — `WeaponArchetypeDefinition` Resource Schema

**Class**: `class_name WeaponArchetypeDefinition extends Resource` (annotated `@tool`). Stored in `assets/data/weapon_archetypes/`. Boot-phase load. Templates inmutables — nunca se duplican a save, nunca se modifican en runtime.

**Fields (14)**:

| Field | Type | `@export` | Default | Range | Description |
|---|---|---|---|---|---|
| `id` | `StringName` | ✓ | `&""` | non-empty, unique | Canonical lookup (`&"sword"`, `&"dagger"`, `&"hammer"`) |
| `display_name` | `String` | ✓ | `""` | 1–32 chars | Locale key or fallback |
| `damage_type` | `StringName` | ✓ | `&""` | `&"slash"` / `&"pierce"` / `&"blunt"` | Maps 1:1 a art bible §5.3 |
| `icon_path` | `String` | ✓ | `""` | valid `res://` path | Silueta 100px que telegrafía damage type |
| `attack_base` | `int` | ✓ | 0 | 1–500 | Base attack (integer; combat path) |
| `defense_base` | `int` | ✓ | 0 | 0–500 | Base defense |
| `speed_base` | `int` | ✓ | 0 | 1–500 | Base speed (initiative + dodge tiebreak) |
| `crit_chance_base` | `int` | ✓ | 0 | 0–1000 | Per-mille crit (`75` = 7.5%) |
| `dodge_base` | `int` | ✓ | 0 | 0–500 | Per-mille dodge, clamped 50% hard cap |
| `affix_slot_count` | `int` | ✓ | 3 | 1–6 | Número de slots para afijos |
| `affix_slot_tiers` | `Array[int]` | ✓ | `[2,3,4]` | each 1–5; `length == affix_slot_count` | Tier cap por slot (slot acepta afijos con tier ≤ ese valor) |
| `element_affinities` | `Dictionary` | ✓ | `{}` | keys = element StringNames; values = int per-mille | Override del default elemental; si vacío, `ElementDefinition` default aplica |
| `base_power_rating` | `int` | ✓ | 0 | 1–9999 | Contribución base del archetype a Matchmaking |
| `forge_primary_damage_type` | `StringName` | ✓ | `&""` | same set as `damage_type` | Materiales con afinidad a este dtype aportan 100% bonus; incompatibles 50% (ver Rule 6) |
| `schema_version` | `int` | ✓ | 1 | 1–99 | Incrementado en cambios de layout del schema |

**MVP archetype values (3)**:

| Campo | Sword (`&"sword"`) | Dagger (`&"dagger"`) | Hammer (`&"hammer"`) |
|---|---|---|---|
| `damage_type` | `&"slash"` | `&"pierce"` | `&"blunt"` |
| `attack_base` | 100 | 70 | 145 |
| `defense_base` | 80 | 45 | 110 |
| `speed_base` | 100 | 160 | 55 |
| `crit_chance_base` | 75‰ (7.5%) | 150‰ (15%) | 30‰ (3%) |
| `dodge_base` | 60‰ (6%) | 120‰ (12%) | 25‰ (2.5%) |
| `affix_slot_count` | 3 | 3 | 3 |
| `affix_slot_tiers` | `[2, 3, 4]` | `[2, 3, 4]` | `[2, 3, 4]` |
| `base_power_rating` | 500 | 480 | 520 |
| `forge_primary_damage_type` | `&"slash"` | `&"pierce"` | `&"blunt"` |

**Affix tier caps** (MVP uniform): Slot 0 ≤ Uncommon, Slot 1 ≤ Rare, Slot 2 ≤ Epic. Legendary afijos (tier 5) no existen en MVP (consistente con materiales). Diferenciación por archetype = Tier 2 tuning pass.

**Validation** (`_get_configuration_warnings()`): id empty, display_name empty, `affix_slot_tiers.size() != affix_slot_count`, tiers fuera de 1–5, damage_type fuera de {slash/pierce/blunt}, stats ≤ 0 ilegales, crit > 1000, dodge > 500, schema_version < 1, filename != id, `base_power_rating < 1`.

---

### C.2 — `WeaponInstance` Resource Schema

**Class**: `class_name WeaponInstance extends Resource`. Cada arma forjada es su propio `.tres` en `user://weapons/[instance_uid].tres`. Creada EXCLUSIVAMENTE por `ForgeSystem.create_weapon(...)`; nunca instantiable desde otro sistema.

**Fields (25)**:

| Field | Type | Default | Notes |
|---|---|---|---|
| `instance_uid` | `String` | `""` | UUID v4 generado en creation. Frozen. Globally unique. |
| `archetype_uid` | `String` | `""` | `uid://...` del `WeaponArchetypeDefinition` referenciado |
| `weapon_name` | `String` | `""` | Player-typed, **1–20 chars** UTF-8, no control chars. Seteado en transición FORGED_UNNAMED→NAMED |
| `player_signature` | `String` | `""` | Player-typed, **1–32 chars** UTF-8. Required antes del forge (no auto-fill). Frozen al inicio de FORGING |
| `forge_date` | `int` | 0 | Unix timestamp UTC (seconds since epoch) |
| `source_material_ids` | `Array[StringName]` | `[]` | Ordered list de los 12 MVP material IDs consumidos |
| `source_material_counts` | `Array[int]` | `[]` | Parallel a `source_material_ids` (length match obligatorio) |
| `forge_grade` | `int` | 0 | 0–100. Bandas nombradas: 0–19 Rough / 20–39 Fair / 40–59 Clean / 60–84 Masterwork / 85–100 Sublime |
| `forge_session_id` | `String` | `""` | UUID v4; link al Forge session log + Replay record |
| `random_seed` | `int` | 0 | int64 any. Deterministic RNG seed — Replay re-deriva afijos desde acá |
| `rarity` | `int` | 1 | 1–5 (computado por Rule 3 gate-based). Frozen. |
| `element_id` | `StringName` | `&"neutral"` | Uno de los 7 elements (computado por Rule 9 dominance). Frozen. |
| `attack_final` | `int` | 0 | Resolved via Rule 4. Frozen. |
| `defense_final` | `int` | 0 | Resolved. Frozen. |
| `speed_final` | `int` | 0 | Resolved. Frozen. |
| `crit_chance_final` | `int` | 0 | 0–1000 per-mille. Resolved. Frozen. |
| `dodge_final` | `int` | 0 | 0–500 per-mille. Resolved. Frozen. |
| `power_rating` | `int` | 0 | Frozen. Matchmaking lee este valor único. |
| `affix_slots` | `Array[Resource]` | `[]` | `Array[AffixInstance]`, length = `archetype.affix_slot_count`. Embedded via `duplicate_deep()` al forge. Frozen. |
| `state` | `int` | 0 | enum `WeaponState` (0–7). Ver C.4 |
| `equipped_by_hero_uid` | `String` | `""` | UUID v4 del HeroInstance equipando. Mutable post-NAMED. |
| `cosmetic_overlay_id` | `StringName` | `&""` | Tier 3 reserva (reskin feature). Mutable (cosmetic-only). |
| `pvp_snapshot_version` | `int` | 1 | Version del shape de serialización al ladder |
| `signature_propagated_at` | `int` | 0 | Unix timestamp (segundos). `0` = signature nunca propagada (pre-match). `> 0` = timestamp del match post-propagation. Mutable post-NAMED **solo por PvP Async System** al cierre de un match (write-once por match; monotónicamente creciente). Usado como observable para verificar Rule 7 (post-match-only propagation). |
| `schema_version` | `int` | 1 | Save migration version |

**PvP snapshot subset** — via `WeaponInstance.to_dict() -> Dictionary` (NOT `duplicate_deep()`; per gameplay-programmer audit, `to_dict()` es más portable y JSON-nativo sin dependencia de engine Resource):

| Incluido en snapshot | Excluido (queda en save local) |
|---|---|
| `instance_uid`, `archetype_uid`, `weapon_name`, `player_signature`, `rarity`, `element_id`, `attack_final`, `defense_final`, `speed_final`, `crit_chance_final`, `dodge_final`, `power_rating`, `affix_slots` (full array), `pvp_snapshot_version` | `source_material_ids/counts`, `forge_grade`, `forge_session_id`, `random_seed`, `forge_date`, `equipped_by_hero_uid`, `cosmetic_overlay_id` |

Rationale: reducir payload al backend y **no revelar la "receta" del jugador** en el ladder (la receta es privada del propietario — solo la ven en el detail view de su propia arma). Esto también bloquea scraping del meta por data leak del envelope.

---

### C.3 — Core Rules

**Rule 1 — Creation Authority.** Las `WeaponInstance` se crean EXCLUSIVAMENTE vía `ForgeSystem.create_weapon(archetype: WeaponArchetypeDefinition, materials: Dictionary, forge_grade: int, player_signature: String, weapon_name: String, seed: int) -> WeaponInstance`. Llamadas directas a `WeaponInstance.new()` desde otro sistema son violación de coding standards y deben triggerar `push_error` en debug builds.

**Rule 2 — Immutability Invariant.** Una vez que `state == NAMED` (state=2), los siguientes campos son permanentemente inmutables: `archetype_uid`, `player_signature`, `forge_date`, `source_material_ids`, `source_material_counts`, `forge_grade`, `forge_session_id`, `random_seed`, `rarity`, `element_id`, stats finales (`attack_final`..`dodge_final`), `power_rating`, `affix_slots`. Solo mutables: `state`, `equipped_by_hero_uid`, `cosmetic_overlay_id`. Intento de escritura a frozen fields en state≥2 debe triggerar `push_error()` y return sin efecto.

**Rule 3 — Rarity Gate Computation (determinístico).** Rarity NO es random. Se evalúa al forge como gates en orden descendente; el primer gate que matchea define rarity:

| Rarity | Gate (todos los AND deben cumplirse) |
|---|---|
| **5 Legendary** | `epic_count ≥ 2` AND `forge_grade ≥ 85` |
| **4 Epic** | (`epic_count ≥ 1` AND `grade ≥ 60`) OR (`rare_or_higher_count ≥ 2` AND `grade ≥ 75`) |
| **3 Rare** | (`rare_or_higher_count ≥ 1` AND `grade ≥ 40`) OR (`uncommon_or_higher_count ≥ 3` AND `grade ≥ 60`) |
| **2 Uncommon** | (`uncommon_or_higher_count ≥ 1` AND `grade ≥ 20`) OR (`common_count ≥ 3` AND `grade ≥ 60`) |
| **1 Common** | default floor (siempre matchea) |

Donde: `epic_count` = materials tier==4, `rare_or_higher_count` = materials tier≥3, etc. Formula formal en Sección D.

Ejemplos verificables:
- 2 Epic + 1 Rare + grade 90 → **Legendary** (gate 5 pasa)
- 3 Epic + grade 70 → **Epic** (gate 5 falla por grade; gate 4 primer-OR pasa)
- 2 Common + 1 Rare + grade 50 → **Rare** (gate 3 primer-OR pasa)
- 3 Common + grade 30 → **Common** (floor)

**Rule 4 — Stat Resolution (integer math, frozen).** Resolved stats computados al forge completion, frozen en el `.tres`:

`stat_final = archetype_base + floor(material_bonus_per_mille × archetype_base / 1000) + floor(grade_bonus_per_mille × archetype_base / 1000)`

con `grade_bonus_per_mille = forge_grade × 5` (Sublime grade 100 = 500‰ = +50% cap). `material_bonus_per_mille` es aggregate de `stat_bonuses[stat_axis]` de los materiales (field pendiente, append non-breaking al Material GDD; integer per-mille).

Contract para Combat: `WeaponInstance.get_combat_stats() -> Dictionary` devuelve `{attack, defense, speed, crit_chance, dodge}` como ints. **Combat Sim NUNCA re-deriva desde archetype o materiales** — lee los `_final` directamente. Integer-only path garantiza determinismo iOS/Android/Web.

**Rule 5 — Player Signature Required at Forge Init.** `ForgeSystem.create_weapon(...)` rechaza (retorna `null` + emite `forge_failed(reason)`) si `player_signature` está vacío o excede 32 chars. La firma es frozen al entrar a state `FORGING` y nunca se modifica después. **No hay auto-fill** desde profile ni default; siempre requiere prompt UI explícito antes del minijuego.

**Rule 6 — Forge Material Compatibility.** Materiales cuyo `damage_type_affinity` matchea `archetype.forge_primary_damage_type` aportan **full** `material_bonus_per_mille`. Incompatibles aportan `floor(bonus / 2)` (50% penalty). Mixed forges son legales — la tensión composicional ES la mecánica. Element dominance (Rule 9) es **independiente** de compatibility.

**Rule 7 — Cross-Reference Stability.** Todas las refs inter-Resource usan `uid://`, no paths: `archetype_uid` guarda `uid://...` del archetype `.tres`; `equipped_by_hero_uid` guarda UUID v4 del HeroInstance (no path). `affix_slots` embedded vía `duplicate_deep()` (Godot 4.5+) — no external uid refs. **Defensive pattern obligatorio**: código que resuelve `archetype_uid` a un `WeaponArchetypeDefinition` debe null-guard (archetype eliminado = "corrupted weapon" state en UI, skippeable en combat, nunca crash).

**Rule 8 — Roster Cap = 12 (no-purchasable).** `MAX_ROSTER_SIZE = 12`. Al hit del cap, ForgeSystem rechaza nuevas forjas (error "Roster Full") hasta que el jugador retire o borre un arma. **El cap NO es purchasable** — es la línea anti-P2W del sistema. **Punto de enforcement**: el cap se evalúa al **inicio** de `ForgeSystem.create_weapon(...)` (es decir, weapons en state `FORGING` SÍ cuentan hacia el cap desde el momento de iniciar la forja). Esto previene workarounds tipo "iniciar 13 forges simultáneas en background". Armas en state RETIRED no cuentan hacia el cap (pero al MVP Retired = funcionalmente Deleted; Retired Vault es Tier 2 feature). Weapons en state `ORPHANED` (ver C.4) tampoco cuentan hacia el cap (huérfanas son read-only, no-playable).

**Rule 9 — Element Dominance.** `element_id = argmax over elements E of sum(material.tier for material in consumed_materials where material.element == E)`. Tiebreak: primer-encontrado en input order. `&"neutral"` solo si TODOS los materiales son Neutral. Los 7 StringNames válidos: `&"fire"`, `&"water_ice"`, `&"earth"`, `&"wind_storm"`, `&"light"`, `&"dark"`, `&"neutral"`.

**Rule 10 — PvP Snapshot Versioning.** `WeaponInstance.to_dict()` stampa `pvp_snapshot_version = 1` al exportar. El backend rechaza snapshots con version debajo del floor soportado → cliente re-exporta (re-serializa desde el save local, nunca mutando el Resource). Source material history nunca viaja al ladder (privacidad de receta per Rule 10.B).

---

### States and Transitions

**Enum `WeaponState`** (stored as `int` en `WeaponInstance.state`):

| Value | State | Entry condition | Mutable en este state | Transitions Out |
|---|---|---|---|---|
| `0` | `FORGING` | ForgeSystem.create_weapon() inicia; materiales consumidos atomically | Ninguno (construyéndose) | → `FORGED_UNNAMED` al completar forge |
| `1` | `FORGED_UNNAMED` | Forge completa; DNA frozen; `player_signature` + `forge_date` set | `weapon_name` (player tipeándolo) | → `NAMED` al submit weapon_name válido (1–20 chars) |
| `2` | `NAMED` | Player submit weapon_name; arma entra al roster | `equipped_by_hero_uid`, `cosmetic_overlay_id`, `state` | → `EQUIPPED` si hero equipa; → `RETIRED` por player action; → `DELETED` por player action |
| `3` | `EQUIPPED` | HeroSystem.equip(); `equipped_by_hero_uid` set | `equipped_by_hero_uid`, `cosmetic_overlay_id`, `state` | → `DEFENDING` si hero va a PvP defense slot; → `NAMED` si hero unequip |
| `4` | `DEFENDING` | Hero asignado a defense roster del PvP | `cosmetic_overlay_id`, `state` | → `EQUIPPED` al removerse de defense; **unequip PROHIBIDO mientras DEFENDING** |
| `5` | `RETIRED` | Player retira; libera roster slot | `state` | → `DELETED` (no resurrection path) |
| `6` | `DELETED` | Player confirma delete | Ninguno | Terminal. Save purga el `.tres` en próximo save write. No undo. |
| `7` | `ORPHANED` | Load-time auto-transition: `ResourceUID.get_id_path(archetype_uid)` retorna null (archetype .tres fue borrado o movido sin migración) | `state` (→ RETIRED/DELETED solamente) | → `RETIRED` o `DELETED` por player action. **No puede** → FORGING/EQUIPPED/DEFENDING. Read-only para inspection. No cuenta hacia MAX_ROSTER_SIZE. |

**Transition guards**:
- FORGED_UNNAMED → NAMED requiere `len(weapon_name.strip_edges()) >= 1` AND `len(weapon_name) <= 20` AND no control chars
- DEFENDING bloquea unequip para prevenir retirar el arma defensiva entre ladder checks (PvP Async System maintiene el flag)
- RETIRED weapons permanecen en save (browsable en Tier 2 Retired Vault); al MVP equivalente funcional a DELETED para roster purposes
- DELETED es soft en sesión; SaveSystem purga el `.tres` en próximo save write

---

### Interactions with Other Systems

| System | Direction | Contract (signatures cuando aplica) | Owner |
|---|---|---|---|
| **Data-Driven Config** | WeaponArchetypeDefinition es Resource data-driven | `ResourceLoader.load("uid://...") -> WeaponArchetypeDefinition`. Boot-phase load. Campo semántico = este GDD; loader + folder contract = config GDD | Shared |
| **Material System** | Mat → Forge → Weapon | `MaterialSystem.consume_materials(requirements: Dictionary) -> bool` atomic. `MaterialDefinition.stat_bonuses: Dictionary[StringName,int]` (per-mille) + `damage_type_affinity: StringName` (schema extensions pendientes — non-breaking append al Material GDD) | Mat owns consume + fields; este GDD owns cómo se aplican (Rule 4+6) |
| **Forge System** (siguiente GDD) | Forge → Weapon (creator) | `ForgeSystem.create_weapon(archetype, materials, forge_grade, player_signature, weapon_name, seed) -> WeaponInstance`. Forge llama a Material consume → Affix roll → Rarity gate → Stat resolve → Save. Anti-spam policy (grade degradation) lives en Forge GDD | Forge owns minigame + grade output; este GDD owns el schema que populate |
| **Save/Load** (provisional, undesigned) | Save ↔ Weapon | `ResourceSaver.save()` + `ResourceLoader.load()` por-instancia; path `user://weapons/[instance_uid].tres`. Roster índice: `Array[String]` de paths. Null-guard obligatorio al resolve archetype_uid (corrupted weapon state). Migration vía `schema_version` | Save GDD owns migration + backend-swappable storage; este GDD owns schema + version increment policy |
| **Combat Simulation** | Weapon → Combat (read-only) | `WeaponInstance.get_combat_stats() -> Dictionary[StringName, int]` devuelve `{attack, defense, speed, crit_chance, dodge}` como ints. Combat NUNCA re-deriva | Este GDD owns el contract; Combat GDD owns cómo se consume en damage formula |
| **Replay System** | Forge → Replay (snapshot embed) | En record time, ForgeSystem embebe la instance completa (incluyendo `random_seed` + `forge_session_id`) en el replay record. Replay re-deriva rolls de afijos desde seed — determinismo garantizado | Replay GDD owns record format; este GDD owns campos required para determinismo |
| **PvP Async System** | Weapon → PvP (export) | `WeaponInstance.to_dict() -> Dictionary` → JSON → backend. `pvp_snapshot_version` stamp. Version floor reject = cliente re-export desde save local | Este GDD owns to_dict(); PvP GDD owns transmisión + version floor policy |
| **Matchmaking** | Weapon → MM (read-only) | `WeaponInstance.power_rating: int` (frozen). MM GDD owns formula de bracket assignment que consume este valor | Este GDD owns power_rating compute + freeze; MM owns bracket logic |
| **Hero/Wielder** | Hero ↔ Weapon (equip pointer) | `HeroSystem.equip_weapon(hero_uid: String, weapon: WeaponInstance) -> bool` setea `equipped_by_hero_uid` + transita state a EQUIPPED. `HeroSystem.unequip_weapon(hero_uid)` clarea y vuelve a NAMED | Hero GDD owns hero side; este GDD owns pointer field + state guards |
| **Affix/Mutation** (siguiente después de Forge) | Affix → Weapon (frozen slots) | `AffixSystem.roll_affixes(archetype, materials, grade, seed) -> Array[AffixInstance]` llamado por ForgeSystem. Result `duplicate_deep()` → `affix_slots`. Post-NAMED: Affix READS slots pero NUNCA escribe. Roll es **bounded RNG seeded por seed** — la ÚNICA RNG surface del sistema completo | Affix GDD owns AffixInstance schema + roll pools (element-filtered); este GDD owns slot count + tier caps + freeze invariant |
| **Hub HUD** | Weapon → HUD (read-only display) | Lee `weapon_name`, `rarity`, `element_id`, `archetype.damage_type`, `archetype.icon_path`, `state`. **`power_rating` NO visible en HUD** (anti-optimization meta — jugadores deben sentir balance via match quality, no leer stats). Silueta 100px telegrafía damage type | UI owns HUD scene; este GDD owns campos canónicos de display |
| **Weapon Roster UI** | Weapon[] → Roster browser | Lee `weapon_name`, `player_signature`, `rarity`, `element_id`, `forge_date`, archetype info. Sort/filter. **MAX_ROSTER_SIZE=12** enforced en display + forge gate | UI owns scene; este GDD owns fields + cap constant |
| **PvP Post-Match UI** (signature propagation) | Weapon snapshot → Loss/Victory + Replay viewer | Mini-card jerarquía: archetype silhouette 100px (PRIMARY) → `weapon_name` (line 2) → `player_signature` (line 3, smaller) → forge_grade glyph + element icon (badges bottom-right). **NO `power_rating` visible**. **NO pre-match visibility** (evita reputation-avoidance meta) | UI owns scene; este GDD owns propagation policy (post-match only, qué fields) |

## Formulas

Seis fórmulas conforman el camino computacional del sistema. **Todas integer-only** salvo `expected_weapons_per_session` (análytica, no combat). Floats PROHIBIDOS en el combat path — garantiza determinismo iOS/Android/Web.

### Formula 1 — Rarity Gate (determinístico, no random)

La rarity de una `WeaponInstance` NO es random; se evalúa como 5 gates en orden descendente. El primero que matchea define rarity. (Ver C.3 Rule 3 para el fast-read.)

```
rarity = 5  if epic_count >= 2 AND forge_grade >= 85
rarity = 4  if (epic_count >= 1 AND grade >= 60) OR (rare_or_higher_count >= 2 AND grade >= 75)
rarity = 3  if (rare_or_higher_count >= 1 AND grade >= 40) OR (uncommon_or_higher_count >= 3 AND grade >= 60)
rarity = 2  if (uncommon_or_higher_count >= 1 AND grade >= 20) OR (common_count >= 3 AND grade >= 60)
rarity = 1  otherwise  (default floor)
```

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `common_count` | c1 | int | 0–12 | Cantidad de materiales consumidos con tier==1 |
| `uncommon_or_higher_count` | c2+ | int | 0–12 | Materials con tier≥2 |
| `rare_or_higher_count` | c3+ | int | 0–12 | Materials con tier≥3 |
| `epic_count` | c4 | int | 0–12 | Materials con tier==4 |
| `forge_grade` | g | int | 0–100 | Execution quality del Forge minigame |
| `rarity` | r | int | 1–5 | 1=Common, 2=Uncommon, 3=Rare, 4=Epic, 5=Legendary |

**Output Range**: 1–5. Legendary (5) requiere compromiso genuino (2+ Epic materials + Sublime grade). Common (1) es garantía de "toda forja produce algo".

**Ejemplo 1**: 2 Epic + 1 Rare + grade 90 → epic_count=2, grade=90 → gate 5 pasa → **Legendary**.
**Ejemplo 2**: 3 Epic + grade 70 → gate 5 falla (grade<85); gate 4 primer-OR pasa (epic≥1 AND grade≥60) → **Epic**.
**Ejemplo 3**: 2 Common + 1 Rare + grade 50 → gate 3 primer-OR pasa (rare_or_higher≥1 AND grade≥40) → **Rare**.
**Ejemplo 4**: 3 Common + grade 30 → todos los gates >1 fallan → **Common** (floor).

---

### Formula 2 — Grade Bonus Per-Mille (linear)

Convierte `forge_grade` (0–100) a bonus per-mille aplicado sobre stats base del archetype.

`grade_bonus_per_mille = forge_grade × 5`

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `forge_grade` | g | int | 0–100 | Execution quality del Forge minigame |
| `grade_bonus_per_mille` | gbpm | int | 0–500 | Bonus per-mille aplicado a cada stat axis |

**Output Range**: 0–500 (0% a +50% del archetype base). Rough grade aporta ~0–10%, Sublime grade (85–100) aporta +42.5% a +50%.

**Ejemplo**: forge_grade=72 → gbpm = 360 → aplicado a attack_base 100 (Sword) → +36 bonus de grade.

---

### Formula 3 — Material Bonus Aggregation (per stat axis)

Suma los aportes per-mille de todos los materiales consumidos a un stat axis específico, weighted por tier + count, con multiplier de compatibility.

```
material_bonus_aggregate[axis] = sum over all consumed materials m of:
    m.stat_bonuses[axis] × m.count × tier_weight[m.tier] × compatibility_multiplier(m, archetype) / 1000

where
    tier_weight[1]=1, tier_weight[2]=2, tier_weight[3]=4, tier_weight[4]=8
    compatibility_multiplier(m, archetype) = 1000  if m.damage_type_affinity == archetype.forge_primary_damage_type
                                             500  otherwise   (50% penalty)
```

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `m.stat_bonuses[axis]` | bpm | int (per-mille) | 0–100 | Aporte base per-mille del material al stat axis (field pendiente en Material GDD, non-breaking append) |
| `m.count` | n | int | 1–999 | Cantidad consumida de este material específico |
| `tier_weight[m.tier]` | tw | int | 1 / 2 / 4 / 8 | Peso exponencial por tier — invertir en materiales más raros rinde más |
| `compatibility_multiplier` | cm | int (per-mille) | 500 o 1000 | 100% si compatible, 50% si incompatible |
| `material_bonus_aggregate[axis]` | Maxis | int (per-mille) | 0–3000 típico, sin cap dura | Aporte total per-mille al stat axis; consumido por Formula 4 |

**Output Range**: típico 0–3000 per-mille (0%–300% bonus). En casos extremos puede exceder — Formula 4 stat_final clamp a 9999 absorbe el overflow. Intermediate integer math con división al final para preservar per-mille units.

**Ejemplo**: Sword forge con 2 saltfire_resin (Uncommon Fire, NO compatible con slash → cm=500, asumiendo `stat_bonuses[attack]=40‰`) + 1 iron_root (Common Neutral, NO compatible → cm=500, `stat_bonuses[attack]=20‰`):

```
from saltfire_resin: 40 × 2 × tier_weight[2]=2 × cm=500 = 80000
from iron_root:      20 × 1 × tier_weight[1]=1 × cm=500 = 10000
sum before divide:                                        90000
material_bonus_aggregate[attack] = 90000 / 1000 = 90 per-mille
```

→ aplicado en Formula 4 a attack_base 100 = +9 attack points.

**Nota de ownership**: `stat_bonuses` y `damage_type_affinity` son **fields nuevos pendientes** en el schema de `MaterialDefinition` — non-breaking append (safe default si ausente: `{}` y `&""` respectivamente). Coordinación con Material GDD en Section F.

---

### Formula 4 — Stat Final Resolution (per stat axis, frozen at forge)

Integración de archetype base + material bonus + grade bonus. Frozen al forge completion:

```
stat_final = archetype_base
           + floor(material_bonus_aggregate[axis] × archetype_base / 1000)
           + floor(grade_bonus_per_mille × archetype_base / 1000)
```

clamped al rango `[1, 9999]` (dodge y crit_chance además clampean a sus caps específicos: 500‰ y 1000‰).

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `archetype_base` | b | int | 1–500 (500 hard cap) | Stat base del archetype para el axis |
| `material_bonus_aggregate[axis]` | Maxis | int (per-mille) | 0–3000 tuning typical | Output de Formula 3 |
| `grade_bonus_per_mille` | gbpm | int (per-mille) | 0–500 | Output de Formula 2 |
| `stat_final` | sf | int | 1–9999 (clamped) | Frozen en el WeaponInstance |

**Output Range**: `stat_final ∈ [1, 9999]`. Common forge típico (3 Commons + grade 40): `≈ 100 + 4 + 20 = 124`. Legendary forge (2 Epic + 1 Rare + grade 95): `≈ 100 + 150 + 47 = 297`.

**Ejemplo** (Sword attack, 2 saltfire_resin + 1 iron_root, grade 70):
- `archetype_base = 100`
- `material_bonus_aggregate[attack] = 90` (Formula 3 ejemplo)
- `grade_bonus_per_mille = 350` (Formula 2 → 70 × 5)
- `attack_final = 100 + floor(90×100/1000) + floor(350×100/1000) = 100 + 9 + 35 = 144`

Clamped result: 144 (dentro de [1, 9999]).

---

### Formula 5 — Element Dominance (argmax, tiebreak by input order)

Determina `element_id` emergente de la composición de materiales.

```
element_id = argmax over E in 7_elements of:
                sum(m.tier for m in consumed_materials where m.element == E)

tiebreak: first-encountered element wins (by order in source_material_ids)

special case: element_id = &"neutral" iff all consumed materials are Neutral
```

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `E` | E | StringName | uno de 7 elements | Un elemento del catálogo |
| `m.tier` | t | int | 1–4 | Tier del material individual |
| `m.element` | e | StringName | uno de 7 | Elemento del material (de MaterialDefinition) |
| `element_id` | eid | StringName | uno de 7 | Elemento dominante emergente |

**Output Range**: uno de `&"fire"`, `&"water_ice"`, `&"earth"`, `&"wind_storm"`, `&"light"`, `&"dark"`, `&"neutral"`. Nunca vacío — Neutral es fallback garantizado.

**Ejemplo 1**: 2 Ash Shard (Fire, tier 1) + 1 Cinder Core (Fire, tier 3) → Fire=5 → **`&"fire"`**.
**Ejemplo 2**: 1 Ash Shard (Fire, 1) + 1 Void Mote (Dark, 2) + 1 Frost Petal (Water/Ice, 1) → Dark=2, Fire=1, Water/Ice=1 → **`&"dark"`**.
**Ejemplo 3**: 1 Frost Petal (Water/Ice, 1) + 1 Clay Knot (Earth, 1) → tie → input order (Frost Petal primero) → **`&"water_ice"`**.
**Ejemplo 4**: 3 Iron Root (Neutral, 1) → **`&"neutral"`** (only-Neutral fallback).

---

### Formula 6 — Power Rating (weighted sum, integer-safe)

Single value que Matchmaking lee para bracket assignment. Frozen al forge.

```
power_rating = archetype.base_power_rating
             + floor((attack_final + defense_final + speed_final) / 3)
             + floor(crit_chance_final / 5)
             + floor(dodge_final / 5)
             + rarity × 50
             + sum over affix_slots s of (s.tier × 30)    [0 if slot is null]
```

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `archetype.base_power_rating` | bpr | int | 1–9999 | Aporte base del archetype (Sword 500 / Dagger 480 / Hammer 520) |
| `attack_final`, `defense_final`, `speed_final` | afinal,dfinal,sfinal | int | 1–9999 each | Formula 4 outputs |
| `crit_chance_final`, `dodge_final` | cfinal,dofinal | int (per-mille) | 0–1000 / 0–500 | Resolved per-mille values |
| `rarity` | r | int | 1–5 | Formula 1 output |
| `affix_slots[i].tier` | at_i | int | 0–5 (0 if null) | Tier del afijo en slot i |
| `power_rating` | pr | int | ~500–15000 typical | Frozen en WeaponInstance |

**Output Range**: `power_rating ∈ [~500, ~15000]` en rango de juego típico. Piso ≈ base_power_rating (~480–520); techo ≈ Legendary forge con 3 Epic-tier afijos + stats máximos. Los pesos (×50 rarity, ×30 affix_tier, ÷5 crit/dodge, ÷3 stat-avg) son **tuning knobs de Matchmaking GDD** — re-expuestos allá.

**Ejemplo** (Sword Rare forge: attack=152, defense=130, speed=140, crit=75, dodge=60, rarity=3, 2 affix slots filled tier-2 + tier-3, 1 null):
```
pr = 500 + floor((152+130+140)/3) + floor(75/5) + floor(60/5) + 3×50 + (2×30 + 3×30 + 0)
   = 500 + 140 + 15 + 12 + 150 + 150
   = 967
```

**Nota de ownership**: Este GDD owns el **contract** (campo `power_rating: int` frozen al forge, único valor que Matchmaking lee). La fórmula específica de pesos es MVP default; Matchmaking GDD puede proponer tuning sin romper el schema.

## Edge Cases

Todos los casos inusuales del ciclo de vida de una `WeaponInstance` con resolución explícita. Organizados por subsistema.

### E.1 — Creation / Forge

- **E1.1** — **Si `ForgeSystem.create_weapon()` recibe `materials = {}`** (zero materiales): rechazar con `forge_failed("materials_empty")`. No se crea la WeaponInstance. No se consumen materiales. La forja no se puede iniciar.
- **E1.2** — **Si `player_signature` está vacío o excede 32 chars**: rechazar con `forge_failed("invalid_signature")` (Rule 5). La firma es pre-condition no-default.
- **E1.3** — **Si algún material en `materials` requirements no existe en el catálogo**: rechazar con `forge_failed("unknown_material_id")`. No consumir parciales. Valida contra MaterialSystem antes de llamar `consume_materials`.
- **E1.4** — **Si `MaterialSystem.consume_materials(requirements)` retorna `false`** (stock insuficiente detectado atomically): rechazar con `forge_failed("insufficient_materials")`. El inventario permanece intacto (atomicidad de consume). Player UI debe refrescar.
- **E1.5** — **Si roster está lleno (12/12)**: rechazar con `forge_failed("roster_full")` ANTES de consumir materiales. UI presenta prompt "Retire or delete a weapon to forge a new one". No hay retirada automática.
- **E1.6** — **Si `forge_grade` cae fuera de [0, 100]** (bug en Forge minigame): clamp al rango `[0, 100]` antes de usar en fórmulas 1/2. Log `push_warning` en debug.
- **E1.7** — **Si `archetype` parameter es null** (archetype eliminado del catálogo entre load y forge): rechazar con `forge_failed("archetype_missing")`. No consumir materiales.

### E.2 — Immutability violations

- **E2.1** — **Si código escribe a un frozen field en state >= NAMED** (ej: `weapon.attack_final = 999`): `push_error` en debug; en release build el write se ignora silenciosamente (no crash, no corruption). Coding standards rule violated; caught by unit tests.
- **E2.2** — **Si weapon_name excede 20 chars o contiene control chars al submit**: bloquear la transición FORGED_UNNAMED → NAMED. UI presenta error "Weapon name must be 1-20 characters, plain text". El arma queda en FORGED_UNNAMED hasta submit válido.
- **E2.3** — **Si player intenta renombrar un arma ya en state NAMED** (bug en UI que permite re-editar): rechazar silenciosamente en setter; `push_error` en debug. weapon_name es frozen post-NAMED.

### E.3 — Archetype / UID resolution

- **E3.1** — **Si `archetype_uid` resuelve a `null`** al load (archetype `.tres` eliminado o uid_cache corrupto): la weapon entra en estado visual "Corrupted" en la UI (icono de warning, `power_rating = 0`, no equipable). La weapon NO se borra automáticamente — el jugador decide si elimina o si espera hotfix. Log a telemetry (Tier 2).
- **E3.2** — **Si `WeaponInstance.archetype_uid` está vacío** (`""`): tratar igual a E3.1 — Corrupted state. Save migration debe flaggearlo.
- **E3.3** — **Si un patch cambia `WeaponArchetypeDefinition.attack_base` después del forge**: las WeaponInstances existentes NO se recomputan — sus stats están frozen. Nuevas forjas usan el nuevo base. Design intencional: la weapon es un snapshot de las reglas al momento de forge.

### E.4 — Formulas — edge inputs

- **E4.1** — **Si `material_bonus_aggregate` overflow hypothetical** (suma de bonuses × counts × tier_weights excede int64): no factible en MVP (cap de stack 999 × max tier_weight 8 × max bonus ~100‰ × cm 1000 = ~800M per material, muy por debajo de int64). Documentado explícitamente para evitar ansiedad de overflow futura. Si en Tier 2 materials ganan más axes o cambia el schema, re-verificar.
- **E4.2** — **Si todos los materiales son incompatibles con el archetype**: `cm=500` para todos → material_bonus_aggregate × 0.5. El arma queda estadísticamente débil pero válida. Rarity gate aún puede producir Rare/Epic/Legendary si la composición de tiers lo justifica (compatibility no afecta rarity). Design: "forja con materiales inadecuados te da un arma con historia débil pero única".
- **E4.3** — **Si `stat_final` computado < 1** (bug o min-bound violation): clamp a 1 (piso absoluto, weapon nunca tiene 0 attack). Si `stat_final > 9999`: clamp a 9999. Log `push_warning` en ambos casos.
- **E4.4** — **Si todos los materiales son Neutral tier 1**: rarity=1 Common (floor); element_id=`&"neutral"`; stat_final ≈ archetype_base + ~20% grade bonus típico. Arma válida, legible como "first forge" arquetípica.

### E.5 — State transitions

- **E5.1** — **Si player intenta equipar una weapon en state DEFENDING a otro hero**: rechazar. Weapon en DEFENDING no es equipable a nuevo hero sin que primero PvP Async la remueva de defense.
- **E5.2** — **Si player intenta retirar weapon en state DEFENDING**: rechazar con UI prompt "Remove from defense roster first". PvP Async System owns la remoción; Weapon System solo valida el state.
- **E5.3** — **Si hero equipado con weapon es eliminado del roster (future feature)**: la weapon vuelve automáticamente a state NAMED; `equipped_by_hero_uid = ""`. HeroSystem emite signal, WeaponSystem suscribe y actualiza state.
- **E5.4** — **Si player borra una weapon en state EQUIPPED**: no permitido directamente — UI debe forzar unequip primero. Si un path bypassa UI (debug/cheat), HeroSystem debe recibir la notificación y limpiar `equipped_weapon` pointer; el arma pasa a DELETED.
- **E5.5** — **Si dos forjas concurrentes intentan alcanzar roster cap** (race condition hypothetical): no aplica — Forge es single-threaded en el juego (el minijuego es blocking). No hay concurrencia real. Si GDScript async/multi-session en Tier 3 introduce esto, revisitar.

### E.6 — PvP snapshot / serialization

- **E6.1** — **Si backend rechaza snapshot por `pvp_snapshot_version < floor_minimum`**: cliente re-exporta `to_dict()` del save local y retransmite. Si falla dos veces, mostrar error UI "Update required" y bloquear PvP hasta update del cliente.
- **E6.2** — **Si weapon se elimina localmente mientras su snapshot está defendiendo en el ladder**: el snapshot en backend sigue viviendo (es copia, no referencia). El player puede borrar su weapon local sin afectar partidas en curso. Al próximo sync, PvP Async le avisa "defense weapon no longer in roster, assign replacement".
- **E6.3** — **Si JSON serialization de `affix_slots` contiene un null element** (slot vacío): serializar como `null` literal en JSON. Deserialización en backend debe tratar null como "slot vacío", no crash.

### E.7 — Data corruption / recovery

- **E7.1** — **Si save contiene una weapon con `schema_version` superior al código del cliente**: cliente muestra "This save was made with a newer version of the game. Please update." y bloquea el load. No corrupción automática.
- **E7.2** — **Si save contiene una weapon con `schema_version` anterior**: SaveSystem ejecuta migration sequence; cada migration transformer `Save.migrate_weapon_v[N]_to_v[N+1](weapon)`. Si migration falla (datos inválidos), la weapon se marca Corrupted (ver E3.1).
- **E7.3** — **Si `.tres` del weapon está corrupto** (parse error): SaveSystem logea el path, skipea esa weapon en el load, muestra "One weapon could not be loaded" en UI. El jugador pierde esa weapon — documentar esto claramente en la sección Save/Load del GDD futuro.

## Dependencies

Weapon Entity System es un **schema bottleneck** con 7+ dependientes directos. Cada contrato aquí definido debe ser respetado por sistemas upstream y expuesto a los downstream.

### F.1 Upstream Dependencies (sistemas de los que DEPENDE)

#### F.1.1 Data-Driven Config (HARD) ✓ designed
- **GDD**: [data-driven-config.md](./data-driven-config.md)
- **Qué provee**: Patrón Custom Resource + convención per-mille + StringName IDs + `uid://` cross-refs + build-time manifest + `@tool` validation + schema versioning.
- **Qué consume Weapon Entity**:
  - Extiende `Resource` para `WeaponArchetypeDefinition` y `WeaponInstance`.
  - Convención per-mille para todos los campos `*_per_mille`, `crit_per_mille`, `dodge_per_mille`, etc.
  - `StringName` para `archetype_id`, `stat_axis`, `element_id`, `damage_type`.
  - `uid://` referencias: `archetype_uid` (WeaponInstance → WeaponArchetypeDefinition), `equipped_by_hero_uid`.
  - `schema_version: int` field en ambos tipos (migration trigger).
- **Contrato**:
  - Archetypes se cargan via build-time manifest (ver data-driven-config §4.3), NO via `DirAccess`.
  - Todas las fórmulas usan integer-only arithmetic.
  - Null-guard en `ResourceUID` lookups: si `archetype_uid` resuelve a null, `WeaponInstance.is_orphaned() == true` (ver Edge Case E3.2).

#### F.1.2 Material System (HARD) ✓ designed
- **GDD**: [material-system.md](./material-system.md)
- **Qué provee**: 12 MVP materials con `tier`, `element`, `rarity`, `stat_bonuses` (requested extension), `damage_type_affinity` (requested extension).
- **Qué consume Weapon Entity**:
  - `MaterialDefinition.tier` → input a Fórmula #1 (Rarity Gate) y Fórmula #3 (Material Bonus Aggregation via `tier_weight`).
  - `MaterialDefinition.rarity` → input a Fórmula #1 (Rarity Gate: conteo por tier de rareza).
  - `MaterialDefinition.element` → input a Fórmula #5 (Element Dominance).
  - `MaterialDefinition.stat_bonuses[axis]` → input a Fórmula #3 (Material Bonus Aggregation).
  - `MaterialDefinition.damage_type_affinity` → determina `compatibility_multiplier` en Fórmula #3 (vs `archetype.damage_type`).
- **Schema extensions requested** (non-breaking; a aplicar en Material System retrofit):
  1. `stat_bonuses: Dictionary[StringName, int]` — mapa `stat_axis → per_mille bonus`. Campos válidos de `stat_axis`: `&"attack"`, `&"defense"`, `&"speed"`, `&"crit"`, `&"dodge"`. Default `{}` (material no aporta stat bonus).
  2. `damage_type_affinity: StringName` — uno de `&"slash"`, `&"pierce"`, `&"blunt"`, o `&""` (sin afinidad). Default `&""`.
- **Contrato**:
  - `WeaponInstance.input_materials_snapshot: Array[Dictionary]` congela `{material_id, count, tier, rarity, element, stat_bonuses_snapshot}` al momento de forja — la instance es DNA inmutable.
  - Si un `MaterialDefinition` cambia post-forja (balance patch), los weapons existentes NO recomputan — usan el snapshot (ver Edge Case E3.3).

#### F.1.3 Save/Load & Persistence (HARD) — provisional, not yet designed
- **GDD**: pending (System #2, MVP Foundation)
- **Qué provee (expected contract)**: Serialization de Resources a `user://`, load on app start, migration hooks en `schema_version` mismatch.
- **Qué consume Weapon Entity**:
  - `WeaponInstance` se guarda como `.tres` individual en `user://weapons/[instance_uid].tres`.
  - `PlayerProfile.weapon_roster: Array[String]` (lista de `uid://` strings) se persiste; cada weapon es load-on-demand.
  - Migration script on `schema_version` mismatch (ver Edge Case E7.2).
- **Provisional assumption**: El Save/Load GDD debe permitir guardar N archivos Resource individuales (no monolítico). Si esto cambia, revisitar F.1.3.
- **Riesgo**: Save/Load aún no designed — flagged en Open Questions.

#### F.1.4 Affix System (HARD) — not yet designed (System #10, MVP Core)
- **GDD**: pending
- **Qué provee**: `AffixDefinition` resources + `AffixSystem.roll_affixes(seed, slot_count, slot_tiers, rarity) -> Array[AffixInstance]`.
- **Qué consume Weapon Entity**:
  - `WeaponInstance.rolled_affixes: Array[AffixInstance]` — poblado por AffixSystem al momento de forja usando `forge_session_id` como seed (RNG determinista, replayable).
  - `rolled_affixes` es frozen post-NAMED state.
- **Contrato**:
  - AffixSystem NUNCA muta un `WeaponInstance` existente; solo popula en creación.
  - Affix tiers `[2, 3, 4]` de `affix_slot_tiers` en MVP archetypes son los valores a usar.
- **Provisional assumption**: AffixInstance será sub-Resource con `effect_id`, `tier`, `magnitude_per_mille`. Si el design final difiere, revisitar.

### F.2 Downstream Dependents (sistemas que DEPENDEN)

#### F.2.1 Forge System (HARD) — not yet designed (System #16, MVP Feature — GDD #4 de prototype-critical)
- **Qué necesita**:
  - Crear `WeaponInstance` via `WeaponInstance.forge(archetype, materials, forge_grade, forge_session_id) -> WeaponInstance`.
  - Transitioning `FORGING → FORGED_UNNAMED` via `WeaponInstance.complete_forge()`.
  - Naming hook: `WeaponInstance.name_weapon(name, signature) -> void` que transitiona a `NAMED` y lock schema.
- **Interfaz expuesta**: `WeaponInstance.forge()`, `.complete_forge()`, `.name_weapon()`, state machine transitions (ver Sección C.4).

#### F.2.2 Combat Simulation (HARD) — not yet designed (System #13, MVP Core)
- **Qué necesita**:
  - Read-only acceso a stats finales via `WeaponInstance.get_combat_stats() -> Dictionary`.
  - NUNCA re-deriva stats; siempre lee los campos `*_final` congelados.
- **Interfaz expuesta**: `WeaponInstance.get_combat_stats()` retorna `{attack, defense, speed, crit_per_mille, dodge_per_mille, damage_type, element, affixes}`.
- **Contrato determinista**: Combat Sim recibe solo integers; no floats en hot path.

#### F.2.3 Hero/Wielder System (HARD) — not yet designed (System #11, MVP Core)
- **Qué necesita**:
  - Equipar weapon via `Hero.equip_weapon(weapon_uid)`; setea `WeaponInstance.equipped_by_hero_uid`.
  - Unequip via `Hero.unequip_weapon()`; limpia `equipped_by_hero_uid = ""`.
  - `weapon_uid → Hero` lookup reciprocidad (Hero.equipped_weapon_uid espeja).
- **Interfaz expuesta**: Campos mutables `state` + `equipped_by_hero_uid` (los únicos dos mutables post-NAMED).
- **Edge Case coverage**: E5.2 (weapon equipped cuando hero muere en combate) — Hero GDD debe limpiar referencia.

#### F.2.4 Replay System (HARD) — not yet designed (System #17, MVP Feature)
- **Qué necesita**:
  - Read-only snapshot de `WeaponInstance` al momento del combate.
  - Determinismo: mismo weapon + mismo seed + mismo enemy = mismo resultado.
- **Interfaz expuesta**: `WeaponInstance.to_dict() -> Dictionary` — snapshot serializable JSON-friendly.
- **Contrato**: Replay usa el snapshot capturado al momento del match, NO el WeaponInstance actual (cubre Edge Case E6.3 — weapon retired entre match y replay viewing).

#### F.2.5 PvP Async System (HARD) — not yet designed (System #18, MVP Feature)
- **Qué necesita**:
  - Enviar weapon a async tournament via serialization network-friendly.
  - `WeaponInstance.to_dict()` retorna **subset snapshot** (no el objeto completo), excluyendo campos mutables y metadata local.
- **Interfaz expuesta**: `WeaponInstance.to_dict() -> Dictionary` con campos listados en C.2 "PvP snapshot subset table".
- **Contrato**:
  - NUNCA usar `duplicate_deep()` para networking — solo `to_dict()` → JSON.
  - El receiver reconstruye un read-only `WeaponInstance.from_dict(dict)` para Combat Sim; este ghost weapon NO se persiste.

#### F.2.6 Matchmaking System (HARD) — not yet designed (System #19, MVP Feature)
- **Qué necesita**:
  - `WeaponInstance.power_rating: int` (integer-only, calculado al naming) para bucket assignment.
  - `power_rating` es frozen post-NAMED; no recalcula con balance patches (evita churn en matchmaking buckets).
- **Interfaz expuesta**: Campo público read-only `power_rating`.
- **Contrato anti-P2W**:
  - `power_rating` **NO es visible en HUD** ni mini-cards (evita optimization meta a la vista del jugador).
  - `power_rating` es *ballpark signal*, no score exacto — Matchmaking GDD define los weights finales.

### F.3 Downstream — Soft Dependents (consume sin bloqueo)

#### F.3.1 Hub HUD (SOFT) — not yet designed (System #20, MVP Presentation)
- **Qué consume**: Display de `weapon_name`, `rarity`, `archetype.sprite_id`, `element_id` para weapon roster tile.
- **NO consume**: `power_rating` (anti-P2W), `input_materials_snapshot` (spoiler del signature).

#### F.3.2 Weapon/Material UIs (SOFT) — not yet designed (System #22, MVP Presentation)
- **Qué consume**: Full `WeaponInstance` fields para inspection panel: stats finales, affixes, signature, fecha de forja.
- **Interfaz expuesta**: Read-only acceso a todos los campos (state-permitting).

#### F.3.3 PvP Replay/Victory/Meta Menus (SOFT) — not yet designed (System #23, MVP Presentation)
- **Qué consume**: Post-match: signature propagation display (si ganó, propaga signature del winner al perdedor).
- **Contrato**: Signature propagation es **post-match only**, NUNCA en pre-match preview (evita reputation-avoidance meta).

#### F.3.4 Cosmetic/Skin System (SOFT) — Tier 3, not in MVP
- **Qué consume**: `WeaponInstance.cosmetic_overlay_id` (único campo mutable cosmético post-NAMED).
- **Contrato**: Cosmetic overlay es 100% visual; NUNCA afecta stats, power_rating, o gameplay.

### F.4 Lateral Dependencies (coexistencia, no consumidor/productor)

- **Economy System** (System #14, MVP Core): El weapon puede ser "vendido" o "retired" (state transition), generando materials/currency como output. Definido en Economy GDD (pending); Weapon Entity solo expone transición `NAMED → RETIRED → DELETED`.
- **Player Progression** (System #15, MVP Core): Forging un weapon de rareza ≥ Epic desbloquea achievements. Player Progression consume events emitidos por Weapon Entity; Weapon Entity no conoce a Player Progression.

### F.5 Bidirectional Consistency Note

Los siguientes GDDs **deben listar Weapon Entity como dependencia** cuando sean autorizados:
- Forge System, Combat Simulation, Hero/Wielder, Affix System, Replay System, PvP Async, Matchmaking System (HARD upstream).
- Hub HUD, Weapon/Material UIs, PvP Replay Menus, Cosmetic/Skin (SOFT upstream).

Los GDDs **ya designed** que deben ser retrofitted cuando se aplique la schema extension requested:
- Material System: agregar `stat_bonuses` y `damage_type_affinity` a `MaterialDefinition` schema (no-breaking append).

Los GDDs **ya designed** que deben mencionar Weapon Entity en su sección de Dependencies:
- Data-Driven Config: actualizar "GDDs using this pattern" para incluir Weapon Entity.
- Material System: actualizar "Consumers" para listar Weapon Entity como downstream.

### F.6 Provisional Dependency Risks

- **Save/Load & Persistence not designed** → asumimos `.tres` individual write; si el GDD final restringe a monolithic save, re-architect requerido.
- **Affix System not designed** → asumimos `AffixSystem.roll_affixes(seed, ...)` signature; si el design final difiere, adaptar Fórmula #3 y campo `rolled_affixes`.
- **Combat Simulation determinism contract not locked** → asumimos integer-only consumer; si Combat Sim requiere floats en output, determinism break — flag como crítico en Open Questions.

## Tuning Knobs

Todos los valores ajustables sin cambiar código. Cada knob especifica **tipo**, **default**, **rango seguro**, **qué afecta**, y **owner** (si no es Weapon Entity).

### G.1 Archetype-Level Knobs (por WeaponArchetypeDefinition .tres)

Estos se ajustan por archetype individual. Los defaults vienen del MVP values table en C.1.

| Knob | Tipo | Default (Sword/Dagger/Hammer) | Rango seguro | Afecta |
|------|------|-------------------------------|--------------|--------|
| `base_attack` | int | 100 / 70 / 145 | [20, 300] | Atk final floor |
| `base_defense` | int | 80 / 45 / 110 | [10, 250] | Def final floor |
| `base_speed` | int | 100 / 160 / 55 | [20, 250] | Spd final floor |
| `base_crit_per_mille` | int | 75 / 150 / 30 | [0, 300] | Crit chance cap soft |
| `base_dodge_per_mille` | int | 60 / 120 / 25 | [0, 250] | Dodge chance cap soft |
| `base_power_rating` | int | 500 / 480 / 520 | [300, 800] | Matchmaking bucket |
| `affix_slot_count` | int | 3 / 3 / 3 | [1, 5] | RNG surface per-weapon |
| `affix_slot_tiers` | Array[int] | [2,3,4] | cada slot [1,5] | Affix magnitude cap |

**Safe range rationale**:
- `base_attack/defense/speed` sum should roughly match archetype role power (total ~280 en los 3 MVP). Romper esta simetría rompe balance PvP.
- `base_crit_per_mille` > 300 (30%) combinado con affix bonus podría saturar el cap 500‰ (50%) — no deseado.
- `affix_slot_count` > 3 en MVP explota la combinatoria del roster meta; deferred to post-MVP.

### G.2 Rarity Gate Thresholds (Fórmula #1)

Los thresholds que gobiernan qué rareza se asigna a una weapon según input materials + forge grade.

| Knob | Tipo | Default | Rango seguro | Afecta |
|------|------|---------|--------------|--------|
| `RARITY_5_EPIC_COUNT_MIN` | int | 2 | [1, 4] | Cuántos Epic mats exige Legendary |
| `RARITY_5_GRADE_MIN` | int | 85 | [70, 95] | Qué grade mínimo exige Legendary |
| `RARITY_4_EPIC_COUNT_MIN` | int | 1 | [1, 2] | Path Epic-fast a Epic rarity |
| `RARITY_4_GRADE_MIN` | int | 60 | [40, 80] | Grade mínimo path Epic |
| `RARITY_4_RARE_COUNT_MIN` | int | 2 | [1, 4] | Path Rare-bulk a Epic rarity |
| `RARITY_4_RARE_GRADE_MIN` | int | 75 | [60, 90] | Grade mínimo path Rare-bulk |
| `RARITY_3_RARE_COUNT_MIN` | int | 1 | [1, 2] | Path Rare-fast a Rare rarity |
| `RARITY_3_RARE_GRADE_MIN` | int | 40 | [20, 60] | Grade mínimo path Rare |
| `RARITY_3_UNCOMMON_COUNT_MIN` | int | 3 | [2, 5] | Path Uncommon-bulk a Rare |
| `RARITY_3_UNCOMMON_GRADE_MIN` | int | 60 | [40, 80] | Grade mínimo path Uncommon-bulk |
| `RARITY_2_UNCOMMON_COUNT_MIN` | int | 1 | [1, 2] | Path a Uncommon rarity |
| `RARITY_2_UNCOMMON_GRADE_MIN` | int | 20 | [10, 40] | Grade mínimo path Uncommon |
| `RARITY_2_COMMON_COUNT_MIN` | int | 3 | [2, 5] | Path Common-bulk a Uncommon |
| `RARITY_2_COMMON_GRADE_MIN` | int | 60 | [40, 80] | Grade mínimo path Common-bulk |

**Safe range rationale**: Bajar los thresholds hace que Legendary/Epic sean trivial → devaluación de fantasy. Subirlos hace que sean inalcanzables para F2P no-whales → choke de progresión. Los defaults están calibrados para que ~5-10% de forges de usuarios engaged produzcan Epic+.

### G.3 Grade Bonus Multiplier (Fórmula #2)

| Knob | Tipo | Default | Rango seguro | Afecta |
|------|------|---------|--------------|--------|
| `GRADE_BONUS_MULTIPLIER` | int | 5 | [3, 8] | Magnitud del skill reward por grade |

**Safe range rationale**: `grade_bonus_per_mille = forge_grade × GRADE_BONUS_MULTIPLIER`. Default 5 → rango [0, 500‰] (0% a +50%). Subir a 8 → rango [0, 800‰] puede colapsar el balance entre low-skill y high-skill forges. Bajar a 3 → rango [0, 300‰] hace el forge mini-game irrelevante.

### G.4 Material Bonus Aggregation (Fórmula #3)

| Knob | Tipo | Default | Rango seguro | Afecta |
|------|------|---------|--------------|--------|
| `TIER_WEIGHT_TABLE` | Array[int] | [1, 2, 4, 8] | ver rationale | Peso de contribución de material por tier |
| `COMPATIBILITY_MULTIPLIER_MATCH` | int | 1000 (per-mille) | [900, 1100] | Multiplicador cuando damage_type match |
| `COMPATIBILITY_MULTIPLIER_MISMATCH` | int | 500 (per-mille) | [300, 800] | Penalty cuando damage_type mismatch |

**Safe range rationale**:
- `TIER_WEIGHT_TABLE`: la progresión geométrica [1,2,4,8] hace que usar materials Tier 4 valga *significativamente* más que Tier 1. Alternativas seguras: [1,2,3,4] (linear, menos gradiente), [1,3,9,27] (extremo, hace Tier 1 inútil). Mantener monótono creciente.
- `COMPATIBILITY_MULTIPLIER_MISMATCH` < 300 castiga demasiado experimentación; > 800 elimina el incentivo a matching. Default 500 = 50% penalty es el sweet spot.

### G.5 Stat Final Clamps (Fórmula #4)

| Knob | Tipo | Default | Rango seguro | Afecta |
|------|------|---------|--------------|--------|
| `STAT_FINAL_CLAMP_MIN` | int | 1 | [1, 10] | Floor de stat (nunca 0, evita div-by-zero) |
| `STAT_FINAL_CLAMP_MAX` | int | 9999 | [5000, 99999] | Ceiling de stat (evita overflow/cheat) |

**Safe range rationale**: Clamp min = 1 garantiza no-zero. Clamp max = 9999 fits en int16 si fuera necesario serializar compacto, aunque int32 es default. Raising clamp max requires auditing Combat Sim para overflow en multiplicaciones.

### G.6 Element Dominance (Fórmula #5)

| Knob | Tipo | Default | Rango seguro | Afecta |
|------|------|---------|--------------|--------|
| `NEUTRAL_FALLBACK` | StringName | `&"neutral"` | (no adjustable sin renombrar element) | Element asignado cuando inputs son all-Neutral |
| `ELEMENT_TIEBREAK_MODE` | String | `"first_encountered"` | (enum: "first_encountered", "alphabetical", "weighted_random") | Tiebreak cuando dos elementos empatan en tier sum |

**Safe range rationale**: `ELEMENT_TIEBREAK_MODE` no es un knob numérico sino enum. `"first_encountered"` es el default determinista; cambiar a `"weighted_random"` introduce RNG no-replayable (BREAKS determinism — no recomendado).

### G.7 Power Rating Weights (Fórmula #6) — OWNED BY MATCHMAKING GDD

Estos knobs **NO son ajustados desde Weapon Entity**; los posee Matchmaking para anti-P2W calibration.

| Knob | Tipo | Default (proposed) | Owner GDD | Afecta |
|------|------|---------------------|-----------|--------|
| `POWER_STAT_WEIGHT` | int (divisor) | 3 | Matchmaking | Peso de (atk+def+spd)/3 en PR |
| `POWER_CRIT_DIVISOR` | int | 5 | Matchmaking | Peso del crit en PR |
| `POWER_DODGE_DIVISOR` | int | 5 | Matchmaking | Peso del dodge en PR |
| `POWER_RARITY_MULTIPLIER` | int | 50 | Matchmaking | rarity × 50 en PR |
| `POWER_AFFIX_TIER_MULTIPLIER` | int | 30 | Matchmaking | affix_tier × 30 en PR |

**Contrato cross-GDD**: Cuando Matchmaking GDD sea authorizado, estos knobs se moverán al registry owned by Matchmaking. Weapon Entity calcula `power_rating` al momento de naming usando los valores actuales; re-cálculo NO sucede cuando Matchmaking ajusta los weights post-launch (ver Edge Case E7.4).

### G.8 Roster & Signature Limits

| Knob | Tipo | Default | Rango seguro | Afecta |
|------|------|---------|--------------|--------|
| `MAX_ROSTER_SIZE` | int | 12 | [8, 20] | Cap del weapon roster por player |
| `WEAPON_NAME_MAX_LENGTH` | int | 20 | [10, 40] | Longitud máxima del nombre del weapon |
| `PLAYER_SIGNATURE_MAX_LENGTH` | int | 32 | [16, 64] | Longitud máxima del signature |

**Critical — anti-P2W constraint**:
- `MAX_ROSTER_SIZE` **NO es purchasable**. Subirlo via compra rompe uno de los pillars core del juego. Este knob está hardcoded al valor `12` en el codepath de monetization checks; cambiarlo requiere ADR explícito.
- Safe range [8, 20] es para balance iteration interno (designer puede reducir a 10 para playtest más tight), no para gating de features.

### G.9 Forge Session & RNG

| Knob | Tipo | Default | Rango seguro | Afecta |
|------|------|---------|--------------|--------|
| `FORGE_SESSION_ID_BITS` | int | 64 | [32, 128] | Entropy del seed (debería quedarse en 64) |
| `AFFIX_ROLL_DETERMINISM` | bool | `true` | (no deberia cambiar) | Si false, BREAKS replayability |

**Safe range rationale**: `FORGE_SESSION_ID_BITS` < 32 → birthday paradox collisions entre weapons en pocos millones de forges — replay integrity rota. Default 64 es seguro para la escala anticipada.

### G.10 Knobs Explicitly NOT Adjustable (Locked Constants)

Estos son constantes de invariante, documentados aquí para que no sean confundidos con tuning knobs:

- `SCHEMA_VERSION` (int): Incrementado solo cuando schema cambia incompatible-mente. Not a tuning knob.
- Per-mille scale = `1000`: Cualquier cambio rompe todas las fórmulas. Not a tuning knob.
- StringName damage types `[&"slash", &"pierce", &"blunt"]`: Agregar un 4to requiere nuevo archetype + art asset + Combat Sim update.
- StringName elements (7): Agregar requiere Material System update.
- State enum `[FORGING, FORGED_UNNAMED, NAMED, EQUIPPED, DEFENDING, RETIRED, DELETED]`: Mutar requiere migration script.

### G.11 Knob Adjustment Protocol

1. Todos los knobs de G.1–G.9 están almacenados en `res://data/tuning/weapon_entity_tuning.tres` (Custom Resource `WeaponEntityTuning`).
2. Designer edita `.tres` en editor; cambios visibles en next play.
3. Balance passes que tocan > 3 knobs en un commit requieren playtest documentado antes de merge.
4. Anti-P2W knobs (`MAX_ROSTER_SIZE`, Power Rating weights) requieren approval explícito de Creative Director (per Coordination Rules).

## Visual/Audio Requirements

### V.1 Creation Climax Moment (FORGING → FORGED_UNNAMED)

**Trigger**: `WeaponInstance.state` transitions from `FORGING` to `FORGED_UNNAMED`.

**Visual sequence** (total duration: ~2.0 s, diegetic):
1. **Light-crack seam** (0–0.4 s): A hairline fracture of warm-white light (`#F5EDD8` Ashen Bone) splits the occluded forge silhouette vertically — the weapon is about to break through. Single `AnimatedSprite2D` flipbook, 8 frames at 24 fps, 64×128 px strip.
2. **Reveal flash** (0.4–0.6 s): Peak luminosity burst — 3 frames whiteout on the weapon sprite (`#FFFFFF`), then immediate falloff. No screen shake. No particle burst. Light communicates quality (art bible §6.1 input-feedback rule).
3. **Element-colored settle** (0.6–2.0 s): Weapon sprite fades from over-exposed to its rarity- and element-correct palette. One `GPUParticles2D` emitter fires the element-signature particle shape (see §V.4) — 80 particles max, 1.5 s lifetime, one-shot (non-looping). Emitter released after burst completes, returning to the 4-emitter pool.
4. **Beckon pulse begins** (2.0 s+): Transitions directly into the FORGED_UNNAMED idle state (see §V.6).

**Asset delegation**: `vfx_weapon_reveal_crack_flipbook_64x128.png` (8-frame strip) → technical-artist. Reveal flash is a shader-level luminosity peak — delegate to godot-shader-specialist.

**Performance**: 1 emitter consumed. All other elements are `AnimatedSprite2D` — predictable DC cost.

### V.2 Signature Rendering

The `player_signature` field (max 32 chars) is rendered across three surfaces. Typography follows art bible §7 (Cinzel Decorative headline / Crimson Text body family).

| Surface | Typeface | Size | Color | Context |
|---|---|---|---|---|
| Inspection panel | Crimson Text Italic | 18 px | `#9A8878` worn pewter | Below weapon name, above stat block. Full 32 chars, single line. Overflow: ellipsis at 28 chars. |
| PvP loss screen | Cinzel Decorative Regular | 14 px | `#D4962A` Dawn Brass | Centered under winner's weapon. "Forged by [signature]" prefix in 11 px Crimson Text. |
| Roster mini-card | Crimson Text Regular | 10 px | `#9A8878` worn pewter | Bottom edge of card. Max 20 chars visible; ellipsis truncation. Purely decorative at this LOD. |

**Signature absent** (empty string): Surface renders nothing — no placeholder, no "Anonymous" label. The absence is the signal (weapon has no named maker).

**Asset delegation**: No new asset. Font rendering via Godot `Label` nodes. Ensure `theme_override_font_size` is set per surface in scene, not in code.

### V.3 Rarity Visual Tier System

Rarity signals occupy the **frame/border spatial zone only** — never the weapon glow zone (reserved for element, §V.4). Signals are layered: color → frame weight → particle density → animation speed.

| Rarity | Frame / Border | Glow Tint | Particle Treatment | Idle Animation |
|---|---|---|---|---|
| **Common** | 1 px chamfer border, `#4A4060` Anvil Iron | None — weapon at base palette | None | Static. No animation. |
| **Uncommon** | 2 px chamfer border, `#3D7A4A` Verdant Temper | Faint green inner glow, 20% opacity | None | None. Border weight is the signal. |
| **Rare** | 2 px chamfer border, `#2A5F8C` Cold Quench | Blue inner glow, 35% opacity | Flipbook ambient aura: 6 frames at 12 fps, 64×64 px, slow fade loop | Aura breathes at 0.8 Hz (flipbook loop speed). |
| **Epic** | 3 px chamfer border, `#7A3070` bruised violet | Violet inner glow, 50% opacity | Flipbook ambient aura: 8 frames at 18 fps, 64×64 px; wisps drift inward (dark element shape vocabulary) | Aura pulses at 1.2 Hz. |
| **Legendary** | 3 px chamfer border + animated gold filigree overlay, `#D4962A` Dawn Brass | Gold inner glow, 65% opacity | 1 `GPUParticles2D` emitter, max 60 particles, slow gold-dust drift, looping | Filigree overlay animates: flipbook 12 frames at 24 fps. Weapon idle at 0.5 Hz shimmer. |

**Non-color rarity signal** (accessibility): Frame weight + particle density + animation speed are sufficient to distinguish all 5 tiers without color. Tested in grayscale per art bible §4.5.

**Legendary emitter cost**: The 1 looping emitter for Legendary is drawn from the 4-emitter pool. Only 1 Legendary weapon may be fully rendered with its emitter simultaneously on screen. If 2+ Legendary weapons are visible (e.g., roster scroll), demote all but the focused/selected one to flipbook-only aura.

### V.4 Element Visual Signatures

Element signals occupy the **weapon glow and particle zone** — not the border (reserved for rarity, §V.3). Each element has a distinct particle shape per art bible §4.5 (color-blind fallback).

| Element | Chromatic Overlay | Particle Shape | Particle Color | Ambient Particle Source |
|---|---|---|---|---|
| **Fire** | `#C4501A` Forge Ember inner glow, 40% | Upward-rising sharp sparks, 4–6 px | `#C4501A` → `#F5EDD8` fade | Flipbook: 10 frames at 24 fps, 64×64 px |
| **Water / Ice** | `#2A5F8C` Cold Quench inner glow, 35% | Rounded droplets trailing downward, 3–5 px | `#2A5F8C` → white tip | Flipbook: 8 frames at 18 fps, 64×64 px |
| **Earth** | `#3D7A4A` Verdant Temper inner glow, 30% | Slow tumbling hexagonal shards, 5–8 px | `#3D7A4A` muted | Flipbook: 6 frames at 12 fps, 64×64 px |
| **Wind / Storm** | `#A8C8B0` Storm Pale inner glow, 30% | Horizontal streaks, 8–12 px long, 1–2 px wide | `#A8C8B0` → transparent | Flipbook: 8 frames at 24 fps, 64×64 px |
| **Light** | `#F5EDD8` Ashen Bone inner glow, 45% | Radial burst lines, 4 px, 8-directional | `#F5EDD8` → `#FFFFFF` | Flipbook: 6 frames at 18 fps, 64×64 px |
| **Dark** | `#1A1228` Void Seam tint + `#7A3070` violet bleed, 40% | Inward-collapsing wisps, 6–10 px | `#7A3070` → `#1A1228` | Flipbook: 10 frames at 18 fps, 64×64 px |
| **Neutral** | None — weapon at base palette | None | `#4A4060` Anvil Iron motes, 3 px, very sparse | Flipbook: 4 frames at 8 fps, 64×64 px — barely perceptible |

**Element + Rarity simultaneity**: Element glow bleeds inward from the blade. Rarity border sits outside. The two signals never occupy the same pixel zone. Visual test: cover the border — element must still read. Cover the blade — rarity must still read.

**Element icon**: Always rendered alongside element VFX (art bible §4.5). Icon is the non-VFX fallback; VFX is additive, not primary communication.

### V.5 Archetype Silhouettes

Three archetypes must be distinguishable at **100px rendered height** with no label — Legible Silhouette pillar. Silhouette differentiation via mass distribution (art bible §5.1).

| Archetype | Damage Type | Silhouette Mass Rule | Key Differentiators at 100px |
|---|---|---|---|
| **Sword** | Slash | Mass centered on a long, narrow vertical axis. Guard creates a deliberate horizontal interrupt at 40% height. Tip tapers to a single-pixel point. | Horizontal crossguard break + single apex point. Reads as "divided long form." |
| **Dagger** | Pierce | Mass concentrated in the top 35% — short, dense blade, minimal guard, prominent pommel weight at base. Overall silhouette is narrow and asymmetric top-to-bottom. | Short upper mass + weighted base. No horizontal break. Reads as "dense short form." |
| **Hammer** | Blunt | Mass concentrated in the top 40% as a wide, convex head. Handle is narrow relative to head width. Head-to-handle width ratio ≥ 3:1. | Top-heavy convex mass. Reads as "inverted teardrop." |

**Thumbnail test protocol**: Each archetype design is approved at 100px greyscale silhouette before 100% detail work begins. Silhouette drift > 10% of mass distribution → blocking rejection per art bible §8.2.

**Elemental / stat terminus hints** (art bible §5.1): Applied at terminus of primary mass — blade tip, hammerhead corner, dagger pommel. These are additive details that do not alter the mass distribution read.

**Asset naming**: `weapon_[archetype]_[variant]_[lod].png`
Examples: `weapon_sword_fire_100.png`, `weapon_hammer_legendary_200.png`

### V.6 State Visual Indicators

All state indicators are **diegetic** — communicating through the weapon object itself, not overlay UI badges. Each state has a distinct visual treatment that reads at 100px mini-card size.

| State | Visual Treatment | Animation | Notes |
|---|---|---|---|
| **FORGING** | Weapon occluded: silhouette visible as dark ghost shape behind a semi-opaque forge-heat distortion overlay. Base palette desaturated to 20%. | Heat distortion: flipbook 8 frames at 24 fps, 64×128 px. Distortion expands/contracts at 0.5 Hz. | Managed by Forge System GDD — this state's VFX is owned there. Weapon Entity System renders the occluded sprite only. |
| **FORGED_UNNAMED** | Weapon at full palette. Subtle beckon pulse: inner glow breathes from 0% → 30% → 0% opacity using element color. No border animation. | Flipbook 6 frames at 12 fps, 64×128 px pulse cycle. Loop rate: 0.6 Hz — slower than Rare aura to feel "waiting" not "urgent." | The diegetic "name me" signal. Player Fantasy: the blade is finished, suspended, expectant. |
| **NAMED** | Weapon at full palette. Rarity border active (§V.3). Element ambient aura active (§V.4). No additional state animation. | Per rarity/element tables above. | At rest. This is the baseline "healthy weapon" state. |
| **EQUIPPED** | Weapon at full palette + thin warm-amber bracket (`#D4962A` Dawn Brass, 1 px) on outer corners of bounding box. Bracket does NOT trace silhouette — it is a rectangular corner mark only. | No animation on bracket. Weapon rarity/element animations continue. | Bracket is the single permitted non-diegetic marker — minimal, uses the forge's own accent color. |
| **DEFENDING** | Weapon at full palette + rarity/element animations + a second-ring ambient glow (`#D4962A` Dawn Brass, 25% opacity, 8 px radius beyond border). Weapon appears at slightly higher luminosity (+10%). | Second-ring glow: flipbook 8 frames at 18 fps, looping 1.0 Hz pulse. | Reads as "on guard." Luminosity boost ensures prominence in roster list context. |
| **RETIRED** | Weapon desaturated to 25% of original saturation. Border color reduced to `#4A4060` Anvil Iron regardless of rarity. All particle/aura animations halted — sprite is static. | None. Static. | The absence of animation is the signal. Costs zero draw calls above baseline sprite. |
| **ORPHANED** | Weapon desaturated to 15% + chromatic aberration overlay (red/cyan 2 px horizontal offset). Void Seam `#1A1228` color bleed at sprite edges. Border: broken/dashed 1 px `#7A3070` violet. | Glitch flipbook: 6 frames at 8 fps, irregular timing (frames 2 and 5 held 3× longer). Flicker rate: 0.3 Hz. | Error state. Chromatic aberration is the corruption signal — unmistakable, not confused with any intentional element. Delegate glitch shader to godot-shader-specialist. |

### V.7 Master's Mark Seal

**MVP definition**: A single static stamp icon — hammer-and-anvil silhouette within a circular wax-seal border. Monochromatic: `#D4962A` Dawn Brass on `#1A1228` Void Seam background.

**Render surface**: Tournament victory/results screen only. Positioned bottom-right of the winning weapon card. Size: 24×24 px rendered.

**Trigger condition (MVP)**: Appears when the winning weapon's `forge_grade` ≥ threshold defined in `data_config.json` `prestige_seal_min_grade` (tuning knob — default: not yet balanced). No propagation to PvP loss screen in MVP.

**Asset**: `ui_seal_mastersmark_default_24.png` — single static sprite. No animation in MVP.

**Post-MVP path**: Animated wax-drip stamp effect (flipbook) if player milestone warrants it. Coordinate with producer before scoping.

### A.1 Forge Complete Reveal Audio

**Trigger**: `FORGING → FORGED_UNNAMED` state transition.

**SFX layer** (~0.4 s): Short metallic resonance — a struck iron bell that decays cleanly. Frequency: mid-range (~800–1200 Hz fundamental), no low-end boom. Duration: 0.4 s with 0.3 s natural tail. No reverb added in engine (applied in source asset).

**Music sting** (~2.0 s): A 3-note ascending motif in Cinzel's implied key — final note held, resolving upward (unresolved 7th → resolved octave). Communicates arrival, not triumph (triumph belongs to a named + winning weapon). Instrumentation: solo struck dulcimer or hammered metal bar. No orchestral swell in MVP.

**Rarity modifier**: The base SFX pitch-shifts per rarity tier:
- Common: +0 semitones (base)
- Uncommon: +1 semitone
- Rare: +2 semitones
- Epic: +3 semitones + subtle reverb tail added
- Legendary: +5 semitones + reverb + a second resonant overtone layered at 1.5× fundamental

**Asset**: `sfx_forge_reveal_base.ogg` + music sting `mus_forge_reveal_sting.ogg`. Pitch-shift applied via `AudioStreamPlayer.pitch_scale` at runtime — single source file per sound.

### A.2 Name Submit Audio

**Trigger**: `FORGED_UNNAMED → NAMED` state transition (player confirms the weapon name).

**SFX**: Two-layer hit:
1. Ink-stamp thud — soft percussive impact (~200 ms), low-mid (~400 Hz). Communicates the act of marking/signing.
2. Metallic shimmer tail (~600 ms) — high-frequency shimmer (3–5 kHz), fades to silence. The blade acknowledging its name.

**No music sting**. This moment is intimate — the weapon-naming is a private act between the player and the forge. The stamp thud + shimmer is sufficient.

**Asset**: `sfx_weapon_namesubmit.ogg` — single file, both layers baked in.

### A.3 Equip SFX (3 Damage Types)

**Trigger**: Weapon transitions to `EQUIPPED` state from `NAMED` (player assigns weapon to slot).

Each damage type has a distinct equip sound communicating material and intent:

| Damage Type | Sound Character | Duration | Frequency Focus |
|---|---|---|---|
| **Slash** (Sword) | Blade drawn from scabbard — steel-on-leather friction, brief ring at end. | ~350 ms | 2–6 kHz (steel ring), short low-mid body |
| **Pierce** (Dagger) | Short snap-click — a small blade locking into a sheath or bracer. Dry, minimal resonance. | ~200 ms | 4–8 kHz (click), near-zero low end |
| **Blunt** (Hammer) | Heavy settle — a dense object placed on stone. Thud with brief low resonance. | ~400 ms | 80–400 Hz (thud body), minimal high end |

**Assets**: `sfx_equip_slash.ogg`, `sfx_equip_pierce.ogg`, `sfx_equip_blunt.ogg`.

### A.4 Rarity Reveal SFX (5 Tiers)

**Trigger**: Rarity is determined and displayed during the creation climax (§V.1 / §A.1). Plays as a secondary layer after the forge-complete SFX settles (~0.5 s offset).

| Rarity | Sound Character | Duration |
|---|---|---|
| **Common** | Silence — no rarity reveal SFX. Weapon sound is the reveal. | — |
| **Uncommon** | Single soft chime, mid-register. Clean decay. | ~400 ms |
| **Rare** | Two-note ascending chime, slightly brighter. Clean decay. | ~600 ms |
| **Epic** | Three-note ascending chime + low resonant undertone, slight reverb. | ~900 ms |
| **Legendary** | Full three-note ascending chime + low harmonic bell + sustained reverb tail. Grand but not bombastic — emotional weight, not fanfare. | ~1.8 s |

**Assets**: `sfx_rarity_uncommon.ogg` through `sfx_rarity_legendary.ogg` (4 files; Common = silence). All use the same tonal family — differentiated by note count, register, and reverb, not by genre switch.

### A.5 Element Signature SFX (7 Elements)

**Trigger**: Element identity is revealed during creation climax, layered with rarity reveal (~0.8 s after forge-complete SFX). Short sonic signature — identifies the element, does not compete with the reveal arc.

| Element | Sound Character | Duration |
|---|---|---|
| **Fire** | Soft whoosh + a single crackle tick at the end. | ~400 ms |
| **Water / Ice** | Short high-frequency crystalline ping + brief water-droplet resonance. | ~350 ms |
| **Earth** | Low stone-scrape — heavy, dry, no sustain. | ~300 ms |
| **Wind / Storm** | Rapid breathy flutter — air through a narrow gap, brief. | ~350 ms |
| **Light** | Pure sine-tone ding — very brief, clean, no reverb. Bell-like clarity. | ~250 ms |
| **Dark** | Low sub-bass pulse (~60–80 Hz) + inward whoosh (reversed breathy hit). | ~500 ms |
| **Neutral** | None — no element SFX. Weapon ambient already communicates the absence. | — |

**Assets**: `sfx_element_fire.ogg` through `sfx_element_dark.ogg` (6 files; Neutral = silence).

### A.6 Orphan Discovery SFX

**Trigger**: A `WeaponInstance` is detected as ORPHANED on save-load validation (missing archetype reference or corrupted data block).

**SFX**: A short dissonant metallic groan — as if a blade is stressed beyond tolerance. Two layers baked together:
1. Metal creak (~500–800 Hz, 300 ms)
2. Subtle glitch-click: a digital artifact sound, 2–3 rapid clicks at irregular intervals (~150 ms total)

The glitch-click layer signals "this is a data error, not a game event" — distinguishable from any intentional combat or forge SFX by its digital character.

**No music sting**. This is an error state, not a gameplay moment.

**Asset**: `sfx_weapon_orphaned.ogg`.

## UI Requirements

### UI.1 Weapon Roster Browser

- **Screen**: Hub → Roster
- **Layout**: Vertical scrollable grid, 2 columns, portrait orientation. Each cell is a Weapon Mini-Card (see UI.2). Grid fills from top-left, ORPHANED weapons rendered last regardless of sort.
- **Capacity indicator**: Persistent counter `"X / 12"` pinned top-right of the screen header, 20pt bold. Turns red (`#CC3333` or equivalent high-contrast) when X = 12. Visible at all times — not collapsible.
- **Sort controls**: Segmented control or pill tabs below header — options: `Forge Date` (default, DESC) | `Rarity` | `Archetype`. One active at a time. 44pt tap height.
- **Filter controls**: Horizontally scrollable filter chips below sort bar — options: `All` (default) | `Named` | `Equipped` | `Defending` | `Retired` | `Orphaned`. Multi-select not required; single active filter. 44pt tap height per chip.
- **Empty state**: Full-grid placeholder card with archetype silhouette at 40% opacity and label `"No weapons yet — visit the Forge."` Center-aligned. No sort/filter controls visible when roster is empty.
- **At-cap state**: Capacity indicator turns red. Tapping [Forge] from Hub triggers UI.5 dialog instead of entering forge flow. Roster itself remains fully interactive.
- **Touch interactions**: Tap card → opens UI.3 Inspection Panel. No swipe-to-dismiss on cards. Long-press not required (all actions exposed in Inspection Panel).
- **Accessibility**: Sort and filter controls keyboard/switch-navigable in logical DOM order. Capacity counter has `aria-label="Roster X of 12 weapons"`. Grid items labeled `"[weapon_name], [rarity], [state]"`.

### UI.2 Weapon Mini-Card (100×140dp nominal)

- **Variants**:
  - `roster-own` — tappable, shows full hierarchy, ORPHANED treatment applied
  - `pvp-post-match` — read-only, no tap action, rendered inside match summary panel
- **Information hierarchy** (top to bottom within card):
  1. Archetype silhouette icon — 48×48dp, PRIMARY visual anchor, centered top
  2. `weapon_name` — 14pt medium, 1 line max, truncated with ellipsis at card edge
  3. `player_signature` — 11pt regular, muted color, 1 line max, truncated — visible in `roster-own` only; **hidden in `pvp-post-match` pre-match contexts** (see Rule 7, UI.6)
  4. `forge_grade` glyph + `element` icon — two 16×16dp badges, bottom-right corner, side by side
  5. `rarity` — communicated via border treatment or badge color only (not a text label on the mini-card)
- **NO-display fields**: `power_rating` (anti-P2W, never shown), `player_signature` pre-match (Rule 7)
- **Pre-match signature enforcement**: In pre-match contexts, the `player_signature` Label node is **NOT instantiated / absent from scene tree** (not redacted/blurred). Testable via `assert $player_signature == null` o `scene_has_no_node("player_signature_label")` en automated UI test.
- **ORPHANED treatment**: Card rendered at 60% saturation. Glitch/noise overlay on silhouette icon (1-2 frame loop, subtle). Small `"?"` badge top-left, 16×16dp. Touch target active for inspect, but visual affordance signals restricted state.
- **Touch interactions**: `roster-own` — full card surface is tap target (44pt minimum satisfied by 100dp card height). `pvp-post-match` — no tap registered.
- **Performance**: Card must render with a single draw call per card (sprite atlas). No per-card shaders at roster list scroll speed.
- **Accessibility**: Screen reader announces `"[weapon_name], [archetype], [rarity], [state]"`. ORPHANED cards announce `"[weapon_name] — orphaned weapon, read only"`.

### UI.3 Weapon Inspection Panel

- **Presentation**: Modal sheet sliding up from bottom (mobile sheet pattern). Overlay dims roster at 50% opacity. Swipe-down to dismiss (except FORGED_UNNAMED state which is non-dismissable — see UI.4).
- **Dismiss paths** (redundant): (1) Swipe-down gesture on sheet header. (2) Visible `[X]` button top-right, 44×44dp. Both paths MUST be present (swipe + button) per accessibility fallback requirement.
- **Layout**: Single scrollable column. Header: large archetype silhouette (96×96dp) + `weapon_name` 22pt bold + `player_signature` 14pt regular below name. Body: stat grid (attack, defense, speed, crit, dodge) in 2-column label/value layout, 14pt. Affix slots listed below stats with slot index and affix name or `"Empty"`. `forge_date` and `rarity` in a secondary detail row at bottom.
- **NO-display fields**: `power_rating` never present in this panel.
- **Per-state action buttons** (bottom of sheet, above safe area):

  | State | Primary CTA | Secondary CTA | Tertiary |
  |---|---|---|---|
  | `NAMED` | [Equip to Hero] | [Retire] | — |
  | `EQUIPPED` | [Unequip] | ~~[Retire]~~ (disabled, grayed) | — |
  | `DEFENDING` | — (no actions) | Label: `"In Defense Roster — remove from defense to modify"` | — |
  | `RETIRED` | [Delete] | [Un-retire] (Tier 2 Vault only) | — |
  | `ORPHANED` | [Retire] | [Delete] | — |

- **Confirm dialogs**: Retire and Delete are destructive. Both require a confirmation step:
  - Retire: `"Retire [weapon_name]? This weapon will move to your Retired Vault."` — [Confirm Retire] [Cancel]
  - Delete: `"Permanently delete [weapon_name]? This cannot be undone."` — [Delete Forever] [Cancel]. [Delete Forever] uses a high-contrast warning color distinct from primary CTA.
- **Accessibility**: Sheet has focus trap while open. All buttons minimum 44×44dp touch target. Confirm dialogs are modal and block all background interaction.

### UI.4 Forge Naming Modal

- **Trigger**: Automatic on FORGED_UNNAMED → NAMED state transition. Not player-initiated.
- **Dismissal**: Prohibited via normal paths — no swipe-down, no `[X]` button. Player must complete the naming flow.
- **Android back gesture handling**: Back button intercepted and displays a **nudge toast**: `"Debes nombrar tu arma antes de continuar"` (3s duration, center-bottom). The modal stays open; no navigation occurs. This is more forgiving than silent suppression — player receives explicit feedback.
- **iOS back gesture**: Edge-swipe gesture disabled inside this modal. No toast needed (iOS has no system-level back button to intercept).
- **Weapon reveal**: Mini-card displayed at center at 120dp size (slightly enlarged). Entry animation: scale from 60% + fade-in over 400ms + particle/sparkle burst. Rarity border pulses once.
- **Text input field**:
  - Placeholder: archetype default name (e.g., `"Iron Sword"`) pre-populated and selected on focus so player can overwrite with a single keystroke
  - Character limit: 20 characters. Live counter `"X / 20"` displayed right-aligned below field, turns red at 18+.
  - Validation: UTF-8 allowed; control characters (`\n`, `\t`, etc.) stripped silently; leading/trailing whitespace trimmed on submit.
  - Input field: minimum 44pt height, 16pt font.
- **Action buttons**:
  - [Name & Equip] — primary CTA, pre-highlighted. Equips weapon to hero slot immediately after naming.
  - [Name & Keep in Roster] — secondary CTA. Saves weapon to roster in NAMED state without equipping.
  - Both buttons disabled while `weapon_name` field is empty (after trim).
- **Accessibility**: Modal announces `"New weapon forged. Name your weapon."` to screen reader on open. Input field labeled `"Weapon name, up to 20 characters"`. Both CTAs have distinct accessible labels.

### UI.5 Roster Full Error Dialog

- **Trigger**: Player attempts to initiate a forge action when roster count = 12 (counting FORGING + FORGED_UNNAMED + NAMED + EQUIPPED + DEFENDING states; RETIRED + DELETED + ORPHANED excluded).
- **Presentation**: Centered modal dialog (not a toast — action is required). Cannot be dismissed by tapping outside.
- **Content**: Icon: roster/grid icon with `"!"` badge. Title: `"Roster lleno"`. Body: `"Tenés 12 / 12 armas. Retirá o borrá un arma antes de forjar una nueva."`
- **Action buttons**:
  - [Ir al Roster] — primary CTA. Closes dialog, navigates to Roster screen, briefly highlights the capacity counter (pulse animation, 600ms).
  - [Cancelar] — secondary. Returns player to Hub with no navigation.
- **Anti-P2W constraint**: NO IAP option, NO `"Expandir Roster"` button, NO upsell copy. The roster cap is hard and non-purchasable. Implementation must not include a hook for IAP expansion in this flow. This is enforced by AC.7.3 (testable).
- **Accessibility**: Dialog is a focus trap. Announced as `"Alerta: Roster Lleno. 12 de 12 armas."` Screen reader reads body text. Both buttons minimum 44×44dp.

### UI.6 Signature Propagation Display (Post-Match)

- **Rule 7 invariant (testable)**: `player_signature` is NEVER displayed to either player before a PvP match resolves. The mini-card variant `pvp-post-match` enforces this by design (signature Label node absent from scene tree in the pre-match weapon preview). Automated UI test asserts: `get_node_or_null("%PlayerSignatureLabel") == null` en cualquier weapon display renderizado antes del `match_resolved` signal.
- **Field-level guard**: UI query paths that read `player_signature` MUST also check `WeaponInstance.signature_propagated_at > 0`. If `signature_propagated_at == 0`, display code returns empty string regardless of `player_signature` value.
- **Trigger**: `match_resolved` signal with outcome `LOSS` for the local player.
- **Animation sequence**:
  1. Post-match summary panel slides in (existing flow).
  2. Winner's weapon mini-card appears in center at 120dp.
  3. `player_signature` text fades in over the card — 32pt bold, prominent, centered below card — with a glow/shimmer effect lasting 800ms.
  4. Signature "stamps" onto the local player's HUD weapon slot with a brief imprint animation (300ms scale bounce).
- **Signature display**: `player_signature` up to 32 characters, single line, 32pt bold. If > 32 chars (edge case from legacy data), truncate with ellipsis — do not wrap.
- **Post-match only**: Signature display component is instantiated exclusively from post-match summary scene. It must not be reachable from any pre-match scene graph path.
- **Accessibility**: Animation respects OS `reduce_motion` / `prefers-reduced-motion` — if set, skip animation steps 2-3, display final state immediately. Signature text announced as `"[player_name]'s signature: [player_signature]"`.

### UI.7 Orphan State Indicator

- **Visual treatment** (applied to mini-card and inspection panel header):
  - Saturation reduced to 60% on the entire card
  - Silhouette icon replaced with a generic `"?"` placeholder at same dimensions; original archetype icon not shown (archetype may no longer exist in config)
  - Glitch/scanline overlay: 2-frame sprite loop, low amplitude (4px horizontal shift max), opacity 40%. Must be implemented as a single reusable shader or sprite — not per-card unique asset.
  - Border treatment: dashed or dotted pattern instead of solid rarity border
- **Label**: In the inspection panel below the archetype area, a label reads `"Archetype no longer exists"` in 13pt italic, secondary text color.
- **Tooltip / contextual text**: On first tap of an ORPHANED card (session-persistent, not permanent), an overlay tooltip appears: `"This weapon's archetype was removed. It can no longer be equipped. You may retire or delete it."` Dismiss on tap anywhere.
- **Restricted actions enforced**: [Equip to Hero] button never rendered for ORPHANED state. Only [Retire] and [Delete] buttons present. This is a display-layer enforcement in addition to any backend validation.
- **Accessibility**: ORPHANED card announces `"Orphaned weapon — [weapon_name]. Archetype no longer available. Options: Retire or Delete."` Glitch animation pauses if `reduce_motion` is active — static desaturated state shown instead.

### UI.8 Accessibility Requirements

- **Touch targets**: Minimum 44×44dp (iOS HIG) / 48×48dp (Android Material) for all interactive elements. Where card size constrains this (mini-card at 100dp width), the full card surface is the touch target.
- **Text minimum sizes**:
  - Body / stat labels: 13pt minimum
  - Secondary / signature on mini-card: 11pt minimum — must remain legible against card background with 4.5:1 contrast ratio (WCAG AA)
  - Confirm dialog body: 15pt minimum
  - Signature propagation display: 32pt (legibility is a hard requirement at this size)
- **Color-blind mode**: Rarity must be distinguishable without color. Primary cue: rarity tier label text on inspection panel. Secondary cue: border pattern variation (solid / double / ornate) that does not rely on hue. Element icons use shape differentiation in addition to color fills.
- **Screen reader / accessibility labels**: All interactive elements have unique, descriptive labels. Destructive buttons labeled with outcome (`"Delete weapon permanently"`, not just `"Delete"`). State-dependent labels update dynamically when weapon state changes.
- **Reduce motion**: All multi-frame animations (orphan glitch, signature propagation, weapon reveal sparkle) respect OS reduce-motion preference. Fallback: immediate final state render with no keyframe animation.
- **Safe areas**: All interactive elements and readable text inset from device safe area edges (iOS notch, home indicator, Android display cutouts). Minimum 8dp additional inset from safe area boundary.
- **Font scaling**: UI must remain functional (no overlap, no truncation of critical labels) at OS text size up to 130% of base. Weapon name fields truncate gracefully with ellipsis rather than breaking layout.

### UI.9 Performance Budgets

- **Roster Browser**: Maximum 24 Mini-Card nodes instantiated at any time (2-column grid, ~12 visible + pool of 12 offscreen for scroll). Cards outside viewport are freed from draw calls via Godot `CanvasItem.visible = false`. Target: roster scroll at 60fps on mid-range mobile.
- **Draw calls**: Roster screen budget — 80 draw calls maximum. Achieved via: sprite atlas for all card icons (archetype silhouettes, grade glyphs, element icons compiled into one atlas texture); no per-card unique materials.
- **Inspection Panel**: Stat sheet viewer peak memory — single panel instance reused (not re-instantiated per open). Panel hides and repopulates data rather than freeing/re-creating nodes. Peak additional memory from panel open: target < 2MB.
- **Naming Modal + Signature animation**: Particle/sparkle burst (UI.4) and signature shimmer (UI.6) use CPU particles capped at 40 particles max. Both effects are pooled and reused. Total effect budget: < 0.5ms per frame during animation.
- **Asset size economy (Web HTML5 < 15MB)**: All weapon UI assets (archetype silhouette atlas, grade glyphs, element icons, rarity border sprites) compiled into a single compressed atlas, target < 512KB. Orphan glitch overlay: 2-frame sprite, < 8KB. Signature propagation shimmer: shader-based (no texture), zero asset size.
- **Font**: Single font file covers all UI.1–UI.7 weight/size variants (regular + medium + bold). No additional font loads for weapon UI. Target font file contribution: < 200KB.

## Acceptance Criteria

31 criterios testables en GIVEN-WHEN-THEN que QA puede verificar pass/fail sin ambigüedad. Delegado y endurecido por `qa-lead`. Cobertura: Core Rules R1–R10, Fórmulas F1–F6, State transitions C.4, Anti-P2W constraints, Error handling.

**Legend**: BLOCKING = must pass before ship. ADVISORY = nice-to-have; can be waived con documented justification.

### H.1 Creation & Forge

**AC.1.1 — Creation success path** — BLOCKING
GIVEN un `ForgeSession` con `forge_session_id="test-seed-001"`, `archetype_uid="sword_iron_01"`, 3x Iron Ore (Tier 1, Common, compatible con Sword), `forge_grade=0`, `player_signature="TestSig"`, `weapon_name="TestSword"`
WHEN el forge completa
THEN existe un WeaponInstance con `state=NAMED` (2), `instance_uid` es UUID v4 válido, `archetype_uid` matches input, y los 14 frozen fields están populated sin nulls ni defaults.

**AC.1.2 — Weapon name submit** — BLOCKING
GIVEN un `WeaponInstance` en state `FORGED_UNNAMED`
WHEN el player submit un `weapon_name` válido (1–20 chars)
THEN state transitiona a `NAMED`, el field `weapon_name` stored exactamente como entered, los 14 frozen fields are read-only (escribir produce `push_error` en debug y no muta).

**AC.1.3 — Signature required pre-forge** — BLOCKING
GIVEN `ForgeSystem.create_weapon(...)` called con `player_signature=""` o `player_signature.length > 32`
WHEN el forge inicia
THEN retorna `null` + emite `forge_failed("invalid_signature")`; no WeaponInstance creado; materials NO consumidos.

**AC.1.4 — Archetype immutability during creation** — BLOCKING
GIVEN la `WeaponArchetypeDefinition` para "sword_iron_01" existe como `.tres`
WHEN un WeaponInstance es creado desde ella
THEN `WeaponInstance.archetype_uid` = UID del Archetype, y un checksum del Archetype `.tres` pre-forge y post-forge es idéntico (ningún field mutado).

**AC.1.5 — .tres write performance** — ADVISORY
GIVEN forge panel abierto y ForgeSession iniciada
WHEN el player trigger forge action
THEN un WeaponInstance `.tres` es escrito a `user://weapons/[instance_uid].tres` en < 500ms, y es loadable por `ResourceLoader.load(...)` sin error.

### H.2 Immutability Invariants

**AC.2.1 — Frozen field write rejection** — BLOCKING
GIVEN un WeaponInstance en state `NAMED` con `attack_final=440`
WHEN un debug tool o script escribe 200 a `attack_final`
THEN el field retiene 440 post-escritura. En debug builds, `push_error()` es triggered.

**AC.2.2 — No re-compute on balance change** — BLOCKING
GIVEN un WeaponInstance en state `NAMED` con `attack_final=115`
WHEN balance config del archetype cambia (e.g., Sword `base_attack` actualizado de 100 a 120 en next patch)
THEN `WeaponInstance.attack_final` retiene 115. La re-ejecución de formulas NO es triggered. El `.tres` file es unchanged (checksum idéntico).

**AC.2.3 — Mutable fields acceptance** — BLOCKING
GIVEN un WeaponInstance en state `EQUIPPED`
WHEN escribo a los 3 mutable fields: `state`, `equipped_by_hero_uid`, `cosmetic_overlay_id`
THEN los writes son aceptados. Escribir a cualquiera de los 14 frozen fields triggered `push_error()` sin mutación.

**AC.2.4 — Atomic freeze at NAMED transition** — BLOCKING
GIVEN un WeaponInstance en state `FORGING`
WHEN cualquiera de los 14 eventual-frozen fields son inspeccionados
THEN están todos en in-progress/default values. Freezing es atomic: entre FORGING y NAMED, NO existe estado intermedio donde algunos están frozen y otros no.

**AC.2.5 — signature_propagated_at write-once constraint** — BLOCKING
GIVEN un WeaponInstance en state `NAMED` o posterior con `signature_propagated_at=0`
WHEN PvP Async System cierra un match y escribe `signature_propagated_at = unix_timestamp_now`
THEN value aceptado. Escrituras subsecuentes aceptan SOLO values > current value (monotonically increasing); intento de `signature_propagated_at = 0` o un timestamp anterior triggered `push_error()` y no muta.

### H.3 Formula Correctness

Todos los cálculos usan defaults de G: tier_weight `[1,2,4,8]`; `compatible=1000`; `mismatch=500`.

**AC.3.1 — F2 Grade Bonus per-mille** — BLOCKING
GIVEN `forge_grade=80` WHEN grade_bonus computed THEN `grade_bonus_per_mille = 80 × 5 = 400`. GIVEN `forge_grade=0` THEN `= 0`. GIVEN `forge_grade=100` THEN `= 500`. Values fuera de `[0, 100]` son clamped antes de formula ejecutarse (AC.8.4).

**AC.3.2 — F3 Material Bonus Aggregation (Sword, compatible)** — BLOCKING
GIVEN 3x Iron Ore con `tier=1` (tier_weight=1), `stat_bonuses.attack=1000‰`, `damage_type_affinity="slash"` (match con Sword)
WHEN material_bonus.attack computed
THEN `material_bonus_aggregate.attack = (1000 × 1 × 1 × 1000 + 1000 × 1 × 1 × 1000 + 1000 × 1 × 1 × 1000) / 1000 = 3000‰`. (Ver Fórmula F3 para forma canónica.)

**AC.3.3 — F4 Stat Final (Sword, grade=80, 3x Iron Ore compat)** — BLOCKING
GIVEN base_attack=100, material_bonus_aggregate=3000, grade_bonus_per_mille=400
WHEN attack_final computed
THEN `material_comp = floor(3000 × 100 / 1000) = 300`; `grade_comp = floor(400 × 100 / 1000) = 40`; `final = 100 + 300 + 40 = 440`; clamp [1, 9999] → `attack_final = 440`. Valor exacto persistido en `.tres`.

**AC.3.4 — F4 Stat Final (Dagger, grade=0, 2x Iron compat + 1x Stone mismatch)** — BLOCKING
GIVEN Dagger `base_attack=70`, 2x Iron Ore (T1, compat=1000) + 1x Stone (T1, mismatch=500 vs pierce), grade=0
WHEN attack_final computed
THEN `material_bonus = (1000×1×1×1000 + 1000×1×1×1000 + 1000×1×1×500)/1000 = 2500‰`; `material_comp = floor(2500×70/1000)=175`; `final = 70+175+0 = 245`.

**AC.3.5 — F4 Overflow/Underflow clamp** — BLOCKING
GIVEN un input pathológico que produciría `stat_final > 9999` (e.g., Hammer base=145, 10x T4 compat, grade=100)
THEN `stat_final = 9999` exacto (not 10000+). Input que produciría `< 1` → `stat_final = 1`.

**AC.3.6 — F5 Element Dominance tiebreak** — BLOCKING
GIVEN 2x FIRE (T2 cada, tier_sum=4) + 2x ICE (T1 cada, tier_sum=2) WHEN element resolved THEN `element_id = &"fire"` (higher tier_sum). GIVEN 1x FIRE T1 + 1x ICE T1 (tier_sum tie, FIRE encountered first in materials array) THEN `element_id = &"fire"` (first-encountered tiebreak).

**AC.3.7 — F5 Neutral fallback** — BLOCKING
GIVEN 0 materials con tag element (todos Neutral)
WHEN element resolved
THEN `element_id = &"neutral"` (no null, no error).

**AC.3.8 — F1 Rarity Gate determinism** — BLOCKING
GIVEN `forge_session_id="rarity-test-001"` + idéntico material set + `forge_grade=85` + 2x Epic materials
WHEN rarity resolved en 3 runs consecutivos
THEN los 3 runs producen `rarity=5 (Legendary)` (per G.2: `RARITY_5_EPIC_COUNT_MIN=2` AND `RARITY_5_GRADE_MIN=85`). Diferente `forge_session_id` con mismos materials puede producir distinto affix roll pero mismo rarity (rarity es deterministic, no RNG).

### H.4 State Machine Transitions

**AC.4.1 — Valid forward path** — BLOCKING
GIVEN un nuevo forge inicia
WHEN player progresa cada etapa (forge, name, equip, PvP defend, remove from defense, unequip, retire)
THEN states transicionan exactamente: FORGING → FORGED_UNNAMED → NAMED → EQUIPPED → DEFENDING → EQUIPPED → NAMED → RETIRED. Cada transición es persisted al `.tres` y readable post-restart.

**AC.4.2 — Illegal transition rejection** — BLOCKING
GIVEN un WeaponInstance en state `NAMED`
WHEN code intenta setear `state=FORGING` (backward) o `state=DEFENDING` (skip EQUIPPED)
THEN state retiene `NAMED`. Warning/error logged. El `.tres` no escrito con invalid state.

**AC.4.3 — DEFENDING unequip prohibition** — BLOCKING
GIVEN un WeaponInstance en state `DEFENDING`
WHEN hero intenta unequip antes de removerse del PvP defense roster
THEN la operación es rechazada; state retiene `DEFENDING`; `equipped_by_hero_uid` no muta; error UI/log.

**AC.4.4 — RETIRED → DELETED (no resurrection)** — BLOCKING
GIVEN un WeaponInstance en state `RETIRED`
WHEN player confirma delete
THEN state transitiona a `DELETED`; instance no retornado por ninguna roster query; el `.tres` purged en next save. Intento de reverse (DELETED → RETIRED o earlier) rechazado.

**AC.4.5 — ORPHANED state auto-transition at load** — BLOCKING
GIVEN un WeaponInstance `.tres` en disk con `archetype_uid="sword_deleted_01"` para un archetype que ya no existe
WHEN el game load el `.tres`
THEN state auto-transiciona a `ORPHANED` (7); warning logged con el orphan UID; weapon es visible en inspection UI pero NO equipable/forjable; state transitions permitidas: ORPHANED → RETIRED, ORPHANED → DELETED (no otras).

### H.5 PvP Snapshot & Serialization

**AC.5.1 — to_dict() completeness** — BLOCKING
GIVEN un WeaponInstance en state `EQUIPPED`
WHEN `to_dict()` called
THEN dict retornado contiene todos los 14 frozen fields + `pvp_snapshot_version` + `signature_propagated_at`; todos los values son primitives (String, int, Array[primitive]); NO nested Resource refs. Dict es JSON-serializable sin procesamiento adicional.

**AC.5.2 — to_dict() NOT duplicate_deep() for networking** — BLOCKING
GIVEN cualquier codepath en PvP networking layer
WHEN weapon snapshot preparado para transmisión
THEN code llama `to_dict()` y NO `duplicate_deep()`. Grep "duplicate_deep" en `src/networking/` y `src/gameplay/pvp/` retorna cero matches en files de WeaponInstance serialization.

**AC.5.3 — Snapshot freshness across retire** — BLOCKING
GIVEN un PvP match inicia y snapshot de WeaponInstance "sword_001" capturado via `to_dict()` cuando state=EQUIPPED
WHEN weapon transitiona a `RETIRED` en el device del player antes de que el replay sea procesado
THEN replay usa el captured snapshot dict unchanged; NO re-query del live WeaponInstance. Replay completa sin error y refleja stats at match-start time.

**AC.5.4 — Round-trip fidelity** — BLOCKING
GIVEN un WeaponInstance con `attack_final=440`, `rarity=3`, `element_id=&"fire"`, `affix_slots=[AffixInstance_01, AffixInstance_02]`
WHEN `to_dict()` called y resultado es usado para reconstruir un read-only snapshot struct
THEN todos los field values en snapshot match originals exactly; no floating-point drift (TODOS los fields son int); String fields byte-identical.

### H.6 Determinism & Replayability

**AC.6.1 — Same seed, same affixes cross-platform** — BLOCKING
GIVEN `forge_session_id="determinism-test-42"` + material set idéntico en Android + iOS + Web HTML5
WHEN `AffixSystem.roll_affixes(seed, ...)` called en los 3 platforms
THEN el resultant `affix_slots` array es byte-idéntico en los 3. Test documentado en `tests/integration/forge/determinism_test.gd` con expected output hardcoded como reference.

**AC.6.2 — Replay produces identical WeaponInstance** — BLOCKING
GIVEN un recorded forge replay (`forge_session_id`, material list, `forge_grade`, `archetype_uid`, `player_signature`)
WHEN forge es re-ejecutado desde replay record
THEN WeaponInstance resultante tiene values idénticos para los 14 frozen fields vs. original. Pass como automated integration test.

**AC.6.3 — Seed isolation** — BLOCKING
GIVEN dos `ForgeSession` concurrent con distintos `forge_session_id`
WHEN ambos complete
THEN cada affix roll es independent; seed "A" no influencia result de seed "B". Verificado running sequentially y en parallel simulado + comparar outputs.

### H.7 Anti-P2W Constraints

**AC.7.1 — MAX_ROSTER_SIZE enforcement at FORGING start** — BLOCKING
GIVEN un player con 12 WeaponInstances donde al menos una está en state `FORGING`, `FORGED_UNNAMED`, `NAMED`, `EQUIPPED`, o `DEFENDING` (i.e., NO RETIRED/DELETED/ORPHANED)
WHEN player intenta iniciar forge #13
THEN `ForgeSystem.create_weapon(...)` retorna `null` + emite `forge_failed("roster_full")`; materials NO consumidos; no 13th WeaponInstance creado. Este límite NO es bypasseable por ningún IAP/premium currency flow.

**AC.7.2 — power_rating never displayed in HUD/mini-cards** — BLOCKING
GIVEN cualquier WeaponInstance
WHEN HUD, weapon mini-card, roster screen, PvP post-match screen rendered
THEN ningún UI element muestra label "Power Rating", "PR", o un valor numérico igual al `power_rating`. Grep "power_rating" en `src/ui/` y `src/gameplay/hud/` retorna cero display-binding references.

**AC.7.3 — signature propagation post-match only** — BLOCKING
GIVEN un WeaponInstance con `signature_propagated_at=0` (pre-match)
WHEN pre-match PvP preview UI query el `player_signature` del oponente
THEN `player_signature` es NOT displayed. UI oculta el field hasta que `signature_propagated_at > 0`. Verificable vía QA debug tool que muestre el valor del field + UI screenshot.

### H.8 Error Handling & Recovery

**AC.8.1 — Orphan archetype graceful load** — BLOCKING
GIVEN un WeaponInstance `.tres` con `archetype_uid` que no resuelve
WHEN `ResourceLoader.load(...)` procesa el file
THEN WeaponInstance loads sin crash; auto-transitiona a state `ORPHANED` (ver AC.4.5); warning logged con orphan UID; game continúa sin invalid state.

**AC.8.2 — Schema version mismatch (provisional — see Open Questions)** — ADVISORY
GIVEN un WeaponInstance `.tres` con `schema_version=1` loaded por code esperando `schema_version=2`
WHEN ResourceLoader procesa file
THEN un migration handler es invoked (si Save/Load GDD lo define) o el load es rejected con logged error. Game NO crashea; player NO recibe weapon silenciosamente con null/default stats. Pass/fail exacto definido cuando Save/Load GDD sea autorizado.

**AC.8.3 — Corrupted .tres field graceful recovery** — BLOCKING
GIVEN un WeaponInstance `.tres` donde `attack_final` fue corrupted a String ("corrupted") en lugar de int
WHEN el file es loaded
THEN load reject con logged error + no add al roster, OR sanitize a safe default (e.g., 1) + log corruption warning. En ninguno de los casos game crashea o muestra el corrupted string en UI.

**AC.8.4 — forge_grade out-of-range clamp** — ADVISORY
GIVEN `forge_grade=-10` o `forge_grade=150` pasado al forge pipeline
WHEN `grade_bonus_per_mille` computed
THEN value clamped a `[0, 100]` antes de formula; `grade_bonus_per_mille` en `[0, 500]`; no negative ni >500; warning logged identificando out-of-range input y clamp action.

### H.9 Coverage Matrix

| Core Rule | Covered by |
|-----------|------------|
| R1 Creation Authority | AC.1.1, AC.1.3, AC.1.4 |
| R2 Immutability Invariant | AC.2.1, AC.2.2, AC.2.3, AC.2.4 |
| R3 State enum | AC.4.1, AC.4.2, AC.4.3, AC.4.4, AC.4.5 |
| R4 Archetype-Instance separation | AC.1.4, AC.2.2 |
| R5 Stats frozen at naming | AC.2.2, AC.3.3, AC.3.4 |
| R6 power_rating not displayed | AC.7.2 |
| R7 Signature propagation post-match only | AC.7.3, AC.2.5 |
| R8 MAX_ROSTER_SIZE=12 not purchasable | AC.7.1 |
| R9 to_dict() NOT duplicate_deep() | AC.5.1, AC.5.2 |
| R10 Forge session seed determinism | AC.6.1, AC.6.2, AC.6.3, AC.3.8 |

| Fórmula | Covered by |
|---------|------------|
| F1 Rarity Gate | AC.3.8 |
| F2 Grade Bonus per-mille | AC.3.1 |
| F3 Material Bonus Aggregation | AC.3.2, AC.3.4 |
| F4 Stat Final | AC.3.3, AC.3.4, AC.3.5 |
| F5 Element Dominance | AC.3.6, AC.3.7 |
| F6 Power Rating | (covered indirectly: AC.7.2 visibility; correctness owned by Matchmaking GDD) |

Criterios totales: 31 (28 BLOCKING + 3 ADVISORY).

## Open Questions

Preguntas que el GDD NO resuelve y que deben ser respondidas antes o durante la implementación. Cada una tiene owner tentativo + trigger de resolución.

### OQ.1 Schema migration policy (BLOCKS AC.8.2)
**Pregunta**: Cuando el `schema_version` cambia entre versiones del juego, ¿los WeaponInstances antiguos se migran forward, se quarantine, o se rechazan? ¿Quién owns el migration logic — WeaponInstance o un loader utility de Save/Load?
**Owner**: Save/Load & Persistence GDD (System #2, aún no authorizado).
**Resolution trigger**: Al autorizar el Save/Load GDD, retrofitear AC.8.2 con pass/fail concreto y agregar migration handler signature a la schema en C.2.
**Risk si no resuelto**: Breaking changes post-launch pueden hacer que weapons legacy desaparezcan silenciosamente del roster del player → perceived data loss → trust shock.

### OQ.2 AffixSystem.roll_affixes() final signature (BLOCKS F.1.4 contract)
**Pregunta**: La signature provisional asumida es `roll_affixes(seed: int, slot_count: int, slot_tiers: Array[int], rarity: int) -> Array[AffixInstance]`. ¿Es correcta? ¿Toma input adicional (archetype bias, element bonus)?
**Owner**: Affix System GDD (System #10, aún no authorizado).
**Resolution trigger**: Al autorizar Affix GDD, si la signature difiere, adaptar Fórmula F3 + campo `affix_slots` + AC.6 tests.
**Risk**: Asunción errada → refactor masivo al forjar contract con Forge.

### OQ.3 Combat Simulation determinism contract (BLOCKS F.2.2 + AC.6)
**Pregunta**: Combat Sim se asume que consume solo integers desde `WeaponInstance.get_combat_stats()`. ¿Puede Combat Sim requerir floats para alguna operación (trigonometría, angles, velocities)? Si sí, ¿cómo se preserva cross-platform determinism?
**Owner**: Combat Simulation GDD (System #13, aún no authorizado) + Technical Director.
**Resolution trigger**: Antes de Combat Sim GDD, ADR sobre fixed-point lib (native GDScript vs GDExtension). Referencia: "Simulación determinista: fixed-point lib a usar" ya está en session state open questions.
**Risk**: Si Combat Sim introduce floats sin determinism framework → PvP async no-replayable en devices distintos.

### OQ.4 Matchmaking Power Rating weights (BLOCKS F.6 numerical correctness)
**Pregunta**: Los weights proposed en F6 (`POWER_STAT_WEIGHT=3`, `POWER_CRIT_DIVISOR=5`, etc.) son placeholder. ¿Cuáles son los weights finales tras calibración anti-P2W?
**Owner**: Matchmaking System GDD (System #19, aún no authorizado).
**Resolution trigger**: Matchmaking authorizes F6 weights + update registry + re-genera power_rating para weapons existentes? (decisión abierta: frozen post-NAMED vs re-roll on patch).
**Risk**: Weights mal calibrados → whales dominan leaderboard / F2P experience degrada.

### OQ.5 Tier 2 — Retired Vault semantics
**Pregunta**: MVP tratea RETIRED = funcionalmente DELETED (purga en next save). Tier 2 introduce Retired Vault (browseable, non-playable but preserved). ¿El Vault cuenta hacia MAX_ROSTER_SIZE? ¿Tiene su propio cap (e.g., 100)?
**Owner**: Design lead (decisión post-MVP).
**Resolution trigger**: Antes de Tier 2 content pass.
**Risk**: Si Vault cuenta hacia cap → confusión con "roster" mental model. Si no cuenta → storage explosion ilimitado.

### OQ.6 Tier 3 — Cosmetic overlay pipeline
**Pregunta**: `cosmetic_overlay_id: StringName` está reservado en schema (único mutable cosmético post-NAMED). ¿Cuál es el pipeline para crear overlays? ¿Son .tres adicionales loaded on-demand o sprite atlases monolíticos?
**Owner**: Cosmetic/Skin System GDD (Tier 3, not in MVP).
**Resolution trigger**: Pre-Tier 3 development.
**Risk**: Memory explosion en mobile si overlays son monolithic; load stutter si son on-demand sin asset streaming.

### OQ.7 Anti-spam forge policy
**Pregunta**: Forge System GDD owns grade degradation / cooldown mechanics. ¿Qué anti-spam garantiza que un whale no pueda forjar 1000 weapons/día buscando Legendary drops? (relevante para F1 Rarity Gate balance)
**Owner**: Forge System GDD (System #16, GDD #4 de prototype-critical).
**Resolution trigger**: Forge GDD authorization.
**Risk**: Sin anti-spam → Rarity Gate defaults trivializables → devaluación de fantasy del "weapon especial".

### OQ.8 Signature profanity / moderation
**Pregunta**: `player_signature` acepta 32 chars UTF-8. ¿Filtro de palabras prohibidas? ¿Moderation server-side para signatures propagados a otros players (PvP post-match)? ¿Reporte del player si recibe signature abusivo?
**Owner**: Community/Moderation pipeline (no GDD aún).
**Resolution trigger**: Antes de PvP Async launch.
**Risk**: Abuse vector — signatures visibles a otros players pueden contener harassment. Legal + platform (Apple/Google) pueden requerir moderation framework.

### OQ.9 ORPHANED state retire UX
**Pregunta**: Cuando un player load y descubre weapons ORPHANED (archetype borrado por patch), ¿se le notifica proactivamente o solo al abrir roster? ¿El auto-transition a ORPHANED es visible in una notification feed?
**Owner**: UX Designer (UI.7 de este GDD ya establece lo core, pero el flow de discovery necesita más spec).
**Resolution trigger**: Pre-soft-launch.
**Risk**: Player confusion — "¿dónde está mi weapon?" si la notification no es prominent.

### OQ.10 PvP snapshot dict schema version drift
**Pregunta**: `pvp_snapshot_version: int` está en schema (default 1). Si la forma del dict retornado por `to_dict()` cambia en una version futura, los replays capturados en version antigua necesitan ser reinterpretados. ¿Hay un migration map para dict schemas o solo se rechazan replays legacy?
**Owner**: Replay System GDD (System #17, aún no authorizado).
**Resolution trigger**: Replay GDD authorization.
**Risk**: Replay library invalidation en patches → trust shock similar a OQ.1.
