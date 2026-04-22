# Forge System

> **Status**: In Design
> **Author**: lucianodepalo-ai + Claude (game-designer + systems-designer + economy-designer agents)
> **Last Updated**: 2026-04-22
> **Implements Pillar**: Pillar 1 (Estética primer impacto) + Pillar 2 (Variabilidad emergente) + Pillar 3 (Sesión corta, profundidad infinita) + Pillar 4 (Meta emergente) — **DIRECT on all four**
> **System #**: 16 — Feature layer, MVP priority, **core hypothesis validator**

---

## Overview

**Forge System** es el sistema Feature que transforma materiales + skill del jugador en una `WeaponInstance` firmada. Es el **core loop atómico** de *The Meta-Forge*: sin este sistema no hay juego. Todo lo demás (PvP, matchmaking, progresión, economía, replay, afijos) existe para dar significado a lo que pasa durante los ~30 segundos de una forja.

El sistema opera en dos capas:

- **Capa de datos (infra)**: El `ForgeSystem` es la **única autoridad de creación** de `WeaponInstance` (Weapon Entity Rule 1). Orquesta el contrato atómico `roster_cap_check → signature_valid → archetype_valid → material_catalog_valid → consume_materials → run_minigame → grade_output → rarity_gate → stat_resolve → affix_roll(seed) → freeze → emit forge_completed`. Maneja una state machine de 4 estados (`IDLE → SELECTING → FORGING → REVEALING`), emite eventos hacia Audio/VFX/UI, genera un `forge_session_id` (UUID v4) que sirve de seed determinista para que el Replay System re-derive los afijos idénticamente iOS/Android/Web. Es el **único punto de contacto** entre Material System (consume) y Weapon Entity System (create).

- **Capa de experiencia (player-facing)**: El jugador elige 2–4 materiales de su inventario, selecciona un archetype (sword / dagger / hammer en MVP), escribe su firma si aún no la tiene, y entra al minijuego — 15–20 segundos de timing + secuencia donde cada input acierto eleva el `forge_grade` (0–100). El grade no es solo un stat boost: es la **ejecución hecha número**, la evidencia mecánica de que la forja fue tuya. Al completar, el arma se revela con nombre pendiente; el jugador la nombra; entra al roster. El ciclo total es de ~30 segundos y es el átomo que el Pillar 3 demanda ("10 minutos diarios dejan al jugador satisfecho").

**MVP scope**: minijuego con **2 input types** (timing + secuencia, estilo Paper Mario / Legend of Legaia Arts); **1 minigame template compartido** entre los 3 archetypes (no mini-mecánicas separadas por arquetipo en MVP — abierto como Open Question para Tier 2); **2–4 materiales por forja** (cap por archetype, tuning knob); **diegetic timing indicator** (brasas, color del metal) — no overlay de accesibilidad (decisión heredada del Art Bible y del Weapon Entity GDD). **Anti-spam policy**: grade degradation curve — forjas consecutivas en < N minutos degradan el máximo grade alcanzable, cortando el incentivo al tap-farming sin matar el ritmo del jugador comprometido. Parámetros calibrables.

**Sin este sistema**: no hay loop de 30 segundos, no hay skill ceiling del jugador (sin grade no hay diferencia entre "forjar con intención" vs. "forjar dormido"), no hay seed para los afijos (Replay se rompe), no hay firma que propague en PvP, no hay origen para la identidad de cada arma. Es el **validador de la hipótesis central** del juego: si los 30 segundos del forge no enganchan a los 5 minutos, ningún sistema downstream salva al producto.

### Pillar alignment

| Pillar | Mapeo | Cómo se manifiesta |
|---|---|---|
| **1 — Estética primer impacto** | DIRECT | La forja es el momento más cálido del loop: brasas, chispas, metal incandescente, reveal del arma nombrada. Juice del minigame es no-negociable. |
| **2 — Variabilidad emergente** | DIRECT | `forge_grade` + `forge_session_id` + materials combination producen las ~10^N combinaciones semánticas legibles. El grade es la variable que introduce el skill del jugador en una ecuación de otra forma determinista. |
| **3 — Sesión corta, profundidad infinita** | DIRECT | Ciclo atómico de ~30s. Skill ceiling del minijuego (timing window, sequence recall, input chaining) sostiene la profundidad infinita. |
| **4 — Meta emergente, no impuesto** | DIRECT | Forge es donde "qué materiales + qué skill + qué firma" se fusionan en un objeto que propaga reputación. El meta emerge de lo que los jugadores hacen acá, no de lo que el equipo balancea ex-post. |

## Player Fantasy

**Forge is The Duet that settles into a seal.**

The player enters the workshop with three materials cupped in their hands, walks to the anvil, and for thirty seconds they are not alone. The metal speaks first — a slow phrase of glow and sound — and the player answers with the hammer. This is not a rhythm game; there is no scrolling chart, no *PERFECT* popping out of the anvil, no countdown timer. The metal sets the phrase; the player finishes it. They play the metal the way a jazz musician plays a standard: the melody exists before them, but the interpretation is theirs alone. And when the last strike lands and the metal goes for a single frame pure white, the song stops — not in celebration, but in settling. The blade is *someone* now. The player's hand leaves the hammer and a name-field appears, cursor blinking. They've been the duet partner for thirty seconds. Now they become the author.

### Emotional beats

- **The opening note.** The bellows exhale. The metal goes from dull red to sullen orange and the workshop's light shifts warmer. The player's first strike lands in the gap the fire leaves for it — a narrow diegetic invitation, not a prompted button press. Their shoulder drops. They're in. The game has said *here* without printing the word.

- **The run of three.** Three clean strikes in a row and the forge changes register. The sparks come higher. The hammer rings deeper. The player feels the song getting louder *because of them* — not as a combo multiplier ticking up on screen, but as a rising warmth in the scene. Shadows on the workshop wall recede. The fire leans toward the anvil. The room is on their side.

- **The crescendo.** The metal is at the color the player has been waiting for. They land the final strike. For one frame the metal is pure white — not a success VFX popping out of nothing, but a shared breath between the player and the fire before it cools. Something was agreed upon in that frame. Neither of them will speak of it afterward.

- **The seal.** The last strike settles. The metal dims into a blade. The hammer is heavy in the player's hand again — it was weightless during the duet, it remembers gravity now. The blade on the anvil is no longer *a material arrangement* — it is *someone*, pending only a name. The pantry is lighter by three materials. The workshop smells like iron and smoke. The player's breath is still caught in the frame where the metal was white.

- **The cooling (anti-spam, felt not announced).** If the player comes back to the anvil ten seconds after the previous forge and slams another combination in, the fire does not protest — it simply recedes. The light goes cooler. Shadows creep in at the edges of the screen. The strikes still land, but the metal does not *sing* — it clanks. The grade ceiling falls with the room's temperature. The player feels the workshop withdraw approval, and no popup tells them why. They come back tomorrow and the fire is tall again, leaning toward them.

### What this fantasy is NOT

- **NOT a rhythm game.** No scrolling note chart. No *PERFECT / GREAT / GOOD / MISS* callouts flying out of the anvil. No music-synced hitboxes. The duet is **diegetic**, not charted.
- **NOT a guitar hero combo.** A run of good strikes is not counted at the player — it is **felt** via the forge's rising register (audio deepening, sparks higher, light warmer). The number exists (grade 0–100) but the number is not the fantasy; the fantasy is the room getting louder in their favor.
- **NOT a countdown timer.** The 15–20-second minigame has an end, but no screaming *"5... 4... 3..."*. The metal cools as a diegetic clock — the player reads the color, not the UI.
- **NOT a gacha.** No jackpot audio on high rarity. No confetti. The arma emerges named by the player's execution, not declared by the house. A Common forged with intention outranks an Epic produced on a spam loop — because the fire was leaning toward the careful player and withdrawing from the hurried one.
- **NOT a skill check the player can fail.** Every forge produces a weapon. A bad duet produces a Rough blade — but Rough is still *theirs*, still signed, still on the rack. The fantasy is not "pass/fail" — it is *how well you listened*.

### Tonal braid with Material and Weapon Entity

**Materials breathe; the forge sings, then settles; weapons testify.** The pantry is preparation — the held breath, the jars, the decision "today I combine them." The forge is performance — the duet, the rising register, the single white frame. The weapon is the recording the world will hear — the name, the overnight letter, the re-read. Each system owns one beat of a three-beat braid: **anticipation → ignition → testimony**. Forge is the ignition. It spends the pantry's potential and produces the blade's evidence. It is the only system of the three in which the player is *kinetically* present — materials are stewardship, weapons are legacy, forge is the body in the room with the hammer.

### Pillar mapping

- **Pillar 1 — Estética primer impacto** (DIRECT): The workshop's warm palette, the metal's color-as-diegetic-clock, the music crescendoing with the duet, the single-frame white on the crescendo strike — Forge is the most sensory-dense moment of the loop. Everything the art bible's "Weighted Pigment" and "Kinetic Honesty" principles promise is cashed out here or nowhere.
- **Pillar 2 — Variabilidad emergente** (DIRECT): `forge_grade` (0–100) is the only variable in the entire weapon-creation pipeline that is neither deterministic (materials) nor seeded-RNG (affixes). It is the player's real-time contribution, and it cascades into rarity gate, stat resolution, and the affix tier envelope. Variability is not dice-rolls; it is *the player's duet performance made stat*.
- **Pillar 3 — Sesión corta, profundidad infinita** (DIRECT): The ~30-second atomic cycle is Pillar 3's literal clock. Depth comes from skill ceiling — a player in month three reads the fire's opening phrase faster, holds the tempo tighter, strikes the white-frame crescendo more consistently. The loop does not demand more time; it rewards more *attention*.
- **Pillar 4 — Meta emergente, no impuesto** (DIRECT): Forge is the only place where "what I did" and "what I made" become the same object. The blade's identity — stats, rarity, affixes, signature — is produced by the fusion of *material composition* (choosable) and *duet performance* (skillable). The meta that emerges in PvP is a meta of *readings* — what compositions the community discovered, what duet styles produce consistent grade, what archetypes reward which composition. The team designs the instruments; the community writes the songs.

## Detailed Design

### C.1 — Core Rules

**Rule 1 — Precondition Check Order (Fail-Fast, Ordered).** Antes de cualquier forja, el sistema corre estas checks en secuencia estricta. La primera falla termina el intento y retorna la reason a la UI: (1) `roster_cap_check` — el roster debe tener al menos un slot libre (`active_weapons < MAX_ROSTER_SIZE`, Weapon GDD); (2) `signature_valid` — player_signature no vacío y ≤ 32 chars UTF-8 (Weapon Rule 5); (3) `archetype_valid` — el archetype StringName resuelve en el catálogo; (4) `material_catalog_valid` — cada material ID existe en el catálogo; (5) `material_count_valid` — `MIN_MATERIALS_PER_FORGE ≤ count ≤ archetype.max_materials` (tuning knob, default 2–4). No se consume material hasta que las 5 checks pasan. Las reasons son exactamente las de Weapon Entity E1.1–E1.7: `materials_empty`, `invalid_signature`, `unknown_material_id`, `insufficient_materials`, `roster_full`, `archetype_missing`.

**Rule 2 — Selection Phase (Pre-FORGING, Cancellable).** El jugador ve: tray de materiales con preview de stat contribution, archetype card seleccionable, y signature prompt (solo primera forja o si signature cambió). El jugador puede modificar selección libremente. Este es el **último momento de cancelación**; tapear "Begin the Forge" transiciona a FORGING y bloquea todos los inputs. Selection phase NO es parte del ciclo atómico — no tiene time constraint.

