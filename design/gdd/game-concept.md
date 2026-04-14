# Game Concept: The Meta-Forge

*Created: 2026-04-13*
*Status: Draft*

---

## Elevator Pitch

> Es un **RPG idle mobile** donde **las armas son el protagonista**: forjás espadas combinando materiales con propiedades conocidas, ejecutás un minijuego de herrería que refina su ADN único, y las mandás a torneos PvP asíncronos contra las builds de otros jugadores. Como Forge Master **AND ALSO** cada arma tiene una "partida de nacimiento" irrepetible que define cómo se comporta en combate.

**Test de 10 segundos**: "Forjás armas únicas mezclando materiales, y las peleás contra otros jugadores sin necesidad de estar online al mismo tiempo."

---

## Core Identity

| Aspect | Detail |
| ---- | ---- |
| **Genre** | Mobile RPG / Idle / Asynchronous PvP / Crafting-driven |
| **Platform** | Mobile (iOS + Android) + Web (HTML5) |
| **Target Audience** | Ver sección Target Player Profile |
| **Player Count** | Single-player con PvP asíncrono (snapshots vs snapshots) |
| **Session Length** | 15-30 min (dosis diaria); extensible a sesiones largas opcionales |
| **Monetization** | Free-to-play con pilar anti-pay-to-win: cosméticos, conveniencia, battle pass. Detalle F2P pendiente de un ADR. |
| **Estimated Scope** | Large (12-18 meses a Tier 2 / Launch público, dev solo o equipo de 2-3) |
| **Comparable Titles** | Forge Master (core idle + forja), AFK Arena (PvP asíncrono), Legend of Legaia (Arts system), Chrono Cross (estética y música) |

---

## Core Fantasy

**Ser el herrero legendario cuyas armas definen el meta del servidor.**

El jugador no es un guerrero — es el artesano oculto que forja las herramientas con las que otros pelean. La satisfacción viene de ver tu creación triunfar en manos de un héroe, y de descubrir un combo de materiales que nadie más conoce todavía. Es la fantasía del "artesano maestro" fusionada con el placer del "experimentador alquímico": cada día entrás al taller, mezclás, ejecutás, descubrís, y salís con una o dos armas que solo vos tenés.

A diferencia de los RPG móviles donde sos un comandante o un coleccionista de héroes, acá el **objeto** es el protagonista, no el personaje. Las armas se firman con tu nombre; las builds te sobreviven en el ladder incluso cuando no estás jugando.

---

## Unique Hook

**Cada arma tiene un ADN único no repetible que emerge de la combinación de materiales + ejecución del minijuego de forja.** Como Forge Master, **AND ALSO** vos no apretás botones durante la batalla: vos **diseñás** el arma, y el arma pelea sola contra snapshots de otros jugadores. El meta del servidor lo construye la comunidad descubriendo qué combos de materiales ganan contra qué arquetipos de defensa.

Componentes del hook:
- **Combinatoria conocida** (materiales con propiedades claras, como BOTW cooking)
- **Ejecución con skill** (minijuego de forja con timings, estilo Paper Mario)
- **Variabilidad emergente** (afijos y mutaciones no deterministas pero acotadas)
- **PvP asíncrono con replay** (pelea snapshot vs snapshot, mirable en video)
- **Meta descubierto por la comunidad** (no impuesto por el equipo de diseño)

---

## Player Experience Analysis (MDA Framework)

### Target Aesthetics

| Aesthetic | Priority | How We Deliver It |
| ---- | ---- | ---- |
| **Sensation** (sensory pleasure) | 2 | Música original estilo JRPG clásico, arte HD-2D pixel con animaciones con peso, juice en cada forja (VFX de brasas, chispas, reveal del arma) |
| **Fantasy** (make-believe) | 4 | Rol del herrero-alquimista en un mundo de fantasía clásica con materiales míticos y recetas perdidas |
| **Narrative** (drama, story) | 7 | Lore ambiental en descripciones de materiales y armas. Sin campaña obligatoria. |
| **Challenge** (mastery) | 2 | Skill de minijuego de forja + conocimiento meta de combos de materiales + ladder PvP |
| **Fellowship** (social) | 6 | PvP asíncrono provee competencia indirecta. Guilds/clanes son expansión post-launch. |
| **Discovery** (exploration, secrets) | 3 | Recetas ocultas de combos, afijos raros, descubrimiento del meta por la comunidad |
| **Expression** (creativity) | 1 | Diseñar tu build única, firmar tus armas, estilo de forja propio |
| **Submission** (relaxation) | 5 | Sesiones cortas flexibles, idle progression entre sesiones, no requiere reflejos |

