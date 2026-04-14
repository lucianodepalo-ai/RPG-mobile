# Art Bible — The Meta-Forge

*Created: 2026-04-13*
*Status: Complete (Draft — lean review mode)*
*Visual Identity Anchor: Chrono Revival (HD-2D Pixel)*

> **Art Director Sign-Off (AD-ART-BIBLE)**: SKIPPED [2026-04-13] — lean review mode. All 9 sections authored collaboratively with art-director, ux-designer, and technical-artist subagents. Revisit at phase gate transitions (/gate-check pre-production).

---

## Section 1 — Visual Identity Statement

### The One-Line Visual Rule

> Every asset must read as a frame from a 1999 JRPG cutscene that survived into 2026 with its soul intact but its resolution tripled.

### Supporting Visual Principles

**Principle 1 — Weighted Pigment**
Pixel art uses painterly color logic: warm shadows, cool-shifted highlights, and mid-tones that lean toward a dominant hue per scene. No pure greys, no spec highlights that read as neon.

- *Design test*: When a material's surface treatment is ambiguous, this principle says choose the hue that brings it closer to a Toriyama watercolor sketch, not a Unity PBR preview.
- *Pillar served*: **Estética es el primer impacto** — the palette is the first sensory contract with the player.

**Principle 2 — Legible Silhouette at Thumbnail**
Every sprite, weapon, and UI element must carry its identity at 100px height. Detail exists to reward zoom, not to carry the read. If the silhouette is ambiguous, the shape is wrong before the pixel work begins.

- *Design test*: When a weapon design is ambiguous at small size, this principle says sacrifice internal detail before sacrificing the outer outline that names the archetype (blade, hammer, staff).
- *Pillar served*: **Sesión corta, profundidad infinita** — players parsing builds at a glance in 10-minute sessions cannot afford to squint.

**Principle 3 — Kinetic Honesty**
Animations must express physical consequence: anticipation before the strike, overshoot past the resting point, settle with slight elastic decay. Linear interpolation is banned. A sword that moves like a cursor has no weight and no story.

- *Design test*: When an animation's feel is ambiguous, this principle says add one frame of anticipation and one frame of overshoot before shipping — not after playtesting.
- *Pillar served*: **Variabilidad emergente** — a weapon's unique DNA must be felt in motion, not just read in stats.

---

## Section 2 — Mood & Atmosphere

### 2.1 Hub / Home Base

- **Primary emotion**: Familiar pride — the quiet satisfaction of returning to a place that is yours and improving.
- **Lighting**: Warm amber key light from forge mouth, diffuse cool blue-grey fill from open sky window. Late afternoon, low sun angle. Medium contrast.
- **Atmospheric descriptors**: Worn, inhabited, amber-soaked, craftsman-quiet, lived-in.
- **Energy level**: Contemplative.
- **Carrier element**: The ember glow from the dormant forge bed — always present, never extinguished, pulsing at 0.5 Hz.
- *Pillar*: Sesión corta, profundidad infinita — the hub must reward return visits with a sense of accumulated ownership.

### 2.2 Forge Minigame

- **Primary emotion**: Focused urgency — the split-second clarity of a craftsman at the decisive strike.
- **Lighting**: High contrast, single key source: the incandescent billet. Red-orange at center, rapid cool falloff to near-black edges. No ambient fill visible.
- **Atmospheric descriptors**: Searing, percussive, claustrophobic, heat-warped, hypnotic.
- **Energy level**: Frenetic with rhythmic anchor.
- **Carrier element**: The chromatic distortion halo around the heated metal — expands and contracts with the timing window, acting as both aesthetic element and readability signal.
- *Principles*: Weighted Pigment — dominant hue is pure forge-orange; everything else recedes. Kinetic Honesty — hammer impact has full anticipation + overshoot.

### 2.3 Material Inventory / Stock Screen

- **Primary emotion**: Considered appetite — the pleasure of surveying options before committing.
- **Lighting**: Flat, even, cool-neutral illumination. Stone-shelf presentation light. No dramatic shadows. Slight warm edge light on selected item only.
- **Atmospheric descriptors**: Catalogued, cool-lit, deliberate, mineral, still.
- **Energy level**: Measured.
- **Carrier element**: The material's own inner luminosity — rare ores emit a faint self-glow, common materials are matte and inert. Rarity reads at a glance.
- *Principle*: Legible Silhouette at Thumbnail — each material must be identifiable at icon size by silhouette + glow status alone.

### 2.4 PvP Replay (Weapon vs Weapon)

- **Primary emotion**: Theatrical suspense — the tension of watching an outcome you already sealed but cannot change.
- **Lighting**: Dramatic split-lighting: each weapon carries its own chromatic identity light (warm/cool, or element-coded). Dark arena, particle spray as secondary fill. Cinematic vignette.
- **Atmospheric descriptors**: Grandiose, clashing, arena-lit, dust-heavy, operatic.
- **Energy level**: Ceremonial escalating to frenetic.
- **Carrier element**: The impact flash between weapons — the frame where two lights collide and briefly produce a third color, whiteout edge, then fast falloff to smoke.
- *Pillar*: Estética es el primer impacto — this is the most-watched 60 seconds in the game; every frame must feel like a cutscene.

### 2.5 Victory / Defeat Screens

**Victory**
- Primary emotion: Earned elation with a hint of provocation toward rivals.
- Lighting: Warm gold key from above, high contrast, subjects lit like a trophy presentation.
- Atmospheric descriptors: Triumphant, gilded, saturated, punchy, brief.

**Defeat**
- Primary emotion: Dignified sting — not shame, but the cold clarity of a gap to close.
- Lighting: Cool desaturated fill, single dim blue-white source. Contrast reduced. Same composition as victory but chroma drained.
- Atmospheric descriptors: Muted, steely, contemplative, unfinished, motivating.

- **Energy level (both)**: Ceremonial, then resolved — a beat of silence, then a clear call to action.
- **Carrier element**: The weapon itself, center frame — victorious version saturated and sharpened; defeated version slightly desaturated with a single crack or stress-mark detail added.
- *Pillar*: Meta emergente — defeat must feel like data, not punishment.

### 2.6 Level-up / Unlock Reveal

- **Primary emotion**: Threshold excitement — the precise moment of crossing into new possibility.
- **Lighting**: Internal luminosity burst from the revealed subject. Starts dark (sealed/forged shut), cracks of light precede the reveal, then full warm-white bloom that settles to natural lighting.
- **Atmospheric descriptors**: Revelatory, pressurized, mythic, sharp-edged, momentary.
- **Energy level**: Ceremonial — single concentrated spike, not sustained.
- **Carrier element**: The light-crack seam before the reveal — the hairline fracture where the new thing is about to break through.
- *Distinction from Victory*: Victory is wide-angle and outward-facing (arena, rivals). Unlock is intimate and inward — the light comes from the object itself, not the world around it.

### 2.7 Menus / Settings / Meta-UI

- **Primary emotion**: Transparent utility — the screen should recede; the player should feel in control, not immersed.
- **Lighting**: Non-diegetic. No in-world light source simulated. Dark neutral background (`#1A1A1F` range), UI elements self-illuminated at consistent mid-luminance.
- **Atmospheric descriptors**: Recessive, precise, dark-mode, architectural, unobtrusive.
- **Energy level**: Still.
- **Carrier element**: The single accent color thread — a narrow warm-amber line or glow used sparingly on interactive elements only, echoing the forge without competing with it.
- *Principle*: Legible Silhouette at Thumbnail — interactive vs. decorative elements must be distinguishable without color alone (shape + luminance contrast sufficient).

---

## Section 3 — Shape Language