**Rule 3 — forge_session_id Generation (FORGING Entry).** Inmediatamente al entrar a FORGING — **antes** de consumir material — el sistema genera un UUID v4 `forge_session_id` con 64 bits de entropy (registry constant `FORGE_SESSION_ID_BITS = 64`). Este ID: se pasa a AffixSystem como deterministic affix seed; se loggea al Replay ledger con timestamp, archetype, material IDs, y hash del signature; se embebe en el WeaponInstance resultante. Si la forja falla o se interrumpe post-generation, el ID se retira y nunca se reutiliza.

**Rule 4 — Material Consumption (Atomic, Immediate).** Los materiales se consumen en el mismo frame que se genera `forge_session_id` — antes del minigame. "El costo se paga al umbral, no al resultado." Si el device crashea mid-minigame, los materiales ya se gastaron; el sistema recupera via el `forge_session_id` log y produce un grade-0 result (ver Rule 12 y Section E crash recovery). Previene double-spend exploits y hace el umbral ritualmente significativo.

**Rule 5 — Minigame Structure: The Duet (15–20s).** La Duet es una secuencia de Strike Windows entregadas en tempo con el color phase del metal (Rule 7). Total strikes: `base_strikes = 8 + archetype.strike_modifier` (tuning knob; MVP: Sword=8, Dagger=10, Hammer=6). Cada Strike Window presenta uno de dos input types — nunca ambos simultáneos:

- **Timing Input**: pulso desde el anvil. Player tap dentro de `TIMING_WINDOW_MS` (default 180ms base tempo; 150ms hot phase; 120ms cooling phase). Sin countdown visual — el signal es el pico de brillo del metal.
- **Sequence Input**: secuencia de 3 glifos direccionales/posicionales (L/R/Center o swipe). Replicar dentro de `SEQUENCE_TIMEOUT_MS` (default 2000ms).

Ratio Timing/Sequence por archetype es tuning knob (MVP: 60% Timing / 40% Sequence, uniforme en los 3 archetypes).

**Rule 6 — Grade Accumulation (Duet portion, cap 80).** Base per-strike: `strike_value = floor(80 / base_strikes)`. Streak multiplier: 3+ strikes consecutivos aplican `×1.25` a strikes siguientes (cap `×1.5` a 5-streak). Miss resetea streak y resta `miss_penalty` (default 5 pts, floor 0 — grade nunca negativo). Late hit (fuera de window pero dentro 2× window) cuenta como miss sin resta pero suma 0. Grade es volatile hasta el Seal.

**Rule 7 — The Color Phase as Diegetic Clock.** El color del metal drives el tempo de la Duet y es la **única referencia de timing del jugador**. Tres fases no-lineales:

- **Blaze** (0–30% del time elapsed): naranja-blanco, tempo = `BASE_TEMPO_BPM = 72`, TIMING_WINDOW_MS = 180ms.
- **Ember** (30–70%): naranja-rojo oscuro, tempo `BASE_TEMPO_BPM × 1.15`, TIMING_WINDOW_MS = 150ms.
- **Cooling** (70–100%): crimson-gris, tempo vuelve al base pero TIMING_WINDOW_MS = 120ms (precisión sube porque el metal "stiffens").

Transiciones entre fases: crossfade de 2s, sin label ni UI announcement. La cooling penalty de Rule 10 manifiesta acá: shadows encroach el metal, color desatura hacia gris, comunicando que "la forja fue fría" sin popup.

**Rule 8 — Seal (Final Strike, ~5s, cap 20).** Después de los `base_strikes`, entra Seal phase. El metal settles. Un Timing Input final con `SEAL_WINDOW_MS = 300ms` (más indulgente que Duet — el Seal es flourish, no trap). Perfect Seal suma +20 al grade. Hit Seal suma +10. Miss Seal suma 0. Después: `grade = clamp(duet_grade + seal_grade, 0, min(100, grade_ceiling))` donde `grade_ceiling` viene de Rule 10. El sistema transiciona inmediatamente a REVEALING, invoca rarity_gate → stat_resolve → affix_roll(forge_session_id) → WeaponInstance.create → `forge_completed`.

**Rule 9 — No Cancel Mid-Forge.** Una vez en FORGING, cancelación es imposible. No back button, no pause interrupt, no gesture abort. Diseño, no oversight: la Duet es commitment, no audition. El costo (materiales) ya se pagó. Si el player cierra la app mid-forge, Rule 12 aplica. El UI comunica esto antes del entry: el confirm del "Begin the Forge" dice *"Once you strike, there is no retreat."*

**Rule 10 — Anti-Spam Cooling (Counter + Time model, grade ceiling).** El sistema trackea dos valores persistidos:

- `n`: `consecutive_hot_forges` — incrementa cuando un forge completa con `t_since_last < GRACE_WINDOW`; resetea a 0 cuando `t_since_last > RECOVERY_FULL`.
- `last_forge_completed_at`: Unix ms.

Formula del ceiling (detalle en Section D Formula F.4):

```
heat_penalty    = max(0, n - HOT_FORGE_FREE_COUNT) × PENALTY_PER_EXTRA_FORGE
time_recovery   = clamp((t - DEGRADATION_ONSET) / RECOVERY_DURATION, 0, 1)
grade_ceiling   = clamp(100 - heat_penalty + floor(time_recovery × heat_penalty), GRADE_FLOOR, 100)
```

MVP defaults: `GRACE_WINDOW=420s` (7 min), `HOT_FORGE_FREE_COUNT=3`, `PENALTY_PER_EXTRA_FORGE=20`, `RECOVERY_DURATION=1800s` (30 min), `GRADE_FLOOR=45` (encima del Rare gate=40). Los primeros 3 forges son free. Forge 4 ceiling=80 (Epic still reachable). Forge 5 ceiling=60 (Epic borderline). Forge 6+ ceiling=45 (Rare reachable, Epic+ lockeado). Recovery total en ~37 min. **First-forge-of-day: no bonus** (evita "optimal login time" meta). **Save loss: `n=0`, full heat** (penalización es perder las armas, no el acceso). Manifestación diegética vía Rule 7 (desaturation + shadow creep) — no popup, no badge numérica.

**Rule 11 — Prohibited Concurrent and Re-Forge States.** Exactamente **una forja** puede estar en FORGING o REVEALING simultáneamente — el sistema enforce global mutex. Una weapon en state FORGED_UNNAMED o superior NO puede ser re-forjada. El **Naming Step post-Seal** es mandatory y blocking — el player no puede skip, dismiss, ni navegar hasta confirmar un nombre válido (1–20 chars UTF-8, Weapon Entity E2.2). Hasta que la weapon tenga nombre, el WeaponInstance NO se escribe al roster.

**Rule 12 — Crash-Mid-Forge Recovery.** Si la app se cierra durante FORGING (materiales consumidos, minigame incompleto): al re-launch, Save/Load detecta `pending_forge_session` con status `consumed_not_resolved`. Recovery path: el sistema produce un **grade=0** WeaponInstance con los materiales registrados y archetype stored, entra a state FORGED_UNNAMED, y prompt al jugador para el naming. Materiales NO se reembolsan (atomicity invariant). Si catalog definitions cambiaron entre crash y re-launch (archetype/material migrations), la weapon resuelve con catalog actual; log warning. **Rationale**: nunca dejar al jugador con materiales perdidos Y sin arma — eso mata la confianza en el ritual.

---

### C.2 — ForgeSystem States and Transitions

Esta state machine es del **ForgeSystem** (orquestador del ciclo). Es **distinta** del WeaponInstance state machine (Weapon Entity §C.4), que trackea el lifecycle del arma forjada.

| Value | State | Entry condition | Mutable in this state | Transitions Out |
|---|---|---|---|---|
| `0` | `IDLE` | Game launch; `forge_completed` emitido y procesado; COOLING timer expira | Ninguno (ambient only) | → `SELECTING` cuando player abre Forge UI desde Hub |
| `1` | `SELECTING` | Player abre Forge UI | `pending_archetype`, `pending_materials`, `pending_name` (UI scratch — nada committed) | → `IDLE` on dismiss/back; → `FORGING` cuando Rule 1 checks pasan y player confirma |
| `2` | `FORGING` | Todas las preconditions pasan; `consume_materials()` retorna `true`; `forge_session_id` generado; `session_start_at` timestamped | `minigame_input_log` (append-only), `current_heat_phase` | → `REVEALING` cuando minigame emite `grade_resolved(grade)` |
| `3` | `REVEALING` | Minigame resuelve grade ∈ [0, 100]; `rarity_gate` + `stat_resolve` + `affix_roll(seed)` ejecutados; `WeaponInstance.create_weapon()` retorna | Ninguno (read-only hand-off) | → `COOLING` inmediatamente al emit `forge_completed` |
| `4` | `COOLING` | `forge_completed` emitido; `last_forge_completed_at` stamped; counter `n` incrementado si `t < GRACE_WINDOW` | `cooling_remaining_sec` (countdown, informational only) | → `IDLE` cuando counter-based recovery aplica (ver Rule 10) |

**Transition guards**:
- `SELECTING → FORGING`: todas las Rule 1 checks pass + `consume_materials()` returns `true`
- `FORGING → REVEALING`: `grade ∈ [0, 100]` (integer, clamp + warn log si fuera de rango)
- `REVEALING → COOLING`: `forge_completed(weapon, grade)` emitido; WeaponInstance en FORGED_UNNAMED accesible
- `COOLING → IDLE`: cooling recovery complete (counter-based, not wall-clock alone)
- `IDLE → SELECTING`: sin guard (player tap abre UI)

**Blocked transitions** (rechazadas con `push_error` en debug):
- `FORGING → IDLE` — no cancel path (Rule 9). Crash recovery es la única interrupción legítima.
- `FORGING → SELECTING` — idem Rule 9.
- `REVEALING → SELECTING` — resultado ya computado, WeaponInstance ya creado.
- `SELECTING → REVEALING` (skip FORGING) — minigame no es opcional; grade=0 es mínimo, no bypass.
- Any state → `FORGING` while already `FORGING` — global mutex (Rule 11). Emite `forge_failed("forge_in_progress")`.

---

### C.3 — Interactions with Other Systems