**Primario**: Expression. **Secundario**: Challenge + Sensation. **Terciario**: Discovery.

### Key Dynamics (comportamientos emergentes)
- Los jugadores experimentarán con combinaciones raras de materiales para encontrar sinergias ocultas.
- La comunidad compartirá capturas de armas únicas y builds exitosas.
- Emergerá un "meta" cambiante donde builds dominantes son contradas por nuevas builds.
- Jugadores casuales volverán 15 min por día para forjar + ver replays; hardcore pasarán horas optimizando.
- Los jugadores firmarán armas memorables y las conservarán como trofeos.

### Core Mechanics (sistemas que construimos)
1. **Sistema de materiales** con propiedades determinísticas (tipo, elemento, stat base, afinidad)
2. **Minijuego de forja** con inputs de timing/secuencia que amplifican propiedades y pueden agregar afijos
3. **Sistema de afijos y mutaciones** con variabilidad acotada (RNG controlado)
4. **Combate automático** con reglas transparentes (simulable headless para PvP asíncrono rápido)
5. **PvP asíncrono por snapshots** con ladder, replays, y matchmaking por poder de build

---

## Player Motivation Profile

### Primary Psychological Needs Served (SDT)

| Need | How This Game Satisfies It | Strength |
| ---- | ---- | ---- |
| **Autonomy** | Elegís qué materiales mezclar, qué afijo buscar, qué equipo armar, contra qué ligas medirte. Ningún paso forzado. | Core |
| **Competence** | Tres ejes de mejora: skill del minijuego, conocimiento del árbol de combos, optimización de build. Cada sesión deja algo mejor. | Core |
| **Relatedness** | Ves builds de otros jugadores en replays. Competís por ranking. Guilds en expansión. | Supporting |

### Player Type Appeal (Bartle + Quantic Foundry)

- [x] **Achievers** — Ladders, collection de materiales y recetas, milestones de forja
- [x] **Explorers** — Descubrir recetas ocultas, mapear el árbol de combos, entender afijos raros
- [ ] **Socializers** — Limitado al inicio (replays, ranking). Expansión vía guilds/clanes post-launch.
- [ ] **Killers/Competitors hardcore** — NO es para ellos. Sin combate en tiempo real.

**Primario**: Achievers + Creators (Quantic Foundry: Strategy + Design + Mastery)
**Secundario**: Explorers (Discovery)
**NO para**: twitch competitors, storyline-focused players, MMO guild warriors hardcore al launch

### Flow State Design

- **Onboarding curve**: Primeros 10 min — 1 material, 1 forja guiada, 1 victoria PvE, reveal del hook PvP asíncrono.
- **Difficulty scaling**: El minijuego de forja se complica con más inputs a medida que desbloqueás armas avanzadas. El PvP separa en ligas por poder de build, no por tiempo jugado.
- **Feedback clarity**: Cada arma muestra stats completas + comparación con tu equipo actual. Replays explican por qué ganaste/perdiste.
- **Recovery from failure**: Perder en PvP no bajás mucho de liga (decay suave). Un "fallo" en forja sigue produciendo un arma usable (no se pierde el material).

---

## Core Loop

### Moment-to-Moment (30 segundos)
1. Elegir 2-3 materiales del inventario → determinan tipo/elemento/stats base del arma.
2. Ejecutar minijuego de forja (15-20 seg de inputs de timing/secuencia) → amplifica propiedades y puede agregar afijos.
3. Ver el arma resultante con su "partida de nacimiento" (stats + afijos + visual único).

**Gancho**: "¿Qué sale si combino X con Y?"

### Short-Term (5-15 minutos)
1. Forjar 2-3 armas con materiales del stock.
2. Equipar tu héroe(s) con las mejores.
3. Probar en PvP asíncrono rápido (replay de ~1 min) o PvE farmeo.
4. Recompensa: materiales, gold, XP, info del matchup.

**Gancho**: "Una forja más, a ver si rompo este matchup."

### Session-Level (15-30 min, dosis diaria)
1. Abrir el juego → ver replays de PvP acumulados mientras no estabas + recompensas idle.
2. Cosechar materiales generados offline.
3. 3-5 ciclos de forja y prueba.
4. Enviar tus mejores builds a "defender" en el PvP ranked.
5. Salir pensando en qué forjar mañana.

**Natural stopping point**: después de enviar a defender.

### Long-Term Progression (días/semanas/meses)
- **Semana 1**: Materiales básicos, recetas simples, primera liga.
- **Semanas 2-4**: Materiales raros, combos emergentes, escalada de ranking.
- **Mes 2+**: Recetas secretas (combos ocultos descubiertos por la comunidad), ligas altas, armas firmadas.
- **Long-term**: Meta emergente — el jugador busca "la build que rompe el meta" actual.