### 3.1 Core Geometric Vocabulary

The game operates on two competing shape families held in tension:

- **Hero shapes**: acute angles (55–75°), asymmetric outlines, deliberate negative space. These read as intentional, crafted, dangerous.
- **Supporting shapes**: soft trapezoids and stadium curves. These recede, contextualizing without competing.

The tension between them is the visual argument of the game: **sharp things are made inside soft places**.

### 3.2 Weapon Silhouettes — Primary Shape Authority

Weapons are the protagonist. Every weapon silhouette must pass the **Thumbnail Test**: legible archetype at 100px height, no label required.

**Archetype differentiation by silhouette mass distribution**:

| Archetype | Mass Center | Dominant Angle | Negative Space |
|-----------|-------------|----------------|----------------|
| Sword | Distributed along spine | 60–70° blade taper | Narrow, parallel to spine |
| Hammer / Maul | Top-heavy (>60% mass above grip) | 90° flat face + abrupt shoulder | Wide void under head |
| Staff / Catalyst | Center-hollow (grip is the thinnest point) | Radiating from tips | Symmetric negative space, like a tuning fork |
| Bow / Ranged | Bilateral symmetry, convex outward | Arc of 30–40° curvature | Central void is the functional shape |

**Material tier reads at thumbnail**:
- **Common material**: silhouette closed and convex, no protrusions beyond primary form, even-weight strokes.
- **Rare material**: silhouette has deliberate extrusions — secondary growths, asymmetric guards, a protruding crystal vein. One "surprise corner" per rare tier.
- **Legendary material**: negative space becomes active. A void cuts through the blade itself (a hole, a split, a hollow fuller). The weapon reads as incomplete-but-intentional, like it is still becoming.

**Elemental / stat affinity hint via silhouette terminus**:
- Fire: tip terminates in a forked split (2–3 tines).
- Ice: tip is a single geometric spike, no taper — it stops, does not diminish.
- Lightning: edges are micro-serrated (3–5px notches at 100px scale).
- Physical / weight stat: shoulder width exaggerated; balance point sits visually low.

These are silhouette-level signals, readable in grayscale at 100px.

*Connects to*: **Pillar 1** (estética como primer impacto), **Legible Silhouette at Thumbnail**.

### 3.3 Character Silhouettes

Characters are not the protagonist. Their silhouettes must be distinct from weapons without competing for the eye.

**Wielder archetypes** — each defined by a single exaggerated proportion, not a costume:
- **Melee brute**: shoulder-to-hip ratio > 1.6. Stance wide (feet past shoulders). Planted weight. Thick cylindrical limbs, no taper.
- **Agile striker**: negative space between limbs is the feature — visible air gaps at hip and elbow. Narrow stance, weight on one foot. Limbs taper sharply at extremities.
- **Ranged / caster**: silhouette dominated by weapon or catalyst, not the body. Body narrow (hip-to-shoulder near 1.0), yielding visual authority to what is held.

**Enemies (PvE)** read hostile via convex mass pointing toward the player: shoulders rolled forward, cranial mass forward of spine, forearms larger than upper arms. The silhouette leans.

At 100px only the dominant proportion survives — that proportion IS the character's identity.

*Connects to*: Legible Silhouette at Thumbnail, Pillar 1.

### 3.4 Environment Geometry

- **The Forge**: geometric-ceremonial. Stone and iron intersect at 60° (not 45°, not 90°) — an acute-but-stable angle that feels hand-cut rather than machined. Arches are pointed, not round — Romanesque tension without Gothic fragility. Load-bearing elements visibly oversized relative to function, communicating permanence.
- **PvP Arena**: the 60° angle inverts to 120° obtuse — same angle family, opposite emotional reading. Where the forge compresses inward (vault over anvil), the arena opens outward (tiered steps away from center). Same stone, different intent.

This contrast makes the forge feel private and the arena feel exposed, without using different asset styles.