| System | Direction | Contract | Owner |
|---|---|---|---|
| **Data-Driven Config** | Forge reads | `WeaponArchetypeDefinition` + `MaterialDefinition` + nuevo `ForgeConfig` resource (cooling constants, minigame params, grade clamp bounds). Forge NUNCA escribe config. Load at session start, cached. | Config owns schema; Forge reads |
| **Material System** | Forge reads + writes | `has_materials(reqs) -> bool` gate check en SELECTING (non-destructive). `consume_materials(reqs) -> bool` atómico en SELECTING→FORGING. Si `consume` retorna false after `has` true (race), abort, `forge_failed("insufficient_materials")`. Forge NUNCA lee `_inventory` directo. | MaterialSystem owns inventory; Forge is consumer |
| **Weapon Entity System** | Forge writes (exclusive) | `WeaponInstance.create_weapon(archetype, materials, forge_grade, player_signature, weapon_name, seed) -> WeaponInstance` es la **única vía** de creación (Weapon Rule 1). Called during REVEALING post rarity_gate + stat_resolve + affix_roll. Forge genera el `forge_session_id` (UUID v4, 64-bit). Emite `forge_completed(weapon, grade)` post-create. | Forge owns creation call + session_id; WeaponSystem owns instance lifecycle |
| **Affix/Mutation System** (provisional) | Forge calls | `AffixSystem.roll_affixes(seed, slot_count, slot_tiers, rarity) -> Array[AffixInstance]`. Called once during REVEALING post rarity_gate. Seed = `forge_session_id` (deterministic). Forge pasa result directo a `create_weapon`. | Forge owns seed derivation; AffixSystem owns roll logic + AffixInstance schema |
| **Save/Load** | Forge reads + writes | Persiste: `last_forge_completed_at: int`, `consecutive_hot_forges: int`, `pending_forge_session: Dictionary` (crash recovery). **Crash recovery (Rule 12)**: on re-launch si Save/Load detecta `pending_forge_session` status `"consumed_not_resolved"` → produce grade=0 WeaponInstance con inputs stored. Materiales NO reembolsan. Catalog migration: resuelve con catalog actual + warning log. | Forge owns session record write; Save/Load owns persistence + re-launch detection |
| **Audio System** (provisional) | Forge emits | Event bus: `forge_strike_played(power_0_1)` durante FORGING per strike; `forge_heat_changed(heat_0_1)` on phase delta; `forge_reveal_played(rarity)` al REVEALING entry; `forge_cooling_entered()` al COOLING entry; `forge_anvil_cooled()` cuando Rule 10 penalty aplica. Forge no llama Audio API directo. | Forge owns timing; Audio owns playback + mix |
| **VFX Framework** (provisional) | Forge calls | `VFXFramework.trigger_flipbook(id, position)` + `emit_particles(emitter_id, count)` en FORGING y REVEALING. Hard cap: 4 emitters × 150 particles simultaneous (art bible §8.5). REVEALING entry triggera rarity-specific flipbook. Forge pasa `rarity` context; VFX selecciona asset. | Forge owns trigger; VFX owns asset + budget enforcement |
| **Input System** (provisional) | Forge subscribes | `InputSystem.get_timestamped_touch() -> Dictionary{position, timestamp_ms}` polled durante FORGING only. Precision guarantee ≤ 50ms jitter. Forge appenda cada touch a `minigame_input_log` (append-only) para replay reconstruction. Unsubscribe al salir de FORGING. | Input System owns device event capture; Forge owns log |
| **Forge Minigame UI** | Bidirectional | UI lee ForgeSystem state (heat phase, grade progress) para render. UI NUNCA escribe a ForgeSystem — user actions van vía InputSystem. UI emite `player_confirmed_forge()` que SELECTING escucha como trigger de precondition check. | ForgeSystem owns state; UI owns presentation; Input es el channel |
| **Hub HUD** | Forge → HUD | HUD polls `ForgeSystem.is_cooling() -> bool` para badge "Workshop is cooling" (solo visible cuando `consecutive_hot_forges > HOT_FORGE_FREE_COUNT`). Read-only, no signal subscription. | Forge owns state; HUD owns badge logic |
| **Replay System** | Forge → Replay | Forge embebe `forge_session_id` + `minigame_input_log: Array[Dictionary]` en completed forge record. Replay Tier 2 reconstruye minigame deterministically. MVP: Forge escribe al WeaponInstance; Replay es no-op consumer. Migration: `minigame_input_log` schema debe incluir `version` field. | Forge owns session_id + log; Replay owns storage + reconstruction |
| **Economy System** (provisional) | Forge reads | MVP default: forge cost = 0 gold. `ForgeConfig.gold_cost_per_forge` (tuning knob). Si non-zero, Forge llama `EconomySystem.deduct(amount) -> bool` como precondition gate (E1.0, pre-roster-check). Failure → `forge_failed("insufficient_gold")`. Forge NUNCA lee balance directo. | Economy owns balance; Forge owns cost gate call; ForgeConfig owns knob |

## Formulas

Todas las fórmulas usan integer math para preservar determinismo cross-platform (iOS / Android / Web). Los variables per-mille / per-centum se expresan como ints y se dividen al final (truncation consistente).

### Formula D.1 — Strike Value Per Hit (integer division floor)

Convierte un strike perfecto dentro de la Duet en puntos de grade.

`strike_value = floor(80 / base_strikes)`

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `base_strikes` | bs | int | 6–10 | Total strikes en la Duet según archetype (Sword=8, Dagger=10, Hammer=6) |
| `strike_value` | sv | int | 8–13 | Grade points por strike perfect (sin streak multiplier) |

**Output Range**: `strike_value ∈ [8, 13]` en rango MVP. Sword strike_value=10. Dagger strike_value=8 (más strikes, cada uno pesa menos). Hammer strike_value=13 (menos strikes, cada uno pesa más).

**Rationale**: `80 / bs` garantiza que la Duet **pura** (sin streak, sin miss, sin Seal) topea en 80 — dejando 20 para el Seal (Rule 6/8). Archetype asymmetry: Dagger pide precision sostenida (más strikes), Hammer pide impact timing (menos strikes, cada uno más decisivo).

**Ejemplo**: Sword (bs=8) → `sv = floor(80/8) = 10`. Un Duet de 8 strikes perfect acumula `10 × 8 = 80` grade (antes de streak multiplier).

---

### Formula D.2 — Streak Multiplier (stepped integer)

Bonifica consistencia del jugador durante la Duet.

```
streak_multiplier = 100                if streak < 3         (×1.00)
                  = 125                if 3 ≤ streak < 5     (×1.25)
                  = 150                if streak ≥ 5         (×1.50)
```

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `streak` | st | int | 0–base_strikes | Consecutive perfect strikes en la Duet actual |
| `streak_multiplier` | sm | int | 100, 125, 150 | Multiplicador per-mille (÷100 para aplicar) |

**Output Range**: `streak_multiplier ∈ {100, 125, 150}`. Siempre per-mille (base 100 = 1×). Divisor 100 al aplicar para preservar integer math.

**Rationale**: Stepped en vez de lineal para que el jugador **sienta** el upgrade a ×1.25 y ×1.5 como breakthroughs discrete, no como progreso imperceptible. Cap ×1.5 para que un streak perfecto no colapse el cap de 80 Duet (streak de 8 en Sword: `8 × 10 × 1.5 = 120` — clamped por D.3 to 80).

**Ejemplo**: Sword streak 4 → sm=125. Strike 4 aporta `floor(10 × 125 / 100) = 12` grade (en vez de 10).

---

### Formula D.3 — Duet Grade Accumulation (running total)

Suma contributions per-strike con clamp al Duet cap.

```
for each strike in strikes:
    if strike == PERFECT:
        streak += 1
        contribution = floor(strike_value × streak_multiplier(streak) / 100)
        duet_grade += contribution
    elif strike == MISS:
        streak = 0
        duet_grade = max(0, duet_grade - miss_penalty)
    elif strike == LATE:
        streak = 0
        # no deduction, no contribution

duet_grade = clamp(duet_grade, 0, 80)
```

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `strike_value` | sv | int | 8–13 | Per-strike base (F.1) |
| `streak_multiplier(st)` | sm(st) | int | 100/125/150 | F.2 |
| `miss_penalty` | mp | int | 5 (tuning) | Grade resta on miss |
| `duet_grade` | dg | int | 0–80 (clamped) | Running accumulator |

**Output Range**: `duet_grade ∈ [0, 80]` post-clamp. Typical middle-skill: 50–65. Expert: 70–80. Frustrated/spam: 15–35.

**Rationale**: Late hits no restan para no penalizar doble (no contribution YA es penalty). Miss resta para que un error tenga weight emocional, pero floor 0 previene frustration spiral. Clamp 80 enforcea el split 80/20 de Rule 6/8.

**Ejemplo** (Sword, 8 strikes, sequence: 5 perfect → miss → 2 perfect):

```
strikes 1–3 (perfect, streak 1→3):   3 × 10 = 30
strike 4 (perfect, streak=4, sm=125): floor(10 × 125 / 100) = 12 → dg=42
strike 5 (perfect, streak=5, sm=150): floor(10 × 150 / 100) = 15 → dg=57
strike 6 (MISS):                      dg = max(0, 57 - 5) = 52, streak=0
strike 7 (perfect, streak=1, sm=100): 10 → dg=62
strike 8 (perfect, streak=2, sm=100): 10 → dg=72

duet_grade = clamp(72, 0, 80) = 72
```

---

### Formula D.4 — Seal Bonus (binary stepped)

Aporte del final strike post-Duet.

```
seal_bonus = 20   if seal_input == PERFECT_SEAL
           = 10   if seal_input == HIT_SEAL
           = 0    if seal_input == MISS_SEAL
```

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `seal_input` | si | enum | PERFECT/HIT/MISS | Result del Timing Input final (window 300ms / hit window ±50ms dentro del 300ms) |
| `seal_bonus` | sb | int | 0, 10, 20 | Contribución al final grade |

**Output Range**: `seal_bonus ∈ {0, 10, 20}`. Perfect Seal requiere el tap en el centro ±50ms del window; Hit es dentro del window pero fuera del centro; Miss es fuera del window.

**Rationale**: Binary stepped (no linear 0–20) para que el Seal tenga **identidad** — el player SIENTE si logró el perfect, no deduce un scaled value. 10 es el consolation prize de "participé en el Seal". 0 es solo para bad timing explícito (tap fuera de 300ms).

**Ejemplo**: player clava el Perfect Seal → sb=20. Final grade = duet_grade + sb = 72 + 20 = 92 (pre-clamp).

---

### Formula D.5 — Grade Ceiling (anti-spam counter+time model)

Topa el grade máximo alcanzable según historial reciente de forjas.

```
t             = max(0, now - last_forge_completed_at)        (seconds)
heat_penalty  = max(0, n - HOT_FORGE_FREE_COUNT) × PENALTY_PER_EXTRA_FORGE
time_recovery = clamp((t - DEGRADATION_ONSET) / RECOVERY_DURATION, 0.0, 1.0)
                (only applies when t > DEGRADATION_ONSET; else 0)
grade_ceiling = clamp(
                  100 - heat_penalty + floor(time_recovery × heat_penalty),
                  GRADE_FLOOR,
                  100
                )
```

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `t` | t | int (sec) | 0–∞ | Segundos desde last_forge_completed_at |
| `n` | n | int | 0–∞ | consecutive_hot_forges counter (persistido) |
| `HOT_FORGE_FREE_COUNT` | Hf | int | 3 (default, safe 2–5) | Forjas gratis antes de penalty |
| `PENALTY_PER_EXTRA_FORGE` | Pp | int | 20 (default, safe 10–30) | Grade points por extra hot forge |
| `DEGRADATION_ONSET` | Do | int (sec) | 420 (= GRACE_WINDOW) | Seconds antes de empezar recovery |
| `RECOVERY_DURATION` | Rd | int (sec) | 1800 (safe 900–3600) | Seconds hasta full recovery |
| `GRADE_FLOOR` | Gf | int | 45 (safe 30–60) | Minimum ceiling enforceable |
| `grade_ceiling` | gc | int | 45–100 | Output cap aplicado en D.6 |

**Counter update rules** (ejecutados al completar un forge):

```
if t_since_last < GRACE_WINDOW:       n = n + 1         (forge hot chain continues)
elif t_since_last > RECOVERY_FULL:    n = 0             (full reset)
else:                                   n = n            (cooling but not fully recovered)
```