### Retention Hooks
- **Curiosity**: ¿Qué material raro todavía no probé? ¿Qué combo nadie descubrió?
- **Investment**: Armas firmadas, progreso en ligas, recetas desbloqueadas, reputación.
- **Social**: Replays de tus builds defendiendo, ver tu ranking, comunidad compartiendo descubrimientos (post-launch: guilds).
- **Mastery**: Optimizar skill del minijuego, profundidad del árbol de combos, subir liga.

---

## Game Pillars

### Pillar 1: Estética es el primer impacto
Música, arte y juice visual son no-negociables. Si entra por la vista y el oído, el resto se perdona.

*Design test*: Entre 5 armas feas vs 3 hermosas → elegimos las 3 hermosas.

### Pillar 2: Variabilidad emergente
No hay dos forjas ni dos batallas iguales. El sistema le regala sorpresas al jugador dentro de reglas conocidas.

*Design test*: Si una mecánica es divertida pero determinista → agregamos variabilidad controlada o la cortamos.

### Pillar 3: Sesión corta, profundidad infinita
10 minutos diarios dejan al jugador satisfecho; 10 horas revelan más profundidad.

*Design test*: Si una feature requiere >5 min sin stopping point natural → se rediseña o se corta.

### Pillar 4: Meta emergente, no impuesto
Las builds dominantes las descubre la comunidad. Diseñamos herramientas, no respuestas.

*Design test*: Si queremos dar "build recomendada" al jugador → la cortamos. Que la descubra.

### Anti-Pillars (lo que este juego NO es)

- **NO combate activo en tiempo real**: Todo PvP es asíncrono con replays. Sin botones de habilidad durante la pelea. Compromete "sesión corta" y accesibilidad mobile.
- **NO pay-to-win absoluto**: F2P puede competir en top ranks; pagar acelera, no desbloquea poder exclusivo. Compromete "Meta Emergente".
- **NO narrativa lineal obligatoria**: Lore como condimento ambiental, no como campaña. Compromete scope y focus.
- **NO PvP sincrónico**: Ni lobbies vivos, ni esperar oponente, ni timers de turno. Compromete sesión corta flexible.

---

## Visual Identity Anchor

**Dirección: Chrono Revival (HD-2D Pixel)**

### Regla visual (una línea)
> "Pixel art HD con animaciones con peso, paleta saturada pintada (sombras cálidas, luces específicas), y parallax de capas."

### Principios de soporte

1. **Paleta saturada pero pintada** — no neón, no pastel lavado.
   *Design test*: Si un asset se ve "lavado" o "neón" → se repinta.

2. **Silueta legible a 100px** — es mobile; armas y héroes deben reconocerse en miniatura.
   *Design test*: Si un asset no es reconocible a 100px de alto → se rediseña la silueta.

3. **Animación con peso (anticipación + overshoot + settle)** — no interpolaciones lineales.
   *Design test*: Si un sprite solo translada sin ease → se re-anima con curva.

### Color philosophy
Paletas cálidas dominantes (ámbar, rojo tierra, violeta crepuscular); fríos (azul acero, cyan) como acentos específicos por escena. Cada escena tiene una **dominante emocional** — evitamos saturación monótona. La forja misma es el momento más cálido del loop (brasas, fuego, metal incandescente); el PvP puede virar a fríos según la zona.

### Referencias visuales
- Chrono Trigger / Chrono Cross (tono, paleta, sprites con alma)
- Octopath Traveler / HD-2D (parallax y profundidad)
- Sea of Stars (timing de animación con peso)
- Legend of Legaia (silueta de personajes con weight)

---

## Inspiration and References

| Reference | What We Take From It | What We Do Differently | Why It Matters |
| ---- | ---- | ---- | ---- |
| **Forge Master** | Core idle + forja satisfactoria; economía de materiales | Le metemos variabilidad emergente, modos de juego, PvP asíncrono con replays, meta vivo | Valida que la audiencia existe y el core engancha; identifica el hueco que ocupamos |
| **AFK Arena** | PvP asíncrono con snapshots; dosis diaria; idle progression | Focus en armas no en héroes gacha; skill de forja reemplaza pulls | Valida el modelo de retención mobile con PvP asíncrono |
| **Legend of Legaia** | Sistema de Arts (combos con skill del jugador) | Aplicado a forja en vez de combate en vivo | Fuente del minijuego estratégico con inputs |
| **Chrono Cross** | Estética, música, arte 2D con alma | Mobile HD-2D moderno, escala mobile | Ancla emocional del equipo; diferenciador visual en app store |
| **Counter-Strike** | Cada partida es única; meta que la comunidad persigue | Asíncrono en lugar de sincrónico | Valida el appeal de variabilidad emergente + meta comunitario |
| **Hearthstone Battlegrounds** | Auto-battler asíncrono con profundidad estratégica | Centrado en forja de arma no en picks | Valida viabilidad de auto-combat mobile |
| **Diablo / Path of Exile** | Loot como recompensa emocional; afijos; builds | Forja directa en lugar de farmeo de drops | Valida la satisfacción de "item único" |