*Connects to*: Pillar 3 (short session, the forge is the player's sanctuary) and Weighted Pigment (heavy geometry earns heavy color).

### 3.5 UI Shape Grammar

UI is **diegetic-adjacent, not diegetic**. It does not pretend to be stone or iron, but it shares the forge's angle family.

- **Dominant UI shape**: chamfered rectangle — rounded corners replaced with 45° cuts at two opposing corners (top-left / bottom-right). One operation away from a rectangle; signals craftsmanship without simulating material.
- **Panel hierarchy**: top-tier containers use the full chamfer. Secondary panels drop to a single chamfer corner. Tertiary labels are plain rectangles — the chamfer grades down as importance decreases.
- **Explicitly forbidden**: circular buttons, hexagonal grids, scroll-unroll animations from gacha conventions. These are the "generic mobile gacha" markers this game refuses.

The chamfer is the game's design signature at UI scale — it is the same acute angle as the weapon tip, abstracted.

*Connects to*: Pillar 1, Visual Identity Statement.

### 3.6 Visual Hierarchy — Hero vs. Supporting Shapes

In any scene with competing elements (forge backdrop + active material + UI chrome + VFX):

1. **Commands the eye**: weapon silhouette — acute angles pull focus.
2. **Second read**: active VFX — kinetic shapes, never static.
3. **Third read**: wielder character — distinctive proportion, stationary.
4. **Recedes**: forge environment — soft trapezoids, low contrast relative to foreground.
5. **Invisible infrastructure**: UI chrome — chamfered containers, never outlined, always lower saturation than game world content.

**The rule**: if a background element uses an acute angle, it must be muted below 30% saturation. Acute angles at full saturation belong exclusively to weapons and interactive elements.

*Connects to*: Pillar 1, Legible Silhouette at Thumbnail, Kinetic Honesty.

---

## Section 4 — Color System

### 4.1 Primary Palette

These seven colors are the game's foundational vocabulary. Every other palette derives from or references them.

| Name | Hex (approx.) | Role | Evokes |
|---|---|---|---|
| **Forge Ember** | `#C4501A` | Heat, danger, fire element anchor | The moment iron turns orange before white — productive danger |
| **Anvil Iron** | `#4A4060` | Neutral UI ground, shadow base | Old metal worn smooth, wisdom in darkness |
| **Dawn Brass** | `#D4962A` | Legendary rarity, victory, heroic moment | Sunlight hitting a freshly polished blade |
| **Cold Quench** | `#2A5F8C` | Water/ice element anchor, rare rarity | The hiss of hot steel plunged into a bucket |
| **Ashen Bone** | `#E8D9B8` | Text, highlights, cooled metal, UI surface | Cooling forge ash — warmth that has settled |
| **Verdant Temper** | `#3D7A4A` | Earth element anchor, common/uncommon rarity | Moss on a stone that has never moved |
| **Void Seam** | `#1A1228` | Deep shadows, corruption element anchor, frames | The gap between the hammer and the dark |

**Palette rule**: No pure greys (`#808080`). Every neutral leans warm (brass-tinted) in light areas and cool-violet (Anvil Iron family) in shadows. This follows the Weighted Pigment principle — mid-tones are always pulled toward the scene's dominant hue.

### 4.2 Semantic Color Vocabulary

The player must learn this grammar in the first 10 minutes through consistent reinforcement.

| Color family | Meaning in this world | Why it earns that meaning |
|---|---|---|
| **Red-orange** (Forge Ember) | Heat, fire element, forge phase escalation, critical warning | The forge is the game's heart — heat = progress AND danger |
| **Gold** (Dawn Brass) | Legendary rarity, victory screen, meta-defining weapon | Blacksmiths measure success in gold; brass is gold you made yourself |
| **Blue** (Cold Quench) | Water/ice element, rare rarity tier, quench/cooldown phase | Quenching makes a blade hard — blue = precision, mastery |
| **Purple** (Void Seam + red shift) | Epic rarity, dark/corruption element, cursed materials | Purple dye was historically inaccessible — the exceptional AND the ominous |
| **Green** (Verdant Temper) | Earth element, common/uncommon rarity, material available | Earth is abundant; green means accessible, foundational, safe |
| **White-warm** (Ashen Bone) | Cooled/finished state, holy/light element, draw outcome | Ash is what remains after mastery — stillness and completion |
| **Deep violet** (Void Seam) | Defeat screen, corruption element, cursed affixes | The void is where legends go to be forgotten |

### 4.3 Elemental Affinity Palettes

| Element | Primary | Secondary | Must NOT borrow from |
|---|---|---|---|
| **Fire** | `#C4501A` Forge Ember | `#F0A030` molten gold-orange | Water; avoid pure red (UI-critical) |
| **Water / Ice** | `#2A5F8C` Cold Quench | `#A8D8F0` frost silver-blue | Wind at small size; avoid desaturated blue |
| **Earth** | `#3D7A4A` Verdant Temper | `#8B6830` rich ochre-brown | Fire; keep secondary warm, never yellow |
| **Wind / Storm** | `#5ABFB0` teal-jade | `#D4EDE8` pale seafoam | Water (distinguish via yellow-green lean); no grey |
| **Light / Holy** | `#F5EAC0` warm ivory | `#FFD97A` solar gold | Earth (ivory must read brighter); avoid cold whites |
| **Dark / Corruption** | `#1A1228` Void Seam | `#7A3070` bruised violet | Epic rarity purple (corruption uses deeper, less saturated violet) |
| **Neutral** | `#4A4060` Anvil Iron | `#9A8878` worn pewter | Must sit below all elemental palettes in saturation |

### 4.4 Rarity Color System

**Gradient philosophy**: Rarity increases saturation AND luminosity together, then at Legendary/Mythic, gold displaces the rarity hue entirely. The frame shape and particle style carry the rarity signal when color fails.

| Tier | Frame tint | Glow color | Particle style | Shape backup |
|---|---|---|---|---|
| Common | `#6A7A60` muted grey-green | None | Dust motes, no glow | Plain square frame |
| Uncommon | `#3D7A4A` Verdant Temper | Faint green shimmer | Slow leaf-like flakes | Square frame + single corner notch |
| Rare | `#2A5F8C` Cold Quench | Blue pulse, 1 Hz | Rising water droplets | Beveled frame, 2 corners |
| Epic | `#7A3070` deep violet | Violet slow burn | Upward ember-sparks | Ornate frame, all 4 corners |
| Legendary | `#D4962A` Dawn Brass | Gold radiant halo | Bright star-sparks, continuous | Filigree frame + animated border |
| Mythic | `#E8D9B8` → `#D4962A` shifting | White-gold pulse, rapid | Full weapon silhouette glows | Animated filigree + floating runes |

**Element + Rarity simultaneity rule**: Element color lives in the weapon's glow and particle system. Rarity color lives in the frame and border. A Legendary Fire weapon has a gold filigree frame (legendary) with orange-ember particles bleeding inward from the blade (fire). The two signals occupy different spatial zones and never merge into a single ambiguous color.

### 4.5 Per-Scene Color Temperature

| Scene | Dominant temperature | Shadow base | Highlight push | Rationale |
|---|---|---|---|---|
| **Forge minigame** | Warm (2700–3200K) | Violet-brown (Void Seam lean) | Forge Ember orange | Heat communicates progress |
| **Hub / town** | Neutral-warm (4000K) | Anvil Iron blue-grey | Dawn Brass soft gold | Resting state; metal shop at golden hour |
| **PvP replay** | Shifts with outcome: warm = win, cool = loss | Warm: dark brown / Cool: deep navy | Warm: gold / Cool: cold blue | Temperature delivers result before UI text registers |
| **Menu / meta** | Cool-neutral (5000K, moonlit forge) | Void Seam deep violet | Ashen Bone ivory | Players browse at night on mobile; cool meta separates browsing from playing |

### 4.6 UI Palette

The UI palette is a restrained subset of the world palette, not an independent system.

**The divergence**: World art uses the full Weighted Pigment range with soft edges and painterly gradients. UI elements use hard-edged versions of the same hues with a luminosity boost of +15–20 to guarantee readability at 375 px wide in direct sunlight.

| UI element | Color | Sunlight readability |
|---|---|---|
| Primary button | Dawn Brass `#D4962A` on Void Seam `#1A1228` | 7.2:1 contrast ratio |
| Body text | Ashen Bone `#E8D9B8` on Void Seam `#1A1228` | 9.4:1 contrast ratio |
| Warning / heat indicator | Forge Ember `#C4501A` + white icon overlay | Icon carries signal; color reinforces |
| Confirmation / positive | Verdant Temper `#3D7A4A` with Ashen Bone label | Never rely on green alone — paired with checkmark icon |
| Destructive / negative | Forge Ember family, desaturated to `#A04020` | Paired with X icon; not pure red |

**No pure black backgrounds**. All UI backgrounds use Void Seam (`#1A1228`) or warmer dark derivatives. Pure black breaks the painterly soul of the identity rule.

### 4.7 Colorblind Safety

**At-risk semantic pairs**:

| Pair | Risk | Population affected |
|---|---|---|
| Forge Ember (red-orange) vs Verdant Temper (green) | Fire vs Earth element confusion | Deuteranopia (~8% male) |
| Cold Quench (blue) vs Void Seam purple | Rare vs Epic rarity confusion | Tritanopia (~0.5%) |
| Victory gold vs defeat cool-blue | Outcome confusion if screen glanced | Deuteranopia, protanopia |

**Backup cue matrix**:

- **Rarity without color**: Frame shape is the primary non-color signal (4.4). Particle density + motion as secondary. Animation speed scales with rarity.
- **Elements without color**: Each element has a distinct particle shape — Fire = upward-rising sharp sparks, Water = rounded droplets trailing down, Earth = slow tumbling hexagonal shards, Wind = horizontal streaks, Light = radial burst lines, Dark = inward-collapsing wisps. Element icon always displayed alongside element-colored VFX.
- **Forge phase without color**: Cold/dormant = static; Heated = slow pulse; Critical/molten = rapid shake + emission; Cooled/finished = full-stop + completion chime icon.

Phase and rarity are never communicated by color alone.

---

## Section 5 — Character Design Direction

### 5.1 Player Identity: The Blacksmith

The blacksmith does not appear as a playable avatar. No portrait, no character sheet, no face.

**Rationale**: The player's identity is expressed entirely through the weapon roster. The forge UI, the weapon collection screen, and the tournament results are the "face" of the player. Introducing a blacksmith avatar creates a second protagonist competing with the weapon for narrative ownership — a direct violation of the core fantasy. The player is the god behind the glass; the weapons are the myth.

One exception: a small, silhouetted **master's mark** stamp (hammer-and-anvil icon, unsigned) may appear in tournament screens to represent the player's lineage. It is a seal, not a character.

*Pillar alignment*: Estética primer impacto — the weapon is the first read; the player's ego is projected onto it, not onto a face.

### 5.2 Wielder Archetypes

- **Launch (MVP): 2 archetypes** — Melee Brute + Agile Striker. Covers dominant weapon categories and provides clear visual contrast in tournament previews.
- **Tier 2: add Ranged/Caster** — three archetypes is the maximum vocabulary before silhouette differentiation breaks down at 100px.

#### Subordination Rule (applies to all wielders)

Wielders are rendered in the Anvil Iron / Ashen Bone family, saturation capped at 35%. No wielder may carry a saturated accessory. Hair, clothing trim, and skin all pull from desaturated warm-to-neutral ranges. **The weapon slot is the only permitted high-saturation region on the combined silhouette.**

Clothing philosophy: wrapped cloth, undecorated armor plates, muted leather — utilitarian shapes. No insignia, no emblems, no patterned fabric. If a silhouette element draws the eye, it is wrong.

#### Melee Brute
- **Proportion anchor**: Shoulder-to-hip ratio > 1.6, thick cylindrical limbs, wide stance.
- **Head shape**: Low cranial dome, wide jaw, neck as thick as head. Reads as a boulder with legs at 100px.
- **Clothing**: Short-sleeved or bare upper arms — limb mass must not be obscured. Layered cloth skirt or greaves below hip.
- **Hair**: Absent, cropped, or helmet. Never flowing.
- **Stance**: Weight on both feet, slight forward lean. At rest, looks like it is already mid-swing.

#### Agile Striker
- **Proportion anchor**: Air gaps at hip/elbow, narrow stance, sharply tapered limbs.
- **Head shape**: Elongated oval, pointed chin, eyes set high. Reads as a blade itself.
- **Clothing**: Form-fitting wraps with open panels at hip and elbow — air gaps are part of the silhouette. No billowing cloth.
- **Hair**: Short, directional spike or pulled tight. One direction of motion, not ambient.
- **Stance**: Weight shifted to one foot, spine angled. At rest, looks like it is caught mid-step.

### 5.3 Enemy Design

#### PvE Enemies (Farmers)
Enemies telegraph weapon-type weakness through **the geometry of their defense**, not through iconography.

- **Blunt-weak enemies**: Convex plate coverage on the front torso — clearly hard, clearly breakable. Limb mass in chest, not limbs.
- **Slash-weak enemies**: Exposed, elongated limbs with no plate. Silhouette is all limb, minimal trunk.
- **Pierce-weak enemies**: Dense cloth or layered fabric volume — soft, no hard surface to resist a point. Wide compressible silhouette.

A player reading enemy silhouette at 100px can infer the correct weapon family without a UI tooltip. **Shape is the tutorial.**

#### PvP Defenders
PvP defenders are opponent wielders. They use the same archetype vocabulary as the player's wielders — same two launch archetypes, same subordination rules. A distinct defender vocabulary would split the player's attention between "whose weapon is that" and "what is that character."

### 5.4 NPCs and Merchants

Two static world NPCs at launch:
- **Material Merchant**: Rendered at same detail tier as wielders. No name, no portrait closeup. Identified by a single shape prop (overloaded pack, scale weight). Functional, not narrative.
- **Forge Mentor (Tutorial only)**: One appearance as a silhouette behind a bright forge light — never fully revealed. After tutorial, absent. The mentor's obscured form reinforces that the blacksmith identity belongs to the player.

No NPC receives more visual complexity than a wielder.

### 5.5 Expression and Pose Style

**Stiff-and-iconic**. Poses are held frames, not animated arcs. Each archetype has three canonical poses: idle, wind-up, impact.

- **Idle animation**: chest breathing cycle only (2–3 frame loop). No cloth secondary motion, no hair sway, no ambient fidget.
- **Motion budget is reserved for the weapon** — blade shimmer, rune glow, particle trail. If the character moves, the weapon moves more.
- **Portrait screens (closeup, 400px+)**: Full facial detail permitted. At 200px, eyes simplified to high-contrast shapes. At 100px, face is the head silhouette — no internal detail.

### 5.6 LOD Philosophy at Mobile Scale

| Scale | Surviving Detail |
|---|---|
| **100px** (battle grid, tournament list) | Dominant proportion + weapon silhouette only. One-color read. |
| **200px** (roster screen, team select) | Archetype shape + clothing mass. Eyes as high-contrast shapes. Weapon material family visible. |
| **400px** (portrait screen, result card) | Full facial detail, clothing layer separation, weapon surface detail, expression. |

**Design rule**: Every wielder must be approvable at 100px before it is detailed at 400px. If the archetype does not read as a silhouette first, the design fails regardless of how detailed the final art is.

Face at thumbnail: two high-contrast dots for eyes, nothing else. The cranial silhouette carries archetype identity. Detail is added upward through LOD tiers, never assumed downward.

*Visual identity alignment*: The LOD tiers are the survival — the soul is the 100px silhouette; the resolution is what the 400px portrait adds on top.

---

## Section 6 — Environment Design Language

### 6.1 Architectural Tradition: The Volcanic Romanesque

This world draws from **late Byzantine foundry culture blended with early Romanesque ecclesiastical stonework**, filtered through the visual grammar of SNES-era JRPGs. Think Ravenna basilicas repurposed as weapon sanctuaries — weight-bearing stone piers, round arches elongated into points (the 60° acute already approved for the Forge), and decorative ironwork that is simultaneously ornamental and structural.

**Why this tradition**: Byzantine foundries treated metalwork as sacred manufacture. Their architecture reflects that tension — monumental and ceremonial, but built around a working fire. That duality mirrors the game's core fantasy exactly: the weapon is both tool and protagonist, and its birthplace must feel like a temple as much as a workshop. The Arena's obtuse arches echo the same stone vocabulary but read as amphitheater rather than chapel — same world, different purpose.

All environments share one construction logic: **stone shaped around fire, over centuries.** Soot patina, iron fixtures, and load-bearing walls that lean slightly inward or outward depending on whether the space compresses (Forge) or opens (Arena).

### 6.2 Per-Environment Design Briefs

#### The Forge Interior (hub + minigame)
- **Primary visual function**: "I am working. This place has been worked in for a long time."
- **Key elements**: central anvil altar on a raised stone dais; iron-banded pillars with embedded tong hooks; vaulted ceiling with a single oculus venting smoke; ember-glow brazier rings; chains hanging from above — not decorative, load-bearing.
- **Texture philosophy**: hand-painted stone with visible brushstroke direction; iron surfaces use cross-hatch hatching as in ink illustration.
- **Prop density**: dense near the anvil, sparse toward the edges — eyes funnel to center.
- **Storytelling detail**: worn groove in the stone floor exactly in front of the anvil, from centuries of the same stance.

#### The Forge Exterior (establishing / loading)
- **Primary visual function**: "This place has weight and history. I am arriving somewhere that matters."
- **Key elements**: massive pointed arch doorway (the only exterior acute angle); iron-banded double doors, slightly ajar; soot-darkened stone facade; a forge-fire glow bleeding through the door gap as the only light source at dusk.
- **Texture philosophy**: wide pixel-painted stone with minimal detail — silhouette does the work.
- **Prop density**: sparse; one or two ambient props (a water barrel, discarded slag).
- **Storytelling detail**: a weapons testing rack beside the door, several weapons missing from their slots.

#### Material Inventory (storage alcove within the Forge)
- **Primary visual function**: "My raw materials live here. This is organized, not chaotic."
- **Key elements**: stone shelf-niches carved directly into the wall (recessed, arched); labeled crates with iron number-stamps; hanging bundles of material tied with rough cord; a small brazier for light; low ceiling — deliberately intimate.
- **Texture philosophy**: more wood and rope than anywhere else; introduces organic textures as counterpoint to iron/stone.
- **Prop density**: deliberately dense — abundance communicates resource richness.
- **Storytelling detail**: one shelf niche is empty with a fresh chisel mark beside it — something was recently taken.

#### PvP Arena (weapon replay stage)
- **Primary visual function**: "Something is about to be decided. I am watching."
- **Key elements**: tiered stone steps ascending from the floor; 120° archways open outward; iron torch brackets at each tier corner; a raised central combat platform of different (darker) stone; spectator ghost-silhouettes carved into upper walls.
- **Texture philosophy**: same stone as the Forge but with deliberate weathering on upper tiers, polished on the combat platform — the action zone is always cleanest.
- **Prop density**: sparse on the platform (weapon is protagonist), denser in spectator tiers.
- **Storytelling detail**: score marks cut into the platform stone — tallied wins from prior combatants, too many to count.

#### Meta-screens / Menu Backgrounds
- **Primary visual function**: "I am in the world but not yet doing something. This is between moments."
- **Key elements**: out-of-focus Forge Interior or Exterior used as background plate; depth-of-field blur applied at pixel level (1–2 pixel dither, not gaussian); ambient particle — slow ember drift only.
- **Texture philosophy**: desaturated 15%, all detail subordinated to foreground UI.
- **Prop density**: none added — reuses existing environment renders.
- **Storytelling detail**: background can show the empty anvil, reinforcing that the weapon is elsewhere (with the player).

#### World Map / Liga Rankings Screen
- **Primary visual function**: "I understand where I stand in a competitive structure."
- **Key elements**: an overhead parchment-style map with inked regional boundaries; Arena locations marked with the obtuse-arch silhouette icon; rank tiers visualized as elevation contour lines (higher rank = higher ground); a wax-seal motif per tier.
- **Texture philosophy**: deliberate contrast from all other environments — parchment warm cream against all the stone; this is a document, not a place.
- **Prop density**: clean; only icons and typography compete for space.
- **Storytelling detail**: the player's current Arena position is marked with a small embedded sword icon — the weapon has a location in the world.

### 6.3 Parallax Layer Strategy

**Budget: 3 layers maximum per scene on mobile.** At 200 draw call ceiling, each parallax layer costs 1 draw call per unique sprite batch. Reserve headroom for UI and VFX.

| Layer | Contents | Scroll Speed |
|---|---|---|
| Background (Layer 0) | Sky/ceiling/architectural void, zero or near-zero scroll | 0–0.1x |
| Midground (Layer 1) | Primary architectural elements, props, environment identity | 0.5x (reference) |
| Foreground (Layer 2) | Atmospheric overlay: ember particles, smoke wisps, chain silhouettes | 1.2x |

**Prohibited parallax behaviors**:
- No 4th layer on mobile — ever. Web HTML5 may use a 4th sky layer only.
- No two layers at the same scroll speed — creates false flatness.
- Foreground layer must never contain readable story detail — players will not focus it.
- No parallax on the weapon itself; the weapon renders outside the parallax stack at a fixed position.

### 6.4 Time of Day — Per Environment (Locked)

Each scene is locked to a specific time. Time does not pass in-game.

| Environment | Time | Emotional Function |
|---|---|---|
| Forge Interior | Perpetual night / deep amber artificial light | The outside world doesn't intrude. Work happens outside of time. |
| Forge Exterior | Dusk — sun just below horizon, orange-violet sky | Arrival and departure. A threshold moment. |
| Material Inventory | Midday — cool natural light from a high window | Inventory decisions are clear-headed. No atmosphere manipulates judgment. |
| PvP Arena | Overcast noon — flat dramatic light, no shadow direction | Neither player is favored by light. The weapons decide. Win/loss palette shifts override this. |
| Menu Backgrounds | Dusk (matches Exterior) | Continuity — menus feel like the space between arriving and entering. |
| Liga / World Map | No time — parchment is timeless | Ranking is historical record, not present moment. |

### 6.5 Seasonal / Event Variation (Live-Service Tier 3)

**Fixed identity signals — never change**:
- Anvil placement and dais silhouette
- Arch typology (acute Forge / obtuse Arena)
- Soot patina on stone
- The worn floor groove at the anvil

**Shiftable mood dressing — event-safe**:
- Brazier flame color (e.g., green for a poison-metal event, blue for ice-forge season)
- Hanging decoration replacing chains (festival banners, seasonal flora on iron hooks)
- Ember particle color and density
- Sky color through the oculus (Exterior dusk palette can shift hue ±30° for events)
- Snow or ash accumulation on the Exterior approach

**Rule**: Any event dressing must remain below 40% saturation unless it is on a weapon asset. Seasonal decoration must never outread the weapons.

### 6.6 Environmental Prohibitions

The following are explicitly forbidden across all environments to protect the "weapons are protagonist" principle:

1. **No environment detail at full saturation.** All environment colors capped at 60% saturation. The weapon in scene is always the most saturated element.
2. **No animated elements more visually complex than the equipped weapon.** Particle density scales down when a visually complex weapon is displayed.
3. **No acute angles above 30% saturation in environments.** Sharp angles at high saturation belong to weapons only.
4. **No readable text or iconography on environment surfaces** unless intentionally illegible. Environment storytelling is visual, never literal.
5. **No pure grey neutrals** in any environment surface. Every stone, shadow, and metal reads with a temperature lean.
6. **No symmetric foreground framing.** Doorways, arches, foreground props must frame the weapon slightly off-axis. Symmetry flattens the weapon into the environment.

---

## Section 7 — UI/HUD Visual Direction

*Authored jointly by art-director and ux-designer. Conflict resolutions: (A) Sans-serif primary with pixel serif for display only, (B) Forge timing indicator is diegetic-only with player responsibility to learn rhythm, (C) Split animation scope — immediate state change on interactive, Kinetic Honesty on decorative.*

### 7.1 Diegetic Scale by Surface

| Surface | Position | Rationale |
|---|---|---|
| **Forge minigame HUD** | Diegetic-adjacent | Timing and heat are properties of the forge itself; indicators live in world-space glow, not overlay bars |
| **Material inventory** | Screen-space | A craftsman's ledger — clinical, deliberate, paged |
| **Weapon roster** | Screen-space | Trophy wall logic — presented, not embedded |
| **PvP replay viewer** | Screen-space with diegetic accents | Combat numbers float in world-space; health bars screen-space overlays |
| **Victory/Defeat result card** | Screen-space | A forged seal — fully graphic, ceremonial |
| **Main hub HUD** | Screen-space | Persistent instrumentation; instantly legible on re-open |
| **Meta menus** | Screen-space | Pure interface, no pretense of world presence |

### 7.2 Typography Direction

**Primary typeface: geometric sans-serif**. The pixel-stylized serif lives as a *display face* only.

UX rationale: pixel serifs collapse at sub-16sp on high-DPI mobile under direct sunlight. Body text and stat values must survive the worst reading conditions. Visual identity rationale: the 1999 JRPG soul is carried by the serif in moments of drama (weapon names, screen titles, flavor lines); for everyday instrumentation, clarity serves the forge more than nostalgia.

**Weight and size hierarchy** (375px portrait reference):

| Role | Size | Weight | Face |
|---|---|---|---|
| Weapon / material flavor names | 20–28px | Bold | Pixel stylized serif (display only) |
| Screen headers | 22px | Bold | Pixel stylized serif |
| Section labels | 16px | Semibold | Geometric sans |
| Body / stat values | 14px | Regular | Geometric sans |
| Micro-text (timers, counts) | 11px | Regular | Geometric sans |

**Dynamic Type / font scale**: UI must reflow and not clip at 200% system font scale (iOS Dynamic Type, Android font scale).

### 7.3 Iconography Style

**Style**: outlined with filled interior, 2px stroke weight. Reads as hand-etched diagrams from a craftsman's manual — neither sterile-flat nor expensive-illustrated.

**Size tiers**:

| Tier | Size | Stroke | Usage |
|---|---|---|---|
| Action icons | 48px | 2px | Forge action slots, primary CTA row |
| Inline icons | 32px | 2px | Inventory rows, weapon roster cards |
| List bullets | 24px | 1.5px | Stat lines, element tags, rarity dots |

**Silhouette-first differentiation at 24px** — shape is primary, color is confirmatory:
- **Weapon-type**: tool silhouettes (sword, axe, staff, bow — asymmetric shapes)
- **Element**: natural forms (flame teardrop, lightning bolt, frost shard — high contrast outlines)
- **Rarity**: geometry tier (single dot → double bar → chamfered star → crown) — no color dependency

**Iconography carries more weight than color** in this game. Color is confirmatory, not primary.

### 7.4 UI Animation Feel (Split Response)

**Split rule**: Interactive state changes fire at 0ms (immediate visual acknowledgment, ≤100ms perceived response). Decorative motion uses full Kinetic Honesty. No exceptions.

| Element | Behavior | Duration | Category |
|---|---|---|---|
| Button press acknowledgment | Immediate color/scale change at 0ms, then 80ms spring settle | 80ms total | Interactive (immediate + settle) |
| Modal enter | Slide up from 8px below final position, settle with 4px overshoot, snap | 180ms | Decorative |
| Modal exit | Reverse slide, no overshoot | 120ms | Decorative |
| Notification badge | Single pulse (scale 1.0 → 1.15 → 1.0) on arrival, then static | 200ms | Decorative (no loop) |
| Panel transitions (same-tier) | Cut with 1-frame flash (white, 10% opacity) | 1 frame | Interactive |
| Victory reveal | Full Kinetic Honesty — anticipation + overshoot + settle | 300–400ms | Decorative |

**Reduced-motion alternative (OS accessibility signal)**: All Kinetic Honesty animations must have a linear-cut fallback that preserves state change clarity without easing curves.

### 7.5 Forge Minigame HUD Specifics

**Timing indicator: diegetic-only**. The strike window is communicated by the luminosity pulse of the heated metal — the glow brightens to peak and falls. Player strikes at peak glow. **No screen-space progress bar.**

*Trade-off accepted*: Players learn the rhythm by practicing. First-time onboarding uses an extended tutorial forge (slow peak, generous window) that gradually speeds up. Visual identity over accessibility overlay in this surface.

**Material slots**: Chamfered rectangle containers (full chamfer, top-tier hierarchy). Occupied slots show material icon at 48px with 2px inset border. Empty slots show outline only — no fill, no placeholder.

**Heat gauge**: Vertical bar on left edge of forge surface, 8px wide, styled as a thermometer engraving in the forge frame (part of environment illustration, not UI overlay). Color transitions: Ashen Bone (cold) → Ember Glow (working) → Dawn Brass (optimal) → desaturated red (overheat danger — the one permitted warm-red use).

**Successful input**: Metal surface flashes to peak luminosity for 3 frames, then settles. No particle burst, no screen shake. Quality communicated by light, not spectacle.

**Failed input**: Glow drops immediately (1 frame cut to cold state). Single low-register audio cue. No red flash overlay — the darkness is the feedback.

**Interruption recovery**: Minigame pauses on app-switch, phone call, network drop. State serializes to disk — cold resume must be identical to warm resume.

### 7.6 Chamfered Rectangle — Visual vs Hit Area

**The visual chamfer does not define the touch target.** Every interactive element has a rectangular hit area padded to minimum **44×44pt (iOS) / 48×48dp (Android)**, regardless of the visible chamfer's shape. The chamfer is aesthetic; the hit area extends invisibly beyond the visible boundary.

Button press animation: 2px depress on the chamfer axis (top-left corner compresses inward), spring release. This visual effect does NOT reduce the hit area.

### 7.7 Layout and Reach

- **Thumb reach zones**: Primary actions (forge trigger, weapon select, replay controls) must sit in the bottom 55% of the 812px screen. No critical tappable element above the 500px mark without an alternative reach path.
- **Left-handed mode**: Forge interaction zone is configurable (left / right / center) via settings. Default center; mirror-mode toggle available.
- **Safe areas**: Respect iOS home indicator (34pt bottom) and notch (44pt top); Android display cutout padding.
- **Destructive action confirmations**: Selling a weapon or dismantling a material triggers a two-step confirmation via dismissible toast with 5-second undo window (not a modal — modals break flow).

### 7.8 Anti-Gacha Visual Commitments

Visual identity commitments signaling what this game is NOT:

- **No lootbox geometry** — no chest-crack reveals, no spinning reels, no burst-of-light curtain drops. New weapons are revealed through the forge finishing sequence — metal cools, shape clarifies, name stamps in.
- **No golden shimmer loops** — rarity is communicated by icon tier and border weight, not by animated shimmer. A legendary weapon has a heavier chamfer border. It does not glow continuously.
- **No "pull" animation frames** — no pull mechanic exists. Random rewards resolve in one beat with the arrival pulse. No stretched anticipation.
- **No percentage tickers on acquisition screens** — drop-rate displays are static text in micro-type, never animated counters designed to trigger loss aversion.

### 7.9 Anti-Gacha UX Structural Commitments

- **No countdown timers on cosmetics** — if an offer is time-limited, state the date plainly in static text.
- **No fabricated social proof** — "47 players forged this today" is banned.
- **No flashing "LIMITED" banners** — static text only.
- **No interstitial offer screens** on session start or after victory.
- **Progress visibility over urgency** — show players how far they are from the next tier, not how little time remains.
- **Transparent drop rates** — displayed as plain text in material inventory; no hidden probability.
- **Session-complete summary** — calm summary of what was forged and earned. No upsell surface.
- **Persistent shop** — exists when player navigates there; does not navigate to the player.

### 7.10 Accessibility Hard Requirements

| Requirement | Specification |
|---|---|
| Contrast — body text | 4.5:1 minimum; 7:1 preferred given sunlight use case |
| Contrast — UI labels on colored element backgrounds | 4.5:1 against element color, not world palette |
| Touch targets | 44×44pt iOS / 48×48dp Android — hit area, not visual size |
| Color independence | Every semantic signal backed by icon/shape/text — never color alone |
| Dynamic Type / font scale | UI reflows at 200% scale without clipping |
| VoiceOver / TalkBack | Every interactive element has accessibility label; weapon stats readable as structured text |
| Reduced motion | Linear-cut alternative to all Kinetic Honesty animations |
| Safe areas | iOS home indicator (34pt) + notch (44pt); Android display cutouts |

---

## Section 8 — Asset Standards

*Co-authored by art-director (artistic ideal) and technical-artist (engine floor). Where the two conflicted, engine physics won. Artistic compromises are marked explicitly.*

### 8.0 Engine Context

- **Engine**: Godot 4.6 (post-LLM-cutoff; verify APIs against `docs/engine-reference/godot/`).
- **Renderer**: **Compatibility** (OpenGL 3.3 / WebGL 2) — required for predictable 2D batching and broad mobile low-end GPU coverage. **NOT Forward+, NOT the 3D Mobile renderer.**
- **Shader Baker**: mandatory in build pipeline (introduced 4.5). Without it, shaders compile on first encounter → frame hitches on low-end devices.

### 8.1 Sprite Resolution Tiers

| Category | Source Resolution | Display Size | Scale Factor | LOD Notes |
|---|---|---|---|---|
| Weapons | 256×256 px | 100 / 200 / 400 px | 0.4× / 0.8× / 1.6× | All 3 LODs hand-authored |
| Wielder Characters | 64×128 px | 100 / 200 px | 1.6× / 3.1× | 400px tier not needed — supporting role |
| Enemies | 64×128 px | 100 / 200 px | 1.6× / 3.1× | Same spec as wielders |
| Environment BGs | 375×812 px | Full portrait | 1× | Parallax layers exported individually, packed into single atlas per scene |
| Material Icons | 64×64 px | 48 / 64 / 96 px | Nearest-neighbor scaled | No 400px tier |
| UI Elements | 9-slice base at 48×48 px min | Variable | Integer only | Buttons, panels, frames |
| VFX Frames | 64×64 px per frame | Up to 128 px | 2× | Sprite sheet; flipbook preferred over GPU particles for auras |
| Typography | Vector source (TTF/OTF) | 8/11/14/16/22 px | Integer multiples only | See §7.2 |

Weapons are the only category hand-authored at all 3 LOD tiers. 100px and 200px for other categories may be downscaled from the 400px/master source with nearest-neighbor, then touched up manually if silhouette drifts.

### 8.2 Atlas Strategy — Hard Constraints

**Atlas max: 2048×2048 on mobile and web** (Adreno 505 / Mali-G51 / PowerVR GE8320 tier still ~15–20% of target market). 4096 is NOT safe cross-device.

**Mandatory atlas discipline** (driven by memory ceiling 512 MB mobile → ~180 MB for textures):

| Atlas group | Contents | Notes |
|---|---|---|
| UI atlas | All chamfered panels, icons, buttons (single persistent atlas) | ~20 MB; loaded at boot, never unloaded |
| Environment atlas (per-scene) | All 3 parallax layers of one scene, packed into single 2048×2048 | ~16 MB; only 1 loaded at a time |
| Weapon atlas (per set, 4–6 weapons) | Hero tier — supports dynamic loading during replay | ~16 MB; multiple may be loaded |
| Character atlas | All wielder animations at MVP (2 chars) | Split across 2 atlases per character if needed |
| Enemy atlas | Shared PvE enemy sprites | Per-biome if needed |
| VFX atlas | Forge sparks, impact, auras (flipbook-based) | ~15 MB |
| Material icon atlas | All material icons shared | ~15 MB |

**Max ~10 atlases loaded simultaneously** before exceeding the 180 MB texture ceiling.

**Cross-group mixing is forbidden** — never put UI icons in the character atlas, etc. Prevents granular unloading.

### 8.3 Parallax Constraint (ART COMPROMISE)

*Art-director ideal*: Each parallax layer authored as a separate 2048×2048 atlas for maximum detail per layer.
*Engine floor*: Memory ceiling only supports 1 environment atlas loaded. 18 scene atlases × 16 MB = 288 MB impossible.
*Applied*: **All 3 parallax layers of one scene share a single 2048×2048 atlas.** Layers packed as horizontal strips or sub-regions.

Consequence for art production: per-layer pixel resolution is constrained. Forge Exterior and PvP Arena (largest scenes) will need the most careful packing — expect art-director + technical-artist to do a joint packing pass per scene.

### 8.4 Character Animation Frame Budget (ART COMPROMISE)

*Ideal*: 64×128 px char × 8 animation states × 15 frames avg = 360 frames = 5.6M pixels. Exceeds 2048×2048 atlas (4.2M pixels).
*Options*: (a) reduce per-frame size to 48×48 (visual compromise, smaller characters), (b) split character animations across 2 atlases (cost: +1 atlas load + ~5–8 draw calls per character).
*Applied*: **Option B — 2 atlases per character**. Preserves the 64×128 size. 10 DC absorbs within the 150 DC ceiling.

### 8.5 VFX Constraint — Flipbook Over Particles (ART COMPROMISE)

*Ideal*: GPU particles for all dynamic effects (forge sparks, auras, persistent glow).
*Engine floor*: On Adreno 505-class GPUs, overdraw budget ~2× screen fill. 200 alpha-blended particles × 3 pixels = 600 overdraw pixels/frame threatens budget.
*Applied*:
- **Max 4 simultaneous `GPUParticles2D` emitters, max 150 particles each** — reserved for the forge strike moment, the PvP impact flash, and two ambient effects.
- **Auras, persistent glow, weapon trail effects** use `AnimatedSprite2D` flipbook VFX, not particle systems. Flipbook converts GPU particle cost to predictable DC + texture cost.

### 8.6 File Format Direction

- **All sprites**: PNG-32 (RGBA). Lossless. No JPEG in the pipeline. PNG compression level 9 at export.
- **VRAM compression**: **Disabled** — ETC2/ASTC block compression introduces banding and edge artifacts on pixel art. Lossless PNG only.
- **Mipmapping**: **Off** — blurs at zoom-out, wastes ~33% VRAM per texture.
- **Texture filter**: `Nearest` project-wide (Project Settings > Rendering > Textures > Default Texture Filter).
- **Alpha**: Straight alpha on export (non-pre-multiplied). Godot importer applies pre-multiplication.
- **Color profile**: sRGB. Strip ICC profiles on export.
- **Scaling**: Nearest-neighbor with **integer scaling + letterbox viewport**. Fractional scaling is forbidden — produces half-pixel artifacts.
- **Audio**: OGG Vorbis for music (48 kbps) and longer SFX (96 kbps); WAV 22 kHz mono for SFX < 1 second. Opus supported but OGG Vorbis has broader web browser compatibility.
- **Animation**: `AnimatedSprite2D` + `SpriteFrames` resource (supports variable FPS per clip; frames reference atlas — no per-frame texture overhead). Source authored in Aseprite or layered PSD.

### 8.7 Naming Convention

Pattern: `[category]_[name]_[variant]_[state]_[index].[ext]`

| Token | Values | Example |
|---|---|---|
| category | `weapon`, `char`, `enemy`, `env`, `mat`, `ui`, `vfx` | `weapon` |
| name | Slug, no spaces | `sword` |
| variant | Element or rarity: `fire`, `ice`, `void`, `legendary`, `common` | `fire` |
| state | Animation state: `idle`, `windup`, `impact`, `settle`, `destroyed` | `windup` |
| index | Zero-padded 3-digit frame number | `001` |

Full example: `weapon_sword_fire_legendary_windup_001.png`

If a token does not apply, omit it — do not substitute with `default`.

### 8.8 Animation Standards

| Category | Frame Rate | Idle Cycle | Impact Duration | Notes |
|---|---|---|---|---|
| Weapons | 10 fps | 24 frames (2.4 s) | 6 frames (0.6 s) | Anticipation 2f + Overshoot 2f + Settle 2f |
| Characters / Enemies | 8 fps | 16 frames (2.0 s) | 4 frames (0.5 s) | Supporting; must not compete with weapon |
| VFX | 12 fps | Loop or one-shot | 8 frames max | One-shots end on transparent frame |

Kinetic Honesty mandatory for weapons. No state transitions linearly. Onion-skin reference frames delivered as separate layered source file for all weapon animations.

### 8.9 Color Palette Enforcement

Artists work exclusively within the 7-color primary palette + active element palette (§4). Off-palette colors are grounds for rejection.

**Palette swap implementation**: GPU shader with **single bound LUT texture as global uniform** (not per-sprite). Artists deliver one palette-indexed source sprite; the shader remaps indexed colors at runtime. Per-sprite LUT textures are forbidden — they break batching and add ~30 DCs.

- Cost: 1 additional texture sample per pixel per sprite.
- Limit: up to 8 simultaneously active palette-swapped sprites without GPU fragment load concern. Measure beyond.
- Verify `Texture` uniform type (not `Texture2D`) since Godot 4.4 — see `docs/engine-reference/godot/modules/rendering.md`.

### 8.10 Silhouette Validation Checkpoint

Two gates:
1. **Thumbnail proof** (before full detail): 100px flat-black fill delivered; art-director approves or rejects. Rejection here costs 15 minutes, not 3 days.
2. **Final export review** (before atlas integration): Exported sprite checked at 100px against approved thumbnail. Drift > 10% of silhouette mass → blocking rejection.

Rejection criteria: silhouette ambiguous at 100px, fewer than 2 distinct regions, reads as a generic blob with no directional intent.

### 8.11 LOD Production Plan

| Category | 400px | 200px | 100px |
|---|---|---|---|
| Weapons | Hand-authored | Hand-authored | Hand-authored |
| Characters / Enemies | Hand-authored | Downscale + touch-up | Downscale only |
| Environments | N/A (full-screen) | N/A | N/A |
| Material Icons | Hand-authored at 64px | Downscale | Downscale |
| UI Elements | Hand-authored at 48px base | N/A | N/A |

The 400px weapon tier is the artistic master. 200px and 100px tiers are independent compositions that preserve silhouette read — NOT crops of the master.

### 8.12 Memory Budget (Mobile, 180 MB texture ceiling)

| Category | Budget |
|---|---|
| UI (panels, icons, HUD) | 20 MB |
| Active scene environment | 30 MB |
| Characters + active weapon | 25 MB |
| Enemies (up to 4 active) | 20 MB |
| Material icons (current batch) | 15 MB |
| VFX atlases | 15 MB |
| Typography | 5 MB |
| Headroom / streaming buffer | 50 MB |
| **Total** | **180 MB** |

### 8.13 Draw Call Budget (Mobile, 150 DC target / 200 DC ceiling)

| Category | DC Allocation |
|---|---|
| UI | 30 |
| Environment parallax (3 layers, 1 atlas) | 6 |
| Characters + weapon sprites | 10 |
| Enemies (up to 4) | 8 |
| VFX (particles + flipbooks) | 40 |
| Material icon grid (up to 50 visible) | 10 |
| Fonts / labels | 15 |
| Headroom / dynamic | 31 |
| **Total** | **150** (50 DC safety margin under 200 hard ceiling) |

**Batching discipline**: All sprites within a category must share a single atlas texture. Z-index layering must keep draw categories contiguous — a weapon sprite between two UI elements breaks batch order.

### 8.14 Build Size Targets

**Mobile APK/IPA initial bundle (< 80 MB)**:
- Godot runtime + GDScript VM: ~18 MB
- Boot + UI atlas + fonts: ~4 MB
- Default start scene (Forge Interior): ~6 MB
- 2 characters + 1 weapon: ~3 MB
- Core audio (title + UI SFX): ~4 MB
- Game code + data: ~5 MB
- **Initial total**: ~40 MB (40 MB headroom for content)

**Web HTML5 initial bundle (< 15 MB)**:
- Godot Compatibility `.wasm` compressed: ~6 MB
- Boot + UI atlas: ~2 MB
- Fonts + boot audio: ~0.5 MB
- **Initial total**: ~8.5 MB (6.5 MB margin for first scene streaming)

Everything else streams via `ResourceLoader.load_threaded_request()` in scene-scoped `.pck` files < 5 MB each. Scene transition must tolerate up to 1.5 s streaming gate on 3G/4G.

### 8.15 Asset Approval Workflow

All new weapon assets require art-director sign-off. Other categories: lead artist sign-off + art-director spot-check on first asset of a new category.

Delivery package:
1. Final PNG(s) following naming convention
2. Source file (Aseprite or layered PSD) in `assets/art/source/[category]/`
3. Silhouette proof at 100px (flat black fill, white background)
4. Reference sheet or concept sketch
5. Palette compliance screenshot (color picker confirms on-palette)

No asset enters the atlas pipeline without items 1–5 present.

---

## Section 9 — Reference Direction

> These references are lenses, not blueprints. Draw the named technique; leave the rest behind.

### 9.1 Vagrant Story (Square, 2000) — Silhouette and Material Weight

- **Draw from**: Every weapon and armor piece reads as a distinct object class at small size — crossbow vs. sword vs. mace never ambiguous at 64px. Vagrant Story achieves this via extreme contrast between the weapon's dominant mass and its accent geometry, never distributing detail evenly. Apply to weapon thumbnails and inventory icons: one dominant shape, one accent, nothing else.
- **Avoid**: The desaturated grey-on-grey environment palette and the oppressive architectural density. The Meta-Forge runs warmer and needs breathing room between vertical elements.
- **Why it matters**: Weapons are the protagonist. If a Forge-crafted blade does not read faster than a rival idle game's icon at thumbnail scale, the entire player-fantasy contract breaks.

### 9.2 Yoshitaka Amano — Illustration Work (1980s–1990s, Final Fantasy concept art) — Color Negative Space

- **Draw from**: Amano's use of unpainted paper as an active color — warm off-white voids that make saturated ink sing louder by contrast. His figures never fill their bounding box; the negative space carries as much weight as the linework. Apply to character splash art and loading screens: resist filling every pixel. Let Forge Ember read against emptiness, not against competing hues.
- **Avoid**: The literal linework style — spidery, overlapping, anatomically loose. The Meta-Forge needs legible silhouettes and mobile-readable forms, not deliberate ambiguity.
- **Why it matters**: Non-game source for color/mood. Directly enforces the saturation cap: a single saturated accent achieves emotional temperature without breaking environment palette rules.

### 9.3 Byzantine Mosaic — Ravenna, 6th Century (San Vitale / Galla Placidia) — Architectural Light Logic

- **Draw from**: Gold tesserae function as internal light sources — they do not reflect ambient light, they emit their own. Dawn Brass trim on Forge architecture should behave the same way: emit at constant luminance regardless of scene lighting, creating the impression that the foundry is alive with residual heat. Compositor rule for environment artists.
- **Avoid**: Flat symmetric icon arrangements and rigid frontality of Byzantine figure composition. The game's camera has depth; parallax layers must feel inhabited, not ceremonial.
- **Why it matters**: Non-game architectural source. The gold-emits-own-light rule is the single most direct visual signal that the Forge is sacred space, not industrial backdrop — keeps Volcanic Romanesque from reading as generic fantasy dungeon.

### 9.4 Castlevania: Symphony of the Night (Konami, 1997) — UI Filigree Economy

- **Draw from**: Inventory and status screens use a single ornamental motif — a thin gothic arch — applied at two scales (panel border, sub-element divider) and never a third. Reads as a designed system, not decoration-for-decoration. Apply the same economy: one chamfered-rectangle language at panel scale, one acute-60° accent at icon scale, nothing further.
- **Avoid**: The dark-on-dark castle palette, and the inventory's reliance on static menus. The Meta-Forge UI operates in mobile ambient light with one thumb.
- **Why it matters**: UI/type reference. Prevents ornamental creep — a common failure mode in RPG idle games where successive feature additions each bring their own decorative language until UI becomes unreadable.

### 9.5 Mamoru Oshii's Ghost in the Shell (Production I.G, 1995) — Kinetic Stillness

- **Draw from**: Action sequences achieve weight by holding on near-stillness — a held pose for two frames longer than expected before a cut — making the burst of motion that follows feel physically massive. Apply as animation timing rule: idle animations hold one to two frames longer at the apex before returning; attack animations hold impact frame for three frames before recovery arc. Kinetic Honesty expressed as specific frame count.
- **Avoid**: Desaturated cyberpunk palette, slow philosophical pacing as narrative model, and any attempt to reference the film's aesthetic as a whole. Single kinetic timing technique, borrowed and recontextualized.
- **Why it matters**: Animation/kinetic reference. Mobile idle RPGs almost universally suffer from floaty cost-cut animation. The held-frame rule is cheap to implement, requires no additional art assets, and is the difference between a weapon swing that lands and one that passes through.