donde `GRACE_WINDOW = 420s`, `RECOVERY_FULL = DEGRADATION_ONSET + RECOVERY_DURATION = 2220s`.

**Output Range**: `grade_ceiling ∈ [45, 100]`. En sesión normal (3–4 forges espaciados 5+ min): gc=100 always. En grinding (12 forges en 10 min): gc degrada hasta 45 después de forge 7. Primer forge post-idle-de-37-min: gc=100.

**Ejemplo 1 — "Engaged session" (4 forges cada 7 min)**:
- Forge 1: t=∞ (first), n=0, heat_penalty=0, gc=100
- Forge 2: t=420s (=GRACE_WINDOW edge), n incrementa a 1, heat_penalty=0 (n ≤ 3), gc=100
- Forge 3: t=420s, n=2, heat_penalty=0, gc=100
- Forge 4: t=420s, n=3, heat_penalty=0 (n = Hf, not > Hf), gc=100

Todos los 4 forges: full access. Calibración preservada.

**Ejemplo 2 — "Grinder" (12 forges cada 50s)**:
- Forge 1: gc=100 (n=0)
- Forge 4: n=3, heat_penalty=0, gc=100
- Forge 5: n=4, heat_penalty = max(0, 4-3) × 20 = 20, time_recovery=0, gc = clamp(100 - 20, 45, 100) = **80**
- Forge 6: n=5, heat_penalty = 40, gc = clamp(60, 45, 100) = **60** (Epic borderline)
- Forge 7: n=6, heat_penalty = 60, gc = clamp(40, 45, 100) = **45** (floor kicks in)
- Forge 8–12: gc = 45 (floor)

Grinder produce 4 potential-Epics, 1 borderline, 7 stuck en Rare cap. Economic brake funciona.

**Ejemplo 3 — "Returner" (3h break after forge)**:
- Last forge: n was 1 post-forge.
- Next forge: t=10800s. `time_recovery = clamp((10800-420)/1800, 0, 1) = 1.0` (full).
- `n` resets to 0 porque `t > RECOVERY_FULL`.
- gc = clamp(100 - 0 + floor(1.0 × 0), 45, 100) = **100**.

Full access. No return bonus, no penalty — clean slate.

**Rationale**: Counter + time (no time-only) cierra el loophole del "grinder disciplinado" que espaciaría forjas cada 5:01 min. Floor 45 sigue arriba del Rare gate (40) para que cold forge siga produciendo un arma usable — penalización es "Epic bloqueado", no "weapon inútil".

---

### Formula D.6 — Final Grade (Duet + Seal, clamped)

Integra Duet y Seal, aplica ceiling, entrega el valor frozen en WeaponInstance.

`forge_grade = clamp(duet_grade + seal_bonus, 0, min(100, grade_ceiling))`

**Variables**:

| Variable | Symbol | Type | Range | Description |
|---|---|---|---|---|
| `duet_grade` | dg | int | 0–80 | F.3 output |
| `seal_bonus` | sb | int | 0, 10, 20 | F.4 output |
| `grade_ceiling` | gc | int | 45–100 | F.5 output |
| `forge_grade` | g | int | 0–100 | Frozen al forge completion, alimenta rarity_gate + grade_bonus_per_mille |

**Output Range**: `forge_grade ∈ [0, 100]`. Teóricamente 0 al 100; practically floor ~40 (player mediocre) a ~95 (expert on hot anvil). Cold anvil cap: 45.

**Ejemplo 1** (skilled player, hot anvil): dg=72, sb=20 (perfect seal), gc=100 → `g = clamp(92, 0, 100) = 92` (Legendary territory si epic_count≥2).

**Ejemplo 2** (skilled player, cold anvil forge 7): dg=72, sb=20, gc=45 → `g = clamp(92, 0, 45) = 45` (Rare cap). El skill "se pierde" en la frialdad del workshop.

**Ejemplo 3** (crash recovery, Rule 12): `duet_grade = 0`, `seal_bonus = 0`, output = 0. WeaponInstance producido con grade=0 (Rough tier). No materials lost.

## Edge Cases

Todos los casos inusuales organizados por subsistema con resolución explícita. 30 edges total, vetted por systems-designer consult.

### E.1 — Minigame Inputs

- **If player makes no inputs during Duet phase**: todas las strike positions score MISS; `duet_grade = 0`. Seal phase proceeds normally.
- **If player taps a non-interactive screen region during a strike window**: input ignorado; strike window permanece open hasta timeout.
- **If multiple simultaneous touches arrive during a single strike window**: el touch con el earliest timestamp se registra; resto descartado.
- **If player taps before a strike window opens**: input ignorado; no scored como MISS ni LATE.
- **If player taps after a strike window closes during Duet**: scored como LATE per Rule 6; sin grade contribution, sin deduction.
- **If player enters an extra glyph during a sequence strike**: extra tap se evalúa contra la posición esperada actual; si no matchea, esa posición scores MISS; sequence continúa desde la próxima posición esperada. No restart, no cascade failure.
- **If player omits a glyph (position times out) during a sequence strike**: posición timed-out scores MISS per Rule 6; sequence continúa.
- **If `duet_grade` reaches 80 antes de que expire el Duet phase clock**: strike_value contributions subsiguientes son silently discarded; minigame continúa visualmente para no disruptir el Seal phase transition.
- **If player taps during the Seal window**: binary hit registered; `seal_bonus = 10` o `20` per Formula D.4.
- **If player taps after the Seal window closes**: input ignorado; `seal_bonus = 0`. **No LATE grace period para Seal** (Seal es binary, not LATE-tolerant).
- **If Duet phase expires while `streak_count > 0`**: accumulation loop termina inmediatamente; streak discarded sin aplicar multiplier a strike in-progress; `duet_grade` es el value acumulado hasta ese momento. `streak_count` se resetea a 0 al entrar al Seal phase (no transfiere).
- **If app is backgrounded mid-minigame**: minigame pauses; resume on foreground. Rule 12 (crash recovery) NOT triggered.
- **If a phone call interrupts mid-minigame**: same as backgrounded — pause + resume.
- **If touch input reports NaN o negative timestamp**: treated as LATE; logged as warning.
- **If device touch latency exceeds 50ms**: sistema registra inputs as received; grade reflects player's actual timing on that device. Documented as known device-capability limitation, not a system bug.

### E.2 — Precondition Edges

- **If material's `stat_bonuses` field is null (unmigrated save)**: treated as `{attack: 0}`; warning logged. Material consumable pero contribuye zero stat value.
- **If material's `damage_type_affinity` is unset**: material treated as incompatible con todos los archetypes; 50% compatibility penalty always applies (Weapon Rule 6 behavior).
- **If archetype's `forge_primary_damage_type` is unset**: ForgeSystem **skips el compatibility penalty call entirely**; `MaterialSystem.compute_compatibility()` NO invocado; todos los materials apply al 100%. Esto es un ForgeSystem-side bypass, no un MaterialSystem behavior change.
- **If player has exactly MAX_ROSTER_SIZE weapons, some of which are RETIRED**: ForgeSystem cuenta solo WeaponInstances en state `FORGING + FORGED_UNNAMED + NAMED + EQUIPPED + DEFENDING` (Weapon registry `MAX_ROSTER_SIZE` definition). RETIRED/DELETED/ORPHANED excluidos. Forge proceeds si count activo < 12.
- **If player submits 0 materials**: `forge_failed("materials_empty")`; UI refreshes a SELECTING. FSM vuelve a IDLE.
- **If player submits more materials than `archetype.max_materials`**: UI debe gatear; backend guard retorna `forge_failed("too_many_materials")` si alcanzado.

### E.3 — Counter and Cooling Edges

- **If `n = 0` and `last_forge_completed_at = null/0`** (first forge ever): `t` treated as infinity; `grade_ceiling = 100` per Formula D.5.
- **If counter state lost (save corruption)**: reset a `n = 0`, `last_forge_completed_at = 0`; player gets `grade_ceiling = 100`. Player-friendly resolution.
- **If device clock manipulated forward**: MVP accepts client time as truth. Known single-player exploit, low stakes; Tier 2 server-auth closes this. Flagged as Open Question.
- **If device clock manipulated backward** (causing `now - last_forge_completed_at < 0`): Formula D.5 computes `t = max(0, ...)`; `grade_ceiling` computado as if no time has passed. Conservative outcome (no bonus from clock rollback).
- **If `t = DEGRADATION_ONSET` exactly (t = 420s)**: cae en `else` branch del counter update rule; `n` unchanged. Cooling has begun pero recovery not triggered.
- **If `n` reaches 20 or higher**: `heat_penalty` satura; `grade_ceiling` floor = 45 per Formula D.5. No integer overflow risk.

### E.4 — Crash and Recovery

- **If crash occurs after material consumption but BEFORE `forge_session_id` is generated**: materials lost sin recovery record. **Critical data-loss bug**. **Implementation ordering constraint**: `forge_session_id` MUST be generated AND persisted ANTES de `consume_materials()`. Rule 3 + Rule 4 order locked.
- **If crash occurs after session_id persisted pero antes del minigame start**: `pending_forge_session.status = "consumed_not_resolved"`; Rule 12 recovery produce grade=0 weapon on re-launch.
- **If crash occurs mid-minigame**: same as above; Rule 12.
- **If crash occurs during REVEALING after grade computed pero antes de que `WeaponInstance.create_weapon()` retorne**: recovery lee grade stored y llama `AffixSystem.roll_affixes(seed=session_id)`. **HARD DEPENDENCY**: `AffixSystem.roll_affixes` MUST be a pure function of `session_id` — mismo seed SIEMPRE produce mismos affixes. Flagged como requirement al Affix GDD.
- **If crash during naming step (WeaponInstance en FORGED_UNNAMED)**: weapon persiste en FORGED_UNNAMED on re-launch; UI routes player al naming prompt antes de cualquier otra forge action.
- **If recovery attempted y archetype removed from catalog**: weapon enters corrupted state per Weapon Entity E3.1; player prompted con error explícito.
- **If recovery attempted y material ID renamed in migration**: consumption ya ocurrió; ForgeSystem resolves con catalog actual, logs ID mismatch warning, proceeds con grade=0 weapon. Materials cannot be refunded post-consumption.

### E.5 — Integration Failures

- **If `AffixSystem.roll_affixes` throws exception**: fallback a `affixes = []`; `weapon_flags |= FLAG_AFFIX_GENERATION_FAILED`; weapon IS created. Visual/flavor treatment of that flag es downstream concern del UI/narrative system.
- **If `MaterialSystem.consume_materials` reports partial failure**: viola Material atomic contract. ForgeSystem NO intenta rollback. Emite `forge_failed("material_consume_error")` + critical error state requiring full save reload. ForgeSystem no debe intentar arreglar un MaterialSystem roto.
- **If ForgeSystem NOT connected a `materials_consumed` signal antes de llamar `consume_materials`**: implementation error. **Integration ordering requirement**: ForgeSystem MUST establish signal connection ANTES de invocar consume.
- **If ForgeSystem receives `materials_consumed` signal con session_id que no matchea forge_session_id activo**: signal ignorado y logged as warning. No state change.
- **If Audio System unavailable**: minigame proceeds silently; timing inputs funcionales porque color phase es el clock (no audio).
- **If VFX system at particle cap cuando forge triggered**: VFX request dropped gracefully; forge proceeds. Visual degradation only.
- **If persistence layer retorna disk-full / permission-denied BEFORE `consume_materials`**: `forge_failed("persistence_unavailable")`; consume_materials NUNCA called. Data integrity preserved.
- **If `Economy.deduct` falla after precondition check passed (race)**: `forge_failed("insufficient_gold")`; consume_materials NUNCA called.