**Non-game inspirations**:
- Herrería histórica japonesa (proceso ceremonial, materiales con alma)
- Alquimia renacentista (combinaciones con propiedades emergentes)
- Música de Yasunori Mitsuda (Chrono Cross OST) como norte emocional del audio
- Ilustraciones de Akira Toriyama en RPG clásicos (silueta clara, paleta cálida)

---

## Target Player Profile

| Attribute | Detail |
| ---- | ---- |
| **Age range** | 22-40 |
| **Gaming experience** | Mid-core (jugó JRPGs en su momento, juega mobile RPG hoy) |
| **Time availability** | 15-30 min/día; picos de 1-2 hr en fin de semana |
| **Platform preference** | Mobile (commute, cama, break) + casual Web en escritorio |
| **Current games they play** | AFK Arena, Raid Shadow Legends, Forge Master, Hearthstone Battlegrounds, ocasional CS o MMO |
| **What they're looking for** | RPG mobile con alma (arte + música), profundidad real de builds, no twitch, no FOMO grotesco |
| **What would turn them away** | Pay-to-win descarado, estética genérica "fantasy gacha", requerir estar online, UI sobrecargada de popups |

---

## Technical Considerations

| Consideration | Assessment |
| ---- | ---- |
| **Recommended Engine** | A decidir en `/setup-engine`. Godot 4.6 es candidato fuerte por target mobile+web (HTML5 export limpio, buen 2D HD). Unity como alternativa si se necesita ecosistema de monetización más maduro. |
| **Key Technical Challenges** | (1) Simulación de combate determinista headless para PvP asíncrono. (2) Sistema de afijos escalable sin explosión combinatoria. (3) Ladder con matchmaking por "poder de build" no por nivel. (4) Optimización del renderer HD-2D en mobile de gama baja. |
| **Art Style** | HD-2D Pixel con parallax (Chrono Revival). Animación con peso; silueta legible a 100px. |
| **Art Pipeline Complexity** | Medio — Pixel HD es escalable con sprites modulares stackables; un artista puede ser bottleneck. |
| **Audio Needs** | Music-heavy — la música es pilar no-negociable. Mínimo 5 tracks para launch, más adaptive layers (forja, combate, ambiente). |
| **Networking** | Client-server ligero — enviás/recibís snapshots de build y resolvés combate offline. No hay estado en tiempo real compartido. |
| **Content Volume** | Launch: 50+ materiales, 30+ recetas documentadas + N descubribles, 20+ afijos, 5-8 arquetipos de héroe, 10+ ligas PvP. |
| **Procedural Systems** | ADN de arma (combinación determinista + mutación RNG acotada). Afijos con pool controlado. Generación de nombres de arma (para "firmar"). |

---

## Risks and Open Questions

### Design Risks
- **Core loop puede no engancharse**: si el minijuego de forja + combinar materiales no es divertido a los 5 min, el juego muere. Mitigación: prototipo Tier 0 con 10-15 testers externos antes de invertir en PvP.
- **Meta estancado**: si los jugadores encuentran la build óptima rápido y el meta cristaliza en 1 build, se pierde la variabilidad. Mitigación: rotación de materiales estacionales + ajustes de balance.
- **Pasividad percibida del PvP**: ver un replay puede sentirse menos satisfactorio que pelear en vivo. Mitigación: replays con narración y "momentos clave" destacados; possibility de retar un amigo que gane bono.

### Technical Risks
- **Simulación determinista cross-platform**: que una pelea dé el mismo resultado en iOS, Android y Web. Riesgo si usamos floating point no-determinístico.
- **Escalabilidad del backend**: ladder global con matchmaking y replay storage crece con el userbase. Mitigación: empezar con snapshot-store simple (serverless) y migrar.

### Market Risks
- **Saturación del mercado mobile RPG**: miles de juegos compiten. Diferenciador único debe ser bien comunicado. Mitigación: branding visual fuerte (Chrono Revival) y hook comunicable en 10 segundos.
- **Monetización F2P ética puede no sustentar**: si no somos pay-to-win, ¿cómo monetizamos? Mitigación: cosméticos + battle pass + conveniencia (slots de forja, auto-tap idle). ADR de monetización pendiente.