### E.6 — Concurrency and Mutex

- **If forge initiated while FSM en REVEALING**: `forge_failed("forge_in_progress")`. Global mutex blocks entry.
- **If forge initiated while FSM en COOLING**: ALLOWED. FSM transiciona a SELECTING; `grade_ceiling` computado con current `n` per Formula D.5.
- **If background signal intenta trigger forge start while FSM en FORGING**: signal ignorado. Global mutex (Rule 11).
- **If `forge_session_id` generation produces value ya presente en `pending_forge_sessions`**: regenerate once. Si second collision (prob ~1 en 2^64), `forge_failed("session_id_collision")` + critical error logged. Policy exists for completeness.

### E.7 — Data Migration

- **If save predating Forge System loaded**: Forge state initialized fresh: `n=0`, `last_forge_completed_at=0`, no pending sessions. No weapons retro-created.
- **If archetype catalog changes entre save write y next load**: pending_forge_session resolves usando current catalog at load; mismatch logged como warning.
- **If `pending_forge_session.schema_version` older but known**: fields migrated in place antes de resolution.
- **If `pending_forge_session.schema_version` unknown (newer/corrupted)**: record dropped; gold refunded; **materials NOT refunded** (consumption ya ocurrió). Player notified.

## Dependencies

Mapea las conexiones con direction + nature + hard/soft. Detalles de contrato ya están en §C.3; esta sección focaliza dep relationship.

### F.1 — Upstream Dependencies (systems que Forge CONSUME)

#### F.1.1 — Data-Driven Config (HARD, ✓ Designed — GDD #1)
- **Qué provee**: `WeaponArchetypeDefinition` catalog, `MaterialDefinition` catalog, nuevo `ForgeConfig` resource (tuning knobs).
- **Interfaz**: `ResourceLoader.load("uid://...")` + folder conventions del Data-Driven Config GDD.
- **Nature**: Forge no arranca sin config. HARD.

#### F.1.2 — Material System (HARD, ✓ Designed — GDD #2)
- **Qué provee**: `has_materials(reqs: Dictionary) -> bool` (gate) + `consume_materials(reqs: Dictionary) -> bool` (atomic write). Emit `materials_consumed` signal.
- **Fields consumidos**: `MaterialDefinition.stat_bonuses` + `damage_type_affinity` + `tier` + `element`.
- **Nature**: sin Material, no hay qué forjar. HARD.

#### F.1.3 — Weapon Entity System (HARD bidirectional, ✓ Designed — GDD #3)
- **Qué provee**: `WeaponInstance.create_weapon(archetype, materials, forge_grade, player_signature, weapon_name, seed) -> WeaponInstance` (Rule 1 delegate). State machine target (`FORGING → FORGED_UNNAMED`).
- **Qué consume Forge desde Weapon**: Rule 1 (creation authority), Rule 5 (signature pre-condition), Rule 6 (compatibility), Rule 9 (element dominance), E1.1–E1.7 (rejection reasons).
- **Nature**: Forge ES el creador exclusivo de WeaponInstance. HARD bidirectional.

#### F.1.4 — Affix/Mutation System (HARD, ✗ NOT designed — PROVISIONAL)
- **Provisional interfaz**: `AffixSystem.roll_affixes(seed: int, slot_count: int, slot_tiers: Array[int], rarity: int) -> Array[AffixInstance]`.
- **HARD REQUIREMENT**: `roll_affixes` MUST be pure function of seed — mismo `forge_session_id` SIEMPRE produce mismos affixes (determinismo para Replay + crash recovery E.4).
- **Nature**: forge_grade + rarity solos no completan el arma — Affix es el cierre mecánico. HARD.
- **Flagged como Open Question** hasta que Affix GDD honre el contrato.

#### F.1.5 — Save/Load & Persistence (HARD, ✗ NOT designed — PROVISIONAL)
- **Provisional interfaz**: `SaveSystem.persist_forge_state(state: Dictionary) -> bool` + `SaveSystem.load_forge_state() -> Dictionary`. Store: `last_forge_completed_at`, `consecutive_hot_forges`, `pending_forge_session`.
- **Crash recovery contract**: `pending_forge_session.status` debe persistirse antes del `consume_materials()` call (E.4 ordering constraint).
- **Nature**: counter y cooling state requieren persistencia cross-session. HARD.

#### F.1.6 — Audio System (SOFT, ✗ NOT designed — PROVISIONAL)
- **Provisional interfaz**: event bus con signals `forge_strike_played(power_0_1)`, `forge_heat_changed(heat_0_1)`, `forge_reveal_played(rarity)`, `forge_cooling_entered()`, `forge_anvil_cooled()`.
- **Nature**: minigame funciona silencioso (E.5 — color phase es el clock, no audio). SOFT — afecta feel, no mecánica.

#### F.1.7 — VFX Framework (SOFT, ✗ NOT designed — PROVISIONAL)
- **Provisional interfaz**: `VFXFramework.trigger_flipbook(id, position)` + `emit_particles(emitter_id, count)` con cap 4 × 150.
- **Nature**: minigame funciona sin VFX. SOFT — afecta feel y Pillar 1 impact.

#### F.1.8 — Input System (HARD, ✗ NOT designed — PROVISIONAL)
- **Provisional interfaz**: `InputSystem.get_timestamped_touch() -> Dictionary{position, timestamp_ms}` con jitter ≤ 50ms. Touch primary; mouse fallback en Web.
- **Nature**: sin input system no hay minigame. HARD.

#### F.1.9 — Economy System (SOFT, ✗ NOT designed — PROVISIONAL)
- **Provisional interfaz**: `EconomySystem.deduct(amount: int) -> bool` como precondition gate si `ForgeConfig.gold_cost_per_forge > 0`.
- **MVP default**: `gold_cost = 0` → Economy no es llamado.
- **Nature**: SOFT en MVP (bypass), becomes HARD en Tier 2 si se habilita cost.

---

### F.2 — Downstream Dependents (systems que DEPENDEN de Forge)

#### F.2.1 — Forge Minigame UI (HARD, ✗ NOT designed, System #21 MVP Presentation)
- **Qué necesita**: render del ForgeSystem state (heat phase, grade progress), selection UI (materials + archetype), reveal animation post-REVEALING.
- **Interfaz expuesta**: read-only `ForgeSystem.get_current_state()`, `ForgeSystem.get_heat_phase()`, `ForgeSystem.get_current_grade()`. Write-only vía `player_confirmed_forge()` signal.

#### F.2.2 — Replay System (HARD, ✗ NOT designed, System #17 MVP Feature)
- **Qué necesita**: `forge_session_id` + `minigame_input_log: Array[Dictionary]` embebidos en forge record para reconstrucción deterministica Tier 2.
- **Interfaz expuesta**: Forge escribe al WeaponInstance; Replay lee post-facto.

#### F.2.3 — Recipe Discovery (SOFT, ✗ NOT designed, System #15 Vertical Slice)
- **Qué necesita**: `forge_completed(materials: Array, archetype: StringName, grade: int)` signal para detectar recipes descubiertos.
- **Nature**: SOFT — Recipe Discovery es Tier 2; Forge no lo requiere para funcionar.

#### F.2.4 — Hub HUD (SOFT, ✗ NOT designed, System #20 MVP Presentation)
- **Qué necesita**: `ForgeSystem.is_cooling() -> bool` para badge "Workshop is cooling". Read-only poll.

#### F.2.5 — PvP Async System (INDIRECT, ✗ NOT designed, System #18 MVP Feature)
- **Qué necesita**: WeaponInstance con `forge_session_id` embebido para replay playback Tier 2. PvP no interactúa directamente con Forge — consume WeaponInstance (creator-agnostic).
- **Nature**: INDIRECT (via Weapon Entity).

#### F.2.6 — Combat Simulation (INDIRECT, ✗ NOT designed, System #13 MVP Core)
- **Qué necesita**: WeaponInstance con stats frozen (Forge las congela en REVEALING via D.6). Combat lee `WeaponInstance.get_combat_stats()`, no llama Forge.
- **Nature**: INDIRECT (via Weapon Entity).

#### F.2.7 — Analytics/Telemetry (SOFT, Tier 2)
- **Qué necesita**: `forge_completed` event con metadata (grade, rarity, materials count, archetype, duration ms) para tracking del core loop health.
- **Nature**: SOFT (stub en MVP).

---

### F.3 — Bidirectional Consistency Note

Los siguientes GDDs **deberán listar Forge como upstream dependency** cuando sean autorizados:

- **HARD upstream**: Forge Minigame UI, Replay System, Recipe Discovery.
- **SOFT upstream via signal**: Hub HUD, Analytics.
- **INDIRECT (via Weapon Entity)**: PvP Async, Combat Simulation, Matchmaking, Hero/Wielder.

Adicionalmente, los siguientes GDDs undesigned **deberán honrar los contratos provisionales de Forge cuando se diseñen**:

- **AffixSystem**: `roll_affixes(seed, slot_count, slot_tiers, rarity)` debe ser pure function del seed. Determinismo cross-platform requerido.
- **Save/Load**: persistencia de `last_forge_completed_at`, `consecutive_hot_forges`, `pending_forge_session` con field `schema_version`. Ordering constraint: session_id persist BEFORE consume_materials.
- **Audio**: event bus signals del forge (5 signals: strike, heat, reveal, cooling_entered, anvil_cooled).
- **VFX**: flipbook trigger API + particle emit con 4×150 cap.
- **Input**: `get_timestamped_touch()` con ≤ 50ms jitter guarantee.
- **Economy** (si Tier 2 habilita gold cost): `deduct(amount) -> bool` atómico.

**Signals públicos del Forge** (consumers deben suscribirse, no pollear):

- `forge_started(session_id: String, archetype_uid: String, materials: Dictionary)`
- `forge_completed(weapon: WeaponInstance, grade: int)`
- `forge_failed(reason: String)` — reasons: `materials_empty`, `invalid_signature`, `unknown_material_id`, `insufficient_materials`, `roster_full`, `archetype_missing`, `too_many_materials`, `forge_in_progress`, `persistence_unavailable`, `material_consume_error`, `insufficient_gold`, `session_id_collision`.

## Tuning Knobs

Todos los valores designer-adjustable viven en el `ForgeConfig.tres` resource (Data-Driven Config pattern). 30 tuning knobs organizados por subsistema + 5 constantes explícitamente LOCKED.

### G.1 — Minigame Shape

| Knob | Type | Default | Safe range | Afecta |
|---|---|---|---|---|
| `MIN_MATERIALS_PER_FORGE` | int | 2 | [2, 3] | Piso de material count; bajar a 1 colapsa variabilidad combinatoria |
| `MAX_MATERIALS_PER_FORGE` (archetype-side) | int | 4 | [3, 5] | Techo material count per archetype; subir complica selección UI |
| `BASE_STRIKES_BASE` | int | 8 | [6, 12] | Strikes base para Sword; afecta duración Duet |
| `STRIKE_MODIFIER_DAGGER` | int | +2 | [+1, +3] | Offset para Dagger (precision sostenida) |
| `STRIKE_MODIFIER_HAMMER` | int | -2 | [-3, -1] | Offset para Hammer (impact timing) |
| `TIMING_RATIO_PER_MILLE` | int | 600 | [400, 800] | Fracción de strikes que son Timing (resto Sequence). 600 = 60% Timing. |
| `SEQUENCE_LENGTH` | int | 3 | [2, 5] | Glifos por Sequence input; MVP uniforme entre archetypes (Tier 2 open question) |

**Safe range rationale**: `BASE_STRIKES_BASE` < 6 hace el Duet demasiado corto para sentir arco; > 12 lo hace agotador en mobile. `TIMING_RATIO_PER_MILLE` fuera de [400, 800] colapsa uno de los dos input types.

### G.2 — Timing Windows (ms)

| Knob | Type | Default | Safe range | Afecta |
|---|---|---|---|---|
| `TIMING_WINDOW_BLAZE_MS` | int | 180 | [140, 220] | Ventana de acierto en Blaze phase (0–30% elapsed) |
| `TIMING_WINDOW_EMBER_MS` | int | 150 | [110, 190] | Ventana en Ember phase (30–70%) |
| `TIMING_WINDOW_COOLING_MS` | int | 120 | [80, 160] | Ventana en Cooling phase (70–100%); precisión máxima |
| `SEQUENCE_TIMEOUT_MS` | int | 2000 | [1500, 3000] | Tiempo total para completar la secuencia de glifos |
| `SEAL_WINDOW_MS` | int | 300 | [200, 500] | Ventana del Seal final (más indulgente que Duet) |
| `SEAL_PERFECT_CENTER_MS` | int | 50 | [30, 80] | Radio del centro del Seal window para PERFECT_SEAL |
| `BASE_TEMPO_BPM` | int | 72 | [60, 96] | Tempo en Blaze; Ember multiplica ×1.15; Cooling vuelve al base |
| `EMBER_TEMPO_MULTIPLIER_PER_MILLE` | int | 1150 | [1000, 1300] | Multiplicador del tempo en Ember phase |

**Safe range rationale**: `TIMING_WINDOW_BLAZE_MS` < 140 excluye jugadores con device latency alto; > 220 trivializa el skill. `BASE_TEMPO_BPM` < 60 hace el Duet lánguido; > 96 rompe la metáfora del ritmo humano.

### G.3 — Grade Accumulation

| Knob | Type | Default | Safe range | Afecta |
|---|---|---|---|---|
| `DUET_GRADE_CAP` | int | 80 | (LOCKED — ver G.7) | — |
| `SEAL_PERFECT_BONUS` | int | 20 | [15, 25] | Puntos por Perfect Seal; + aumento = más peso al Seal |
| `SEAL_HIT_BONUS` | int | 10 | [5, 15] | Puntos por Hit Seal (consolation) |
| `MISS_PENALTY` | int | 5 | [3, 10] | Puntos restados por miss en Duet |
| `STREAK_THRESHOLD_LOW` | int | 3 | [2, 4] | Streaks ≥ este number activan ×1.25 |
| `STREAK_THRESHOLD_HIGH` | int | 5 | [4, 7] | Streaks ≥ este number activan ×1.50 |
| `STREAK_MULTIPLIER_LOW` | int | 125 | [110, 140] | Per-mille multiplier en streak medio |
| `STREAK_MULTIPLIER_HIGH` | int | 150 | [130, 170] | Per-mille multiplier en streak alto |

**Safe range rationale**: `MISS_PENALTY` > 10 produce frustration spiral (un miss mata la sesión emocional). `STREAK_MULTIPLIER_HIGH` > 170 hace que un perfect streak colapse el cap — el cap 80 absorbe esto, pero feel suffers. Ver Section D Formula D.3 clamp.

### G.4 — Anti-Spam Cooling

| Knob | Type | Default | Safe range | Afecta |
|---|---|---|---|---|
| `GRACE_WINDOW` | int (sec) | 420 | [180, 600] | Tiempo entre forges sin acumular heat; < 180 rompe sesión 3-forge |
| `HOT_FORGE_FREE_COUNT` | int | 3 | [2, 5] | Forges gratis antes de penalty; core Pillar 3 alignment |
| `PENALTY_PER_EXTRA_FORGE` | int | 20 | [10, 30] | Grade points por extra hot forge |
| `RECOVERY_DURATION` | int (sec) | 1800 | [900, 3600] | Seconds hasta full recovery; 900 punishes returners, 3600 es whale-hostile |
| `GRADE_FLOOR` | int | 45 | [30, 60] | Minimum ceiling; DEBE stay ≥ Rare gate (40) para no lockear Rare |
| `DEGRADATION_ONSET` | int (sec) | 420 | (= GRACE_WINDOW) | Seconds antes de empezar recovery; típicamente alineado con grace |

**Safe range rationale**: `GRADE_FLOOR < 40` romperá el design (Rare gate es 40 — cold forge no produciría Rare). `HOT_FORGE_FREE_COUNT = 2` colapsa el feeling de "sesión de 3 forges focused"; `= 5` diluye anti-P2W.

### G.5 — Economy

| Knob | Type | Default | Safe range | Afecta |
|---|---|---|---|---|
| `gold_cost_per_forge` | int | 0 | [0, 1000] (MVP: 0) | Gold cost por forge; MVP free, Tier 2 tuning |
| `gold_cost_scales_with_rarity` | bool | false | (Tier 2) | Si true, Tier 2 escala cost con tier de materiales |

### G.6 — Archetype-Specific Overrides

| Archetype | strike_modifier | default max_materials | forge_primary_damage_type (de Weapon Entity) |
|---|---|---|---|
| `sword` | 0 | 4 | `slash` |
| `dagger` | +2 | 3 | `pierce` |
| `hammer` | -2 | 4 | `blunt` |

**Nota**: `forge_primary_damage_type` es owned por Weapon Entity archetype definition, no por ForgeConfig. Reference-only aquí.

### G.7 — Knobs Explicitly NOT Adjustable (Locked Constants)

Estos valores son invariantes arquitectónicos — cambiarlos requiere revision del GDD, no tuning pass:

- **`DUET_GRADE_CAP = 80`** y **`SEAL_MAX_BONUS = 20`**: enforcement del 80/20 split por diseño de fantasy (Player Fantasy Section B — Seal es flourish, no parity con Duet). Cambiar = re-escribir Rule 6/8.
- **`FINAL_GRADE_RANGE = [0, 100]`**: input range para Formulas de Weapon Entity (grade_bonus_per_mille = grade × 5, rarity_gate). Cambiar = romper todos los downstream formulas.
- **`FORGE_SESSION_ID_BITS = 64`**: entropy requirement para collision avoidance; registered en entities.yaml. Cambio requiere anti-collision revision.
- **`NUMBER_OF_HEAT_PHASES = 3` (Blaze / Ember / Cooling)**: narrative beats del Player Fantasy. Agregar una 4ta phase reescribe Section B.
- **`NUMBER_OF_INPUT_TYPES = 2` (Timing + Sequence)**: MVP scope. Tier 2 open question para añadir tipos.

### G.8 — Interacciones entre knobs (changing one affects another)

- **`BASE_STRIKES_BASE` ↔ `DUET_GRADE_CAP`**: `strike_value = floor(80 / base_strikes)`. Cambiar `base_strikes` sin tocar cap cambia el weight per strike.
- **`GRADE_FLOOR` ↔ `PENALTY_PER_EXTRA_FORGE`**: si penalty es alto, floor importa más rápido. Si penalty es bajo, floor casi nunca se alcanza.
- **`GRACE_WINDOW` ↔ `HOT_FORGE_FREE_COUNT`**: sesión típica de 25 min con 4 forges implica gap promedio ~7 min. Si `GRACE_WINDOW > 420s` y free_count=3, la sesión normal nunca toca penalty. Si `GRACE_WINDOW < 420s` Y free_count=3, el 4to forge de la sesión siempre penaliza.
- **`TIMING_WINDOW_*_MS` ↔ `BASE_TEMPO_BPM`**: ventanas más chicas con tempo más rápido = skill ceiling alto. Windows grandes con tempo lento = minigame trivial. Calibrar en pares.

## Visual/Audio Requirements

### Visual/Audio.1 — VFX Requirements

- **Forge Opening / Bellows Ignition**: flipbook 12 frames, looping at idle cadence. Emitter slot 1 (of 4): 30 particles, rising ember drift upward. Palette Blaze: `#FF8C1A` (amber core), `#FFE066` (highlight tip), `#2A1A0A` (smoke shadow).
- **Strike Impact — Normal hit**: flipbook 8 frames one-shot. Emitter slot 2: 20 particles horizontal scatter gravity-pulled down. Palette inherits current heat phase (ver §Visual/Audio.3). Frame 1 brightest; decay a grey ash.
- **Strike Impact — Perfect hit (Spark Upward)**: flipbook 10 frames one-shot sobre el normal strike. Reusa emitter slot 2 redirigido hacia arriba (+Y bias 0.8). Frame 1 `#FFFFFF` pure white en contacto; 2–4 `#FFD700` scatter; 5–10 drift y fade. **Sparks travel upward and outward, not downward** — es la diferencia crítica vs normal.
- **Miss — Shadow Ring Dimming**: no particles. Fullscreen shader pulse — screen edges desaturate 30% over 8 frames, recover over 16. Palette cool grey `#3A3A4A` vignette al 40% opacity peak. **No interrumpe el heat phase color.**
- **Heat Phase Transition**: flipbook 6 frames one-shot crossfade en la superficie del metal. Emitter slot 3: 15 particles burst color-shifted al phase incoming. Blaze `#FF8C1A` → Ember `#C0391B` → Cooling `#6B2D2D`.
- **Perfect Seal — 1-Frame White Flash**: single-frame fullscreen white overlay al 100% opacity, held exactly 1 frame (16ms @ 60fps). Frame 2+: radial fade from center to edges over 300ms. Sin particles, sin emitter. Esto es el visual crescendo — nada compite con este frame.
- **Reveal — Rarity Flipbook**: emitter slot 4 dedicated al reveal only. Common: flipbook 8 frames, 80 particles, warm amber burst. Rare: 12 frames, 120 particles, deep violet `#7B3FA0` + gold rim. Legendary: 16 frames, 150 particles (cap), white-gold `#FFE8A0` sustained glow loop al final.
- **Cooling Degradation — Shadow Creep**: shader-driven (delegado a technical-artist). Screen-edge darkening vignette crece proporcional a `degradation_factor` (0.0–1.0). At 0.5: vignette 15% screen width, desaturation +20%. At 1.0: 30% width, desaturation +50%, ember particles throttled a 5. **Comunica "cold forge" sin HUD text.**

**Emitter budget**: slot 1 ambient embers (throttled), slot 2 strike impacts, slot 3 phase transitions, slot 4 reveal only. Peak simultaneous: 30+20+15=65 particles, bien dentro del cap 4×150.

### Visual/Audio.2 — Audio Requirements