### Scope Risks
- **Arte HD-2D cuello de botella**: un artista pixel HD puede bloquear el cronograma. Mitigación: sprites modulares + asset reuso + evaluar AI tooling para variaciones.
- **Balance de materiales** crece combinatorio: 50 materiales × combinaciones × afijos = espacio gigante. Mitigación: herramientas internas de simulación; community testing early.

### Open Questions
- ¿El minijuego de forja es uno solo evolutivo, o varias mini-mecánicas distintas por tipo de arma? (Resolver en prototipo Tier 0.)
- ¿PvP es 1v1 arma vs arma, o equipo de 3 héroes? (Explorar ambas en prototipo.)
- ¿Cuántos minutos dura una sesión idle sin activity? (A/B test en soft-launch.)
- ¿Engine final: Godot 4.6 o Unity? (Resolver en `/setup-engine`.)
- ¿Modelo de economía de materiales (regenerados offline, comprables, dropeados)? (Diseñar en GDD de Economía.)

---

## MVP Definition

**Core hypothesis**: *"Los jugadores encuentran divertido combinar materiales + ejecutar el minijuego de forja, incluso sin PvP."*

Primero probamos si el **core loop de forja es intrínsecamente divertido**. Si falla este test, nada más importa.

**Required for MVP (Tier 1 — jugable)**:
1. **5-10 materiales** con propiedades claras y combinables.
2. **Minijuego de forja** funcional con 1-2 tipos de input (timing + secuencia).
3. **Sistema de afijos** básico (5-8 afijos posibles, pool simple).
4. **1 héroe "base"** con slot de arma equipada.
5. **Combate PvE simulado** contra 3-5 enemigos fijos (farmeo + prueba de build).
6. **PvP asíncrono básico**: enviás snapshot, recibís resultados de snapshots enfrentados, ver replay texto o visual simple.
7. **UI mobile funcional** (portrait mode, no assets finales — placeholder consistente).
8. **Sistema de progresión mínimo**: gold, XP, desbloqueo de más materiales.
9. **1 track de música** que capture el mood final.

**Explícitamente NO en MVP**:
- Ligas/ranking (llega en Tier 2)
- Guilds/clanes (Tier 3)
- Cosméticos/monetización (Tier 2)
- 50+ materiales — al MVP alcanza con 10-15
- Arte HD-2D completo — MVP con placeholder art consistente
- Narrativa/lore extenso (Tier 2)

### Scope Tiers

| Tier | Content | Features | Timeline |
| ---- | ---- | ---- | ---- |
| **Tier 0 — Prototipo** | Core loop aislado, 5 materiales, sin arte final | Forja + combate PvE simple | 4-6 semanas |
| **Tier 1 — MVP jugable** | 10-15 materiales, placeholder art, 1-2 tracks música | Core loop completo + PvP asíncrono básico + progresión | 3-4 meses (post-prototipo) |
| **Tier 2 — Launch público** | 50+ materiales, arte HD-2D completo, 5+ tracks | Ligas PvP, defensa/ataque, progresión 2-3 meses, F2P balanceado | 6-9 meses (post-MVP) |
| **Tier 3 — Live service** | Materiales mensuales, eventos, cosméticos | Guilds, dungeons cooperativos, battle pass | 9-18 meses (post-launch, continuo) |

---

## Next Steps

- [ ] Correr `/setup-engine` para decidir engine (Godot 4.6 candidato) y poblar docs de referencia version-aware
- [ ] Correr `/art-bible` para crear la art bible completa con Chrono Revival como ancla
- [ ] Validar concepto con `/design-review design/gdd/game-concept.md`
- [ ] Discutir visión con `creative-director` para refinar pilares si surge disonancia
- [ ] Descomponer en sistemas con `/map-systems` (forja, combate, PvP asíncrono, economía, progresión)
- [ ] Escribir GDD por sistema con `/design-system` (empezar por Forja)
- [ ] `/create-architecture` — blueprint técnico maestro + lista de ADRs requeridos
- [ ] `/architecture-decision` (×N) — un ADR por decisión clave (determinismo, stack backend, monetización, etc.)
- [ ] `/gate-check` — validar readiness antes de entrar a producción
- [ ] `/prototype forge-core` — prototipo del Tier 0 para validar el core hypothesis
- [ ] `/playtest-report` — validar el prototipo con testers externos
- [ ] `/sprint-plan new` — planear primer sprint si el prototipo valida