- **Hammer Strike — Archetype Variations**: one-shot, 3 power tiers per archetype (light/medium/heavy) × 3 archetypes = **9 source files**. Duration 80–200ms per hit (sword shorter/crisper; hammer longer/lower). Percussive, tactile, metálico — never "game SFX boomy". Pitch shifts +3 semitones per consecutive streak hit (reset on miss); keeps rising warmth diegetic.
- **Heat Phase Crossfade — Ambient Loop**: looping ambient bed con crossfade entre 3 stems (Blaze / Ember / Cooling). Crossfade 1.5s linear al phase boundary. Each stem seamless loop ~8–12s. Blaze = urgent bellows-breathing; Ember = deep resonant; Cooling = hollow receding. **Mitsuda reference**: Ember stem carga melodic fragment que echoes el Seal crescendo motif.
- **Seal Crescendo**: one-shot stinger non-interruptible, ducks ambient a 20% durante playback. Duration 300ms rising swell + 500ms resonant tail = ~800ms. Emocional: solemn resolution (no triumph). **Perfect Seal** agrega un high-pitched bell tone en el white frame moment (diegetic forge "ring"). **Near-miss Seal** (tap dentro del 300ms pero fuera del 50ms center): dampened thud en vez de bell, slight harmonic drop en tail.
- **Reveal — Rarity Escalation**: one-shot 3 variants. Common 400ms (warm satisfaction). Rare 700ms (surprise + warmth). Legendary 1.2s con sustained harmonic tail (reverent awe). Legendary agrega solo instrument swell (string o piano) antes del full reveal hit.
- **Cooling Withdrawal**: ambient modifier — Blaze/Ember loop gains low-pass filter que tightens proporcional a `degradation_factor`. At full degradation: loop sounds muffled, distant, like fire through thick glass. Runtime DSP (sin nuevo audio file).

### Visual/Audio.3 — Animation Requirements

- **Hammer Swing — Kinetic Honesty (3-phase)**: anticipation 4 frames wind-up (arm pulls back, slight squash on handle). Strike 2 frames descent (motion blur smear en frame 1). Overshoot + Settle: 3 frames past contact point, 2 frames bounce back a rest. **Total 11 frames per swing @ 24fps sub-step**. Archetype variants difieren en arc width, no frame count. Anticipation ease-in; overshoot spring curve. **No linear easing.**
- **Metal Color Phase Crossfade (Diegetic Clock)**: shader-driven color ramp. Keyframes: Blaze `#FF8C1A` → Ember `#C0391B` → Cooling `#6B2D2D`. Transition 2.5s per phase boundary, driven por `heat_level` float (0.0–1.0 per phase). **El metal es el único timer que el player lee — color debe ser unambiguous at a glance.**
- **Perfect Hit Spark Trajectory**: sparks originate at strike contact point, travel upward at 60–80° from horizontal. Gravity applies after 8 frames (apex); sparks fall offscreen, never bounce. 3 spark sizes (large/medium/small) ratio 3:5:8 para depth. Large: 6-frame flipbook. Medium: 4-frame. Small: 2-frame flash.
- **Shadow Creep — Cooling Degradation**: screen-edge darkening vignette animated como shader uniform (no sprite overlay). Driven directly por `degradation_factor`; no fixed keyframes — continuous value. Dark warm brown `#1A0F0A` at full opacity on edges, transparent at center. **Should feel like the room getting darker, not like a UI element appearing.**

### Visual/Audio.4 — Asset Spec Priority Queue

Assets que requieren dedicated spec files antes de implementation:

1. `vfx_forge_strike_normal_small.flipbook` — 8-frame strike impact, heat-phase color variants (3 palette sets)
2. `vfx_forge_strike_perfect_small.flipbook` — 10-frame upward spark, white-to-gold decay
3. `vfx_forge_reveal_[rarity]_large.flipbook` — 3 rarity variants (Common / Rare / Legendary)
4. `char_hammer_swing_[archetype]_idle.anim` — 11-frame swing × 3 archetypes; anticipation/overshoot curves documented per arch
5. `shader_forge_metal_colorramp.gdshader` — diegetic heat clock; input `heat_level` float; output surface color Blaze/Ember/Cooling

> 📌 **Asset Spec**: Visual/Audio requirements definidos. Después del art bible re-review, correr `/asset-spec system:forge-system` para per-asset visual descriptions, dimensions, y generation prompts desde esta sección.

## UI Requirements

### UI.1 — Screen Inventory

**Screen: Hub HUD (Forge Entry Point)**
- Trigger: `forge_state == IDLE`
- Data shown: Workshop entry point; `is_cooling` badge si cooldown active
- Actions: Tap Workshop para entrar a SELECTING
- Constraints: badge 4.5:1 contrast; tap target ≥ 44×44 dp; iOS safe area bottom

**Screen: Forge Setup Screen**
- Trigger: `forge_state == SELECTING`
- Data shown: material tray (owned materials, rarity, type); archetype picker (unlocked); stat preview; Signature input field (first forge only, mandatory 1–32 chars)
- Actions: select/deselect materials; choose archetype; enter Signature (first time); tap "Begin the Forge" (disabled hasta valid selection)
- Constraints: material touch targets ≥ 48×48 dp; full-width CTA; safe area top inset

**Screen: Forge Minigame Screen**
- Trigger: `forge_state == FORGING`
- Data shown: anvil, metal object (color = timing signal), strike zone indicators (diegetic only), phase shift color transitions
- Actions: tap/hold strike inputs
- Constraints: **NO HUD overlays**; toda información de timing embebida en world visuals; full bleed portrait

**Screen: Weapon Reveal Screen**
- Trigger: `forge_state == REVEALING`
- Data shown: weapon sprite, generated name field (mandatory, 1–20 chars), stat summary (power, affix slots filled); affix detail panel (on tap)
- Actions: enter weapon name (cannot skip); confirm para commit WeaponInstance; tap affixes para expandir
- Constraints: name field auto-focused on entry; keyboard NO obscurece confirm button; safe area bottom

**Screen: Workshop Cooling State (Hub HUD overlay)**
- Trigger: `n > HOT_FORGE_FREE_COUNT` dentro del cooldown window
- Data shown: "Workshop is cooling" badge en Workshop node; visual cold state en workshop asset
- Actions: none (read-only)
- Constraints: badge legible a 360×640 min resolution

### UI.2 — Anti-Patterns (PROHIBIDO durante FORGING)

- PERFECT / GREAT / GOOD / MISS popup o toast
- Countdown timer o numerical time display
- Combo counter o streak number visible
- Heat meter, temperature bar, grade progress indicator
- Jackpot-style audio trigger on high-grade strikes
- Score display de cualquier tipo durante minigame
- Post-strike floating text (damage numbers, grade labels)

Durante SELECTING y REVEALING:
- Auto-advancing screens (player controla todas las transitions)
- Skippable mandatory inputs (Signature y weapon name son siempre requeridos)

### UI.3 — Information Architecture

| Context | Always Visible | On Tap Only |
|---|---|---|
| SELECTING | material rarity, type, count en tray; archetype name; stat delta preview | full material lore text; affix probability table |
| FORGING | metal color (phase signal); anvil strike zone | Nothing |
| REVEALING | weapon sprite, name field, stat summary (power score, affix count) | full affix breakdown con values y tiers |

### UI.4 — Critical Interaction Moments

**"Begin the Forge" confirmation**: button copy debe signalar irrevocability: **"Begin the Forge"** (no "Start" ni "OK"). Disabled state mientras selección invalid; enabled solo con archetype + ≥1 material selected. **No secondary confirmation dialog** — el button label carga el peso; tapping es el commitment. Sub-label: *"Once you strike, there is no retreat."*

**Weapon naming after Reveal**: name field auto-focused immediately on REVEALING entry. Input 1–20 chars, no empty string. Confirm disabled hasta length ≥ 1. **Cannot dismiss screen, cannot navigate away** — el nombre es el gate al commit del WeaponInstance.

**Signature input (first forge only)**: aparece en SELECTING, sobre "Begin the Forge". Input 1–32 chars, no empty. "Begin the Forge" disabled hasta Signature no-empty. **Shown exactly once per save file**; persisted al PlayerProfile inmediatamente on forge commit.

### UI.5 — UX Design Priority Queue

Orden para `/ux-design` authoring sessions:

1. **Forge Setup Screen** — highest friction; material tray + archetype + Signature + CTA
2. **Weapon Reveal Screen** — mandatory naming gate es novel pattern
3. **Forge Minigame Screen** — diegetic-only constraint es high-risk para misinterpretation; UX spec debe documentar explicitly what is ABSENT
4. **Hub HUD Forge Entry + Cooling Badge** — lower complexity; coordinar con Hub HUD GDD

> **📌 UX Flag — Forge System**: este sistema tiene UI requirements. En Phase 4 (Pre-Production), correr `/ux-design` para UX spec por screen/HUD element. Stories que referencian UI deben citar `design/ux/[screen].md`, no el GDD directamente.

## Acceptance Criteria

Tests verificables independientes — QA tester debe poder tickear pass/fail sin leer el resto del GDD. 28 criterios vetted por qa-lead consult. `[REQUIRES: X]` marca tooling gaps flagged para architecture phase.

### H.1 — Preconditions

- **AC-H.1.1**: GIVEN el roster del player tiene 12 weapons activas (FORGING + FORGED_UNNAMED + NAMED + EQUIPPED + DEFENDING), WHEN el player intenta iniciar forge, THEN `forge_failed` fires con `reason='roster_full'` y ningún material se consume.
- **AC-H.1.2**: GIVEN `player_signature` es string vacío o > 32 chars UTF-8, WHEN el player intenta iniciar forge, THEN `forge_failed` fires con `reason='invalid_signature'` antes de cualquier consumo.
- **AC-H.1.3**: GIVEN un material ID submitted no existe en el catálogo, WHEN el player intenta iniciar forge, THEN `forge_failed` fires con `reason='unknown_material_id'` y ningún material se consume.
- **AC-H.1.4**: GIVEN archetype válido pero material count < `MIN_MATERIALS_PER_FORGE` (2), WHEN player intenta iniciar forge, THEN `forge_failed` fires con `reason='materials_empty'` o `reason='insufficient_materials'` según sub-caso.
- **AC-H.1.5**: GIVEN material count > `archetype.max_materials`, WHEN player intenta iniciar forge, THEN `forge_failed` fires con `reason='too_many_materials'`.
- **AC-H.1.6**: GIVEN las 5 preconditions pasan, WHEN player confirma, THEN FSM transiciona SELECTING → FORGING en orden: generate session_id → persist → consume_materials. Verificable via inspector del save file: session_id está presente antes del decrement de material counts.

### H.2 — Minigame Mechanics

- **AC-H.2.1**: GIVEN Dagger archetype forge activa, WHEN Duet phase inicia, THEN exactamente 10 strike opportunities aparecen en window 15–20s y `strike_value = floor(80 / 10) = 8` per hit.
- **AC-H.2.2**: GIVEN Sword archetype (base_strikes=8), WHEN 5 strikes consecutivos son perfect, THEN streak_multiplier al strike 5 es exactamente 150 (per-mille); grade contribution del strike 5 = `floor(10 × 150 / 100) = 15`.
- **AC-H.2.3**: GIVEN streak = 3 (exactly), WHEN strike 3 resuelve, THEN streak_multiplier = 125 (no 150). Strike 4 consecutivo mantiene 125 (no saltar a 150 hasta streak 5).
- **AC-H.2.4**: GIVEN duet_grade = 50 y player comete MISS, WHEN miss registers, THEN duet_grade = 45 (50 - MISS_PENALTY=5); streak_count reset a 0.
- **AC-H.2.5**: GIVEN Seal phase activa, WHEN player tap cae dentro de 50ms del center del 300ms window, THEN seal_bonus = 20. WHEN tap dentro de 300ms pero fuera del center 50ms, THEN seal_bonus = 10. WHEN tap después de cerrar el 300ms window, THEN seal_bonus = 0 (no LATE grace).

### H.3 — Grade Formulas

- **AC-H.3.1** *(D.1)*: GIVEN Hammer archetype (base_strikes=6), WHEN strike perfect sin streak, THEN duet_grade increment = exactamente `floor(80/6) = 13` (integer, no float).
- **AC-H.3.2** *(D.2)*: GIVEN streak=4, strike_value=10 (Sword), WHEN strike resolve, THEN duet_grade increment = `floor(10 × 125 / 100) = 12`.
- **AC-H.3.3** *(D.3)*: GIVEN duet_grade=78, next perfect strike con contribution=8, WHEN strike aplicado, THEN duet_grade = clamp(86, 0, 80) = **80** (no 86).
- **AC-H.3.4** *(D.4)*: GIVEN seal input = PERFECT (tap en center 50ms), THEN seal_bonus = 20. GIVEN seal input = HIT, THEN seal_bonus = 10. GIVEN seal input = MISS (fuera del 300ms window), THEN seal_bonus = 0. Verificable via signal payload.
- **AC-H.3.5** *(D.5)*: GIVEN n=5, HOT_FORGE_FREE_COUNT=3, PENALTY_PER_EXTRA_FORGE=20, t=0 (elapsed since last forge), THEN grade_ceiling = `clamp(100 - 40 + floor(0 × 40), 45, 100) = 60`. Verificable via `ForgeSystem.get_current_grade_ceiling()` debug API. `[REQUIRES: debug inspector]`
- **AC-H.3.6** *(D.6)*: GIVEN duet_grade=72, seal_bonus=10, grade_ceiling=75, WHEN forge resuelve, THEN `forge_grade = clamp(82, 0, min(100, 75)) = 75`. Verificable: WeaponInstance.`forge_grade` frozen field = 75 post-REVEALING.

### H.4 — Anti-Spam Cooling

- **AC-H.4.1** *(engaged player — all free)*: GIVEN 4 forges espaciados 7+ min (t ≥ 420s entre cada uno), WHEN cada forge completa, THEN n permanece en 0–3, grade_ceiling=100 en los 4 forges. No shadow creep visible.
- **AC-H.4.2** *(grinder — floor kicks in)*: GIVEN 7 forges espaciados 50s entre cada uno (todos dentro de GRACE_WINDOW), WHEN el 7mo forge completa, THEN grade_ceiling = 45 (floor). Shadow creep + color desaturation visible durante el minigame. **No popup, no badge numérica.**
- **AC-H.4.3** *(returner — full recovery)*: GIVEN n=3, player espera t=2400s (> RECOVERY_FULL=2220s), WHEN next forge inicia, THEN n resets a 0, grade_ceiling=100. Heat penalty calculated=0.
- **AC-H.4.4** *(first forge ever)*: GIVEN save fresh o nunca-forjado player, WHEN primera forge inicia, THEN `last_forge_completed_at=0/null`, `n=0`, grade_ceiling=100. Sin bonus, sin penalty.
- **AC-H.4.5** *(clock rollback)*: GIVEN device clock manipulado backward (client now < last_forge_completed_at), WHEN forge next inicia, THEN `t = max(0, now - last) = 0`; grade_ceiling computed as if no time passed (conservative). No crash, no negative t.

### H.5 — State Machine

- **AC-H.5.1**: GIVEN FSM en SELECTING, WHEN player tap cancel, THEN FSM transiciona a IDLE; zero materiales consumidos; zero forge_session_id generados.
- **AC-H.5.2**: GIVEN FSM en FORGING, WHEN player triggers cualquier acción de cancel (back button, tap outside, OS pause resume), THEN FSM permanece en FORGING; ningún cancel signal emitted. Excepción: backgrounded + phone call pausan visualmente pero NO emiten cancel (minigame resume on foreground).
- **AC-H.5.3**: GIVEN FSM en REVEALING, WHEN otro forge_start signal dispara, THEN `forge_failed` fires con `reason='forge_in_progress'`. Global mutex enforce.
- **AC-H.5.4**: GIVEN WeaponInstance en state NAMED/EQUIPPED/etc. (no FORGING), WHEN código externo intenta crear otra forge con el mismo weapon_uid, THEN Weapon Entity Rule 1 rechaza (no ForgeSystem concern — tested at Weapon layer).

### H.6 — Crash Recovery

- **AC-H.6.1**: GIVEN forge session con materiales consumidos y session_id persistido, WHEN app forzada a cerrarse mid-minigame, WHEN game re-launch, THEN WeaponInstance con `forge_grade=0` exists en roster; materials NO están en inventory (no refund); no partial state remains. `[REQUIRES: crash simulation harness]`
- **AC-H.6.2**: GIVEN crash durante REVEALING post grade computed pero antes de WeaponInstance.create, WHEN recovery ejecuta, THEN WeaponInstance resulta con MISMOS affixes que hubieran resultado sin crash (AffixSystem purity test). Verificable: forge_session_id idéntico produce affixes byte-identical. `[REQUIRES: cross-platform determinism harness]`
- **AC-H.6.3**: GIVEN crash durante naming step (WeaponInstance en FORGED_UNNAMED), WHEN re-launch, THEN weapon persiste en FORGED_UNNAMED y UI routes player al naming prompt antes de permitir cualquier otra forge action.

### H.7 — Integration Contracts

- **AC-H.7.1** *(Material atomic)*: GIVEN forge inicia con 3 materiales required, WHEN consume_materials invocado, THEN inspección del inventory en cualquier frame post-call muestra TODOS los 3 materials reduced, never partial. `[REQUIRES: headless sim con frame-step]`
- **AC-H.7.2** *(AffixSystem purity)*: GIVEN mismo `forge_session_id` passed a `AffixSystem.roll_affixes(seed=...)` dos veces en isolation, WHEN ambas calls complete, THEN returned affix arrays son byte-identical. Cross-platform iOS/Android/Web debe producir el mismo array. `[REQUIRES: cross-platform determinism harness]`
- **AC-H.7.3** *(Creation authority)*: GIVEN codebase completo, WHEN `grep -rn "WeaponInstance.new()" src/` ejecutado, THEN zero matches excepto dentro del método `ForgeSystem.create_weapon()` (Rule 1 enforcement). Static analysis test.
- **AC-H.7.4** *(forge_completed payload)*: GIVEN forge resuelve con duet_grade=60, seal_bonus=20, grade_ceiling=100, WHEN `forge_completed` emite, THEN payload contains: `weapon: WeaponInstance`, `grade: int = 80`. Signal spec defined en §F.3.
- **AC-H.7.5** *(Cooling persistence)*: GIVEN 3 forges completed, app relaunched, WHEN next forge starts, THEN `consecutive_hot_forges` y `last_forge_completed_at` retrieved from save matchean los values pre-close. Save/Load contract verified.

### H.8 — Performance

- **AC-H.8.1**: GIVEN Duet minigame activo con VFX + HUD + audio, WHEN frame time sampled por 10 seg consecutivos en mobile low-end target, THEN average frame time < 16.6ms (60 fps); no individual frame exceeds 33.3ms (30 fps floor). `[REQUIRES: on-device profiling]`
- **AC-H.8.2**: GIVEN touch input during Seal phase, WHEN input timestamp compared to registered hit timestamp, THEN jitter delta ≤ 50ms en 20 trials consecutivos en mobile low-end. `[REQUIRES: touch latency measurement]`
- **AC-H.8.3**: GIVEN `grade_resolved` signal emitido, WHEN `ForgeSystem.create_weapon()` ejecuta, THEN `forge_completed` fires dentro de 100ms de `grade_resolved`. `[REQUIRES: signal timestamp instrumentation]`

## Open Questions

10 preguntas abiertas (owners + target resolution):

### OQ.1 — AffixSystem pure-function requirement (HARD DEPENDENCY)
- **Pregunta**: `AffixSystem.roll_affixes(seed, slot_count, slot_tiers, rarity)` debe ser pure function del seed — mismo seed produce mismos affixes cross-platform iOS/Android/Web.
- **Owner**: Affix/Mutation System GDD (System #3, MVP Core, NOT designed).
- **Target resolution**: al authoring del Affix GDD. Forge recoverability (E.4, crash during REVEALING) + Replay determinismo dependen de esto.

### OQ.2 — Device clock manipulation hardening (Tier 2)
- **Pregunta**: MVP acepta client time como truth (single-player exploit, low stakes). Tier 2 requiere server-authoritative timestamps para cooling state.
- **Owner**: Networking Layer GDD + Security Engineer review.
- **Target resolution**: antes de Tier 2 launch público.

### OQ.3 — Mini-mechanics per-archetype (Tier 2)
- **Pregunta**: MVP usa 1 minigame template compartido entre Sword/Dagger/Hammer. Tier 2 evaluar si cada archetype merece distinct mini-mechanic.
- **Owner**: game-designer + creative-director post-playtest.
- **Target resolution**: playtest data del Tier 0 prototype informará la decisión.

### OQ.4 — Gold cost tuning (Tier 2)
- **Pregunta**: MVP default `gold_cost_per_forge = 0`. Tier 2 probablemente necesita cost positivo como segundo economic brake complementario a grade degradation.
- **Owner**: Economy System GDD (System #14, NOT designed).
- **Target resolution**: al authoring de Economy GDD.

### OQ.5 — Sequence length per-archetype (Tier 2)
- **Pregunta**: MVP: SEQUENCE_LENGTH = 3 uniform para todos los archetypes. Tier 2 evaluar escalar con archetype complexity.
- **Owner**: game-designer post-playtest.
- **Target resolution**: playtest data del Tier 0.

### OQ.6 — Cross-platform determinism harness (tooling)
- **Pregunta**: AC-H.7.2 requiere harness para correr AffixSystem.roll_affixes + forge_grade computation en iOS/Android/Web y comparar output byte-identical.
- **Owner**: technical-director + devops-engineer.
- **Target resolution**: antes del primer sprint de Combat/Forge implementation. **Blocker para todo testing deterministico.**

### OQ.7 — Crash simulation harness (tooling)
- **Pregunta**: AC-H.6.1 requiere harness que force-quit la app en specific signal checkpoint para testear Rule 12 recovery. Godot no tiene esto nativo.
- **Owner**: technical-director + qa-lead.
- **Target resolution**: antes del primer sprint de Forge implementation.

### OQ.8 — On-device profiling pipeline (tooling)
- **Pregunta**: AC-H.8.1/2 requieren profiling real de low-end mobile devices (target listado en technical-preferences: 360×640 mínimo). ¿Qué device pool usamos? ¿Firebase Test Lab / BrowserStack / pool interno?
- **Owner**: devops-engineer.
- **Target resolution**: antes del vertical slice (Tier 2).

### OQ.9 — Save/Load schema versioning policy
- **Pregunta**: Forge persiste `pending_forge_session` con `schema_version`. ¿Migrations son in-place o via transform functions? ¿Cómo se handleja unknown future schema_version?
- **Owner**: Save/Load & Persistence GDD (System #18, NOT designed).
- **Target resolution**: al authoring de Save/Load GDD.

### OQ.10 — Sequence input glyph set (visual vocabulary)
- **Pregunta**: MVP sequences usan 3 glyphs direccionales (L/R/Center). ¿Cuál es el vocabulario visual exacto? ¿Los glyphs son iconos pixel o símbolos estilizados?
- **Owner**: art-director + ux-designer.
- **Target resolution**: al authoring de Forge Minigame UI (System #21).
