# Phase Drift — Design Document
Round 12

---

## DESIGN_DOC
game_name: phase-drift
display_name: Phase Drift

---

## Identity

**Game name:** Phase Drift
**Tagline:** You can only see half the world, but both halves will kill you.

**What is the player:**
A signal ghost — the residual consciousness of a probe that fragmented across two overlapping resonance layers during a failed transit. The probe is small, crystalline, and can vibrate between layers, but not simultaneously inhabit both.

**World feel:**
You are inside a dead city that exists twice — once as hard blue geometry in the present, once as a bleeding red afterimage of itself displaced 0.3 seconds into the past. The walls are the same walls. The threats are different in each version. You can hear the other world but you cannot see it until you step through, and by then the information you acted on is already stale.

**Emotional experience:** Double-vision fluency

**Reference games:**
- *Metal Gear Solid* (V.REX codec call DNA): the invisible layer is not empty. You always know threats exist there. You never know *where* until you commit and it is too late. The paranoia is the game.
- *Wipeout HD* (Fury track restart DNA): phase-switches are instantaneous and punishing with zero forgiveness. The ghost of your mistake is visible on the floor. You read it. You go again. No narrative. No tutorial. Only rhythm.
- *Inside* (environmental logic DNA): the rule is learned by dying once, in under 20 seconds, with zero text. The world explains itself through consequence.

---

## Visual Spec

**Background color:** `#0A0F1E` (near-black deep navy — the void between layers)
**Primary color (Layer A / present):** `#00E5FF` (hard cyan — live geometry, visible walls, floor grid)
**Secondary color (Layer B / echo):** `#FF3366` (hot magenta-red — the other world's geometry, threats, trails)
**Accent color:** `#FFFFFF` (pure white — phase-switch membrane flash, exit beacon, death burst)

**Layer A ambient tint on all geometry when in Layer A:** `#00E5FF` at 8% overlay on all surfaces.
**Layer B ambient tint when in Layer B:** `#FF3366` at 8% overlay on all surfaces.
**When viewing the opposite layer's trail-shadows (always visible regardless of current layer):** opacity 12%, `#FF3366` for Layer B threats visible in Layer A, `#00E5FF` for Layer A threats visible in Layer B.

**Bloom:** Yes. Strength 0.6, threshold 0.75. Apply only to emissive elements: player token, exit beacon, active threat bodies, phase-switch flash. Not to static geometry.
**Vignette:** Yes. Radial, black, opacity 0.45, falloff starts at 65% of screen radius.
**Edge pulse:** When a threat is within 3 tiles in the opposite layer, the vignette edge pulses with the opposite layer's color (`#FF3366` when in Layer A and a B-threat is close). Pulse amplitude 0.15 opacity, frequency matches the sub-bass heartbeat cadence.

**Camera:** Fixed isometric projection. Orthographic (no perspective). Grid tilted 30° around the X axis, rotated 45° around the Z axis. Camera does not move or rotate during gameplay — the entire level is always visible. Grid tiles are 48×24px rendered (standard isometric diamond). The level grid is centered in the viewport with 40px padding on all sides.

**Player silhouette (5 words):** small crystalline isometric diamond token

**Player token visual:** An isometric diamond (top face only) 16×8px, filled `#FFFFFF`, with a 2px inner glow in the current layer's color (`#00E5FF` in Layer A, `#FF3366` in Layer B). On the floor beneath the token, a 24×12px elliptical shadow at 30% opacity in the current layer color. The token does not animate during idle — it is still and tense.

**Phase-switch membrane flash:** On any phase transition, a ring expands from the player's position — circle starts at 0px radius, expands to 80px radius in 0.15s, stroke 2px white, opacity fades from 1.0 to 0 over the 0.15s duration. Simultaneously, the entire screen flashes to 15% white for 2 frames (33ms at 60fps).

**Threat visual (in active layer):** Isometric diamond, same size as player, filled with current layer color (`#FF3366` for Layer B threats when player is in Layer B), with inner glow. Threats that patrol emit a faint `#FF3366` or `#00E5FF` line trail behind them as they move — 4 tiles long, fading from 40% to 0% opacity along the trail length.

**Trail-shadow (opposite layer, always visible):** Same position as the threat's actual position in the other layer, rendered at 12% opacity, no glow, no bloom. The shadow renders beneath all active-layer geometry — it bleeds *through* the floor as a stain, not on top of it. The shadow lags the actual threat position by 0 seconds (it is real-time — the shadow is always correct). This is intentional: 1x players don't know how to read it; 10x players know it's an accurate live feed and use it.

**Death visual:** On death, the player token emits a `#FFFFFF` burst — 8 particles radiate outward at random angles, each a 2×2px square, traveling 40px in 0.3s and fading to 0. The player's outline remains on the floor as a ghost — a 16×8px isometric diamond outline only (no fill), `#FFFFFF` at 20% opacity, for exactly 15 seconds before fading over 1.0s.

**Exit beacon:** A 16×8px isometric diamond pulsing between `#FFFFFF` at 100% and `#FFFFFF` at 40% opacity, period 1.2s, sine easing. A vertical line of light rises from the beacon's center — 1px wide, 32px tall, fading from 80% to 0% from bottom to top. Always visible regardless of current layer.

**Wall geometry:** Isometric walls are 48×48px faces (left and right faces of a cube). In active layer: stroke `#00E5FF` or `#FF3366` at 100% opacity, fill with the layer color at 6% opacity. Walls from the opposite layer are rendered as outlines only at 10% opacity (you can *almost* see them).

**Floor grid:** Each tile has a 1px border in the current layer color at 20% opacity. No fill on floor tiles — background shows through. This keeps the world feeling sparse and technical.

---

## Sound Spec

**Music:**
Synthesized electronic score. 116 BPM. Structure: a 4-bar repeating loop built from two interlocking sequences — one in Layer A register (high-mid, `#00E5FF` feeling: clean, cold, precise arpeggios) and one in Layer B register (low-mid, `#FF3366` feeling: warmer, slightly detuned, slightly behind the beat by 30ms to suggest temporal lag). Both sequences play simultaneously at all times; Layer A sequence is at full volume, Layer B sequence is at 20% volume when player is in Layer A, and vice versa when in Layer B. This means the music audibly shifts in character — never cuts — when you phase. There is no sudden musical change; only a crossfade of emphasis over 0.3s.

At max proximity alert (threat within 2 tiles in opposite layer): the sub-bass heartbeat (see SFX #1) rises in the mix. The musical arpeggios reduce to 60% volume to make room for it.

Music stops entirely for 0.5s on death, then resumes at the start of the next measure (quantized restart, not immediate).

On reaching the exit: the music enters a 2-bar outro variant — same loop but with a high-register crystalline chord (C major, voicing C5-E5-G5-B5) that sustains and fades over 4 seconds.

**Sound Effects:**

1. **Sub-bass proximity heartbeat** — `Tone.Synth({ oscillator: { type: "sine" }, envelope: { attack: 0.005, decay: 0.1, sustain: 0, release: 0.05 } })`. Pitch: 40Hz when threat is 6+ tiles away in opposite layer, interpolates linearly to 80Hz when threat is 1 tile away. Pulse rate: always 116 BPM (every 0.517s). Amplitude: 0 when no opposite-layer threat within 8 tiles, interpolates to 0.8 at 1 tile distance. Mix sits entirely in sub-bass — do not EQ it higher. Player feels it more than hears it.

2. **Phase-switch snap (no nearby threat)** — `Tone.Synth({ oscillator: { type: "triangle" }, envelope: { attack: 0.001, decay: 0.08, sustain: 0, release: 0.22 } })` pitched to C6 (1046.5Hz), with `Tone.Reverb({ decay: 0.3, wet: 0.3 })`. Hard transient attack, very short decay. Feels like snapping a thin glass rod. Duration: 0.3s total.

3. **Phase-switch snap (opposite-layer threat within 4 tiles)** — Same as above but `Tone.Reverb({ decay: 0.6, wet: 0.55 })`. The reverb tail stretches and blooms, signaling to the player that something unseen was close. Duration: 0.6s. The extra wetness is the warning you should have heeded.

4. **Death** — `Tone.NoiseSynth({ noise: { type: "white" }, envelope: { attack: 0.001, decay: 0.18, sustain: 0, release: 0 } })` at amplitude 0.9, filtered through `Tone.Filter({ frequency: 3200, type: "lowpass" })`. Simultaneously, a descending glide: `Tone.Synth` pitched at G5 (784Hz), portamento 0.15s down to G3 (196Hz), envelope attack 0 / decay 0.25 / sustain 0 / release 0. Combined: a sharp noise hit with a dying-tone underneath. Very short, very final.

5. **Player movement (each tile step)** — `Tone.Synth({ oscillator: { type: "triangle" }, envelope: { attack: 0.001, decay: 0.04, sustain: 0, release: 0.02 } })` at amplitude 0.15, pitched to E5 (659Hz) in Layer A, D#5 (622Hz) in Layer B (the slight pitch drop of the other layer). One sound per tile arrival. Barely audible — it is textural, not noticeable, but its absence would be felt.

6. **Exit reached** — `Tone.PolySynth` playing C5-E5-G5-B5 simultaneously, each with `Tone.Synth({ oscillator: { type: "triangle" }, envelope: { attack: 0.01, decay: 0.8, sustain: 0.1, release: 1.2 } })` at amplitude 0.6. Followed immediately by `Tone.Reverb({ decay: 2.5, wet: 0.7 })`. The chord rings out and decays over ~2.5s. Clean, earned, final.

---

## Mechanic Spec

**Core loop:**
Navigate a grid-based isometric maze from start tile to exit tile. Walls and threats exist independently in Layer A and Layer B. The player occupies exactly one layer at all times. Phase-switching (toggling between layers) is the primary mechanic. Die if you share a tile with a threat in your current layer. Threats never move between layers — each threat is locked to one layer for the entire level.

**Primary input:**
- **Keyboard:** WASD or Arrow keys. Each keypress moves the player one tile in the cardinal direction (W=up-right in isometric, S=down-left, A=up-left, D=down-right). If the destination tile has a wall in the current layer, the player does not move (no slide, no bounce — hard stop with a 2-frame screen shake of 2px amplitude).
- **Pointer (click/tap):** pointerdown on any floor tile triggers pathfinding (A* on current layer's passable tiles) and moves the player along the path one tile per step at 120ms per tile. pointerdown on an impassable tile in current layer: no movement.
- **Phase-switch:** Spacebar (keyboard) or pointer double-tap anywhere on the canvas (two taps within 300ms). The switch is instantaneous — no charge, no wind-up.

**Phase-switch refractory period:** After switching, the player cannot switch again for 500ms. The 500ms refractory window is shown as a thin arc indicator beneath the player token — a 360° arc that fills over 500ms in the current layer color. This is the only UI element during gameplay.

**Player movement speed:** 1 tile per 120ms. 8.33 tiles/second max. Each step is a lerped slide between tile centers over the 120ms duration — the player token does not snap; it glides.

**Tile grid coordinate system:** Standard X/Y integer grid. (0,0) is top-left of the level. Isometric projection formula: `screenX = (x - y) * 24 + viewportCenterX`, `screenY = (x + y) * 12 + viewportOffsetY`. All game logic runs on integer grid coordinates. Rendering uses the projection formula.

**Threat behavior:**
- **Stationary threats:** Occupy a fixed tile permanently. Do not move.
- **Patrol threats:** Move along a predefined path (waypoint loop). Speed: configurable per level (see Level Design). At each waypoint, the threat pauses for 200ms before continuing. Path is defined as an ordered list of tile coordinates — threat follows them in order, loops infinitely.
- **Pursuer (Level 5 only):** Actively pathfinds toward the player's last known position using A* on its own layer. Recalculates path every 400ms. Speed: 1 tile per 280ms. Pursuer phase-shifts independently on its own timer (see Level 5).

**Win condition:** Player token enters the exit tile in any layer. The exit is accessible from both layers (it exists as a special tile that has no wall in either layer).

**Lose condition:** Player token occupies the same tile as any threat in the player's current layer on the same frame. Detection is tile-exact (integer grid coordinates match). Instant death — no grace period, no knockback, no i-frames.

**On death:** Respawn at the level start tile in Layer A. Death counter increments. Ghost remains visible for 15s. The refractory arc resets to full (player can switch immediately on respawn).

**Score system:**
- Base score per level clear: `1000` points.
- Time bonus: `500` points at level start, decreasing at `8` points per second. Minimum time bonus: `0`.
- Phase efficiency bonus: `+100` for each "reading" phase-switch — defined as a phase-switch that occurs within 0.4s of the player's trail-shadow reading the correct threat position (implementation: award the bonus if a threat in the destination layer was within 3 tiles at moment of switch AND the player survived the next 1.0s). This rewards proactive switches, not panic switches.
- Death penalty: `-50` per death, applied to the running total (can go negative within a level, minimum total score is 0).
- Level completion: score is locked. Cumulative across all 5 levels. Displayed on completion screen only.

**Difficulty curve summary:**
- L1: 1 threat (Layer B, stationary). Maze is simple. Purpose: die once into the invisible wall, understand the rule.
- L2: 3 threats (2 Layer B patrol, 1 Layer A stationary). First encounter with a Layer A threat — forces awareness that the layer you're in is also dangerous.
- L3: 5 threats (2 Layer B patrol, 2 Layer A patrol, 1 Layer B stationary). First level where the player must hold a two-layer mental map continuously.
- L4: 6 threats (3 Layer A, 3 Layer B, all patrol). The WOW MOMENT level. Trail-shadows are now essential, not optional.
- L5: 4 threats + 1 pursuer (3 standard, 1 pursuer that phase-shifts). The pursuer is the climax mechanic.

---

## Level Design

### Level 1: "First Contact"
**What's new:** The only mechanic introduced is death-in-the-invisible-layer. No Layer A threats exist. The maze layout is two corridors: one clear path in Layer A leading to a wall; one clear path requiring a single phase-switch into Layer B to pass one corridor, then back to Layer A for the exit. The player will attempt to proceed in Layer A, hit the invisible wall (Layer B wall), and die to the stationary threat waiting there. Second attempt: they see their death ghost, they switch before that tile, they proceed.
**Grid size:** 8×8
**Threats:**
- Layer B, Stationary, tile (4,3) — exactly one tile into the only corridor that requires a Layer B segment
**Maze layout:** Start at (0,7). Exit at (7,0). Single winding path, no branching. Exactly one section (tiles (3,3) to (5,3)) requires the player to be in Layer B to pass (Layer A has walls there). Layer B has no walls in that section.
**Trail-shadow intensity:** 12% opacity (standard) — present but players won't understand it yet
**Patrol speed:** N/A (stationary only)
**Target duration:** 30–60 seconds first clear
**Goal:** Teach the death rule with zero text in under 20 seconds of play

---

### Level 2: "Split Traffic"
**What's new:** First patrol threat. First Layer A threat (stationary) — the player learns the currently-visible layer also has threats.
**Grid size:** 10×10
**Threats:**
- Layer B, Patrol, path: `[(2,1),(2,5),(2,1)]`, speed: 1 tile per 400ms
- Layer B, Patrol, path: `[(6,2),(6,7),(6,2)]`, speed: 1 tile per 400ms
- Layer A, Stationary, tile (5,5)
**Maze layout:** Start at (0,9). Exit at (9,0). Two branching paths through the middle that both require at least two phase-switches. The Layer A stationary threat blocks the more direct route, forcing the player to route around it in Layer B — where patrols now exist.
**Trail-shadow intensity:** 12% opacity
**Patrol speed:** 1 tile per 400ms (slow — player has time to observe patrol timing)
**Target duration:** 60–120 seconds first clear
**Goal:** Establish the two-layer threat model; introduce the timing of patrol avoidance

---

### Level 3: "Dual Occupation"
**What's new:** Both layers have patrol threats. The player must maintain a live mental model of both layers simultaneously for the first time. Trail-shadows become non-optional for expert play.
**Grid size:** 12×12
**Threats:**
- Layer B, Patrol, path: `[(1,3),(8,3),(1,3)]`, speed: 1 tile per 320ms
- Layer B, Patrol, path: `[(5,1),(5,9),(5,1)]`, speed: 1 tile per 320ms
- Layer B, Stationary, tile (9,6)
- Layer A, Patrol, path: `[(3,5),(3,10),(3,5)]`, speed: 1 tile per 320ms
- Layer A, Patrol, path: `[(7,4),(10,4),(7,4)]`, speed: 1 tile per 320ms
**Maze layout:** Start at (0,11). Exit at (11,0). Three possible routes — one mostly in Layer A, one mostly in Layer B, one requiring 5+ switches. The all-Layer-A route and all-Layer-B route are both slower (more tiles) but theoretically safer for a 1x player who has learned one layer's layout. The 5-switch route is the fastest but requires dual-layer reading.
**Trail-shadow intensity:** 12% opacity (consistent — the mechanic is now expected to be understood)
**Patrol speed:** 1 tile per 320ms (20% faster than L2)
**Target duration:** 90–180 seconds first clear
**Goal:** Force the player to stop thinking "switch" and start building a depth-map of both layers

---

### Level 4: "Membrane Collapse"
**What's new:** THE WOW MOMENT. At exactly 45 seconds after level start, both layers become simultaneously visible for exactly 1.8 seconds. All geometry — Layer A in `#00E5FF`, Layer B in `#FF3366` — renders at full opacity simultaneously. All threats are visible in both layers. This happens once and never repeats in the level. The player sees the complete maze for the first and only time, and realizes the exit they have likely been circling is directly adjacent in the other layer.
**Grid size:** 14×14
**Threats:**
- Layer B, Patrol, path: `[(2,2),(10,2),(10,8),(2,8),(2,2)]` (perimeter loop), speed: 1 tile per 280ms
- Layer B, Patrol, path: `[(6,3),(6,10),(6,3)]`, speed: 1 tile per 280ms
- Layer B, Patrol, path: `[(10,5),(3,5),(3,11),(10,11),(10,5)]`, speed: 1 tile per 280ms
- Layer A, Patrol, path: `[(1,6),(12,6),(1,6)]`, speed: 1 tile per 280ms
- Layer A, Patrol, path: `[(5,2),(5,12),(5,2)]`, speed: 1 tile per 280ms
- Layer A, Patrol, path: `[(9,1),(9,13),(9,1)]`, speed: 1 tile per 280ms
**Maze layout:** Start at (0,13) in Layer A. Exit at (13,0) — accessible from both layers. The maze is deliberately labyrinthine — multiple dead ends in each layer, with corridors that are open in one layer and walled in the other. The exit is in the top-right quadrant. The natural exploration path loops players through the mid-left section twice before they realize the exit requires a specific Layer B approach.
**The membrane collapse visual:** At t=45s, a white flash (full screen, `#FFFFFF`, 100ms duration) followed by 1.8 seconds of dual-layer rendering. Both layer geometry renders simultaneously: Layer A geometry at `#00E5FF` 100% opacity, Layer B geometry at `#FF3366` 80% opacity (slightly behind, slightly ghostly). All threats visible in both layers. After 1.8s, a second white flash (100ms), then return to single-layer view (whichever layer the player is currently in). If the player dies during the 1.8s window, respawn rules apply normally.
**Audio during membrane collapse:** Music drops to 20% volume. A low, resonant drone (80Hz sine, amplitude 0.4) rises over 0.3s, sustains for 1.2s, then fades over 0.3s as the second flash occurs.
**Patrol speed:** 1 tile per 280ms (fastest standard speed)
**Target duration:** 120–240 seconds first clear
**Goal:** Deliver the wow moment; reward players who have been building a mental map

---

### Level 5: "Two Clocks"
**What's new:** The pursuer. A threat that actively follows the player AND phase-shifts on its own independent timer. The pursuer's phase-shift timer starts at 3.0 seconds per shift, decreasing by 0.2s for every 30 seconds of level elapsed, to a minimum of 1.0 second per shift. The player must outpace the pursuer's rhythm while maintaining their own.
**Grid size:** 12×12
**Threats:**
- Layer B, Stationary, tile (5,3)
- Layer A, Patrol, path: `[(2,6),(9,6),(2,6)]`, speed: 1 tile per 260ms
- Layer A, Stationary, tile (8,8)
- **Pursuer:** Starts in Layer A at tile (11,11). Moves at 1 tile per 280ms. Uses A* pathfinding on its current layer toward player's current tile (recalculates every 400ms). Phase-shifts on independent timer starting at 3.0s, decreasing as described above. When pursuer phase-shifts, plays a muffled version of the phase-switch snap SFX at 40% volume — the player can *hear* the pursuer shift.
**Pursuer visual:** Same isometric diamond as other threats, but `#FFFFFF` filled with an outer glow that alternates between `#00E5FF` and `#FF3366` based on its current layer — this makes it visually distinct from stationary/patrol threats.
**Maze layout:** Start at (0,11). Exit at (11,0). The maze has one clear fast route that requires 4 phase-switches. The pursuer starts far enough away (maximum diagonal distance) to give the player 15–20 seconds before it becomes a serious threat. There are two "dead zones" — tiles where a player in Layer A is safe from the pursuer (pursuer cannot reach them without switching into a loop) — a skilled player can identify and exploit these.
**Safe window mechanic:** At level start, the phase-switch safe window (the time during which both the player and pursuer are in different layers) is approximately 3.0s. At 60s elapsed, it shrinks to approximately 1.6s. At 90s elapsed, approximately 1.0s. The level is designed to be clearable in ~75 seconds by a skilled player — the final 15 seconds are played at 1.0s windows.
**Patrol speed:** 1 tile per 260ms (fastest in game)
**Target duration:** 90–150 seconds for skilled players; first clear may take 5–10 deaths
**Goal:** The climax. Two timers. One body. Three seconds shrinking to one.

---

## The Moment

On Level 4, at exactly 45 seconds, everything you have been building in your head — the approximate position of the Layer B corridor, the rough location of the exit, the threat you keep sensing through the audio — snaps into sudden terrible clarity: both layers are simultaneously visible for 1.8 seconds, and the exit you have been circling for two minutes is directly behind you, one layer away, three tiles.

---

## Emotional Arc

**First 30 seconds:**
You move. You hit something invisible. You die. No text appears. You see your ghost on the floor and understand: the floor has depth. The rule is total. On your second life you move more carefully — not because you know more, but because you know something exists that you cannot see. Confusion lasts exactly one life.

**After 2 minutes:**
The audio heartbeat is no longer alarming — it is a compass. You have stopped asking "should I switch?" and started asking "where are they?" The two maps are collapsing into one spatial model in your head. A threat trail bleeds through the floor at 12% opacity and you already know which direction it's moving before you consciously register seeing it. You are moving through both worlds simultaneously. This is the groove.

**Near the win (Level 5, final 30 seconds):**
The pursuer is 4 tiles behind you. Its phase-shift timer has dropped to 1.2 seconds. Your phase-switch refractory is 500ms. The math is tight but possible — you have one route left. Every switch now has a sound that either confirms you escaped or confirms you didn't. You are running on read-ahead. The exit is 6 tiles away. Two layers. One body. You are the signal ghost, and you are almost free.

---

## This game's identity in one line

This is the game where you learn to see a room you cannot look at.

---

## Start Screen

**Idle animation (game-world specific):**
The start screen renders a miniaturized version of the Level 1 maze (8×8 isometric grid), centered in the lower 60% of the screen. The grid tiles slowly breathe — Layer A tiles pulse `#00E5FF` from 6% to 14% fill opacity and back, period 2.4s, sine easing. Layer B tile-shadows bleed through at `#FF3366` at 8–12% opacity, on the same grid but offset by half a period (they pulse out of phase with Layer A: when A is bright, B is dim, and vice versa).

A single ghost player token — player silhouette, `#FFFFFF` at 70% opacity — traces a looping path through the idle maze: start tile → one branch → dead end in Layer A (tile hits invisible wall, token shakes 2px for 0.1s) → phase-switches (membrane ring expands, color shifts from `#00E5FF` to `#FF3366`) → continues in Layer B → loops back to start. Full loop duration: 6.0 seconds. Repeat indefinitely. Trail-shadows follow the ghost at 8% opacity, fading over 1.5s.

At the stationary threat position (tile (4,3)), a `#FF3366` threat token pulses slowly at 3.0s period, amplitude 60%–100% opacity. It does not move. It waits.

The entire idle scene is rendered at 60% scale relative to gameplay. No UI chrome during idle — only the game world.

**SVG Overlay:**

**Option A — Title glow (required):**
```svg
<svg xmlns="http://www.w3.org/2000/svg" width="400" height="100" viewBox="0 0 400 100">
  <defs>
    <filter id="glow" x="-20%" y="-20%" width="140%" height="140%">
      <feGaussianBlur stdDeviation="4" result="blur" />
      <feComposite in="SourceGraphic" in2="blur" operator="over" />
    </filter>
    <style>
      .title {
        font-family: 'Courier New', monospace;
        font-size: 48px;
        font-weight: 700;
        fill: #00E5FF;
        letter-spacing: 0.15em;
        filter: url(#glow);
        animation: fadeIn 1.2s ease-out forwards;
        opacity: 0;
      }
      @keyframes fadeIn {
        0% { opacity: 0; transform: translateY(8px); }
        100% { opacity: 1; transform: translateY(0); }
      }
    </style>
  </defs>
  <text class="title" x="50%" y="64" text-anchor="middle">PHASE DRIFT</text>
</svg>
```
Position: horizontally centered, vertically at 20% of screen height. The glow filter creates a `#00E5FF` bloom halo around the letterforms. The fadeIn animation runs once on page load.

**Option B — Ghost silhouette (80×80px, above title):**
An isometric diamond silhouette representing the player token — 80×80px SVG positioned directly above the title text (bottom edge of SVG = top edge of title, centered horizontally). The diamond is the player token shape: top isometric face only, filled `#00E5FF` at 60% opacity, with a feGaussianBlur glow (stdDeviation 3) in `#00E5FF`. The silhouette pulses opacity between 60% and 100%, period 2.4s, matching the idle grid breath.
```svg
<svg xmlns="http://www.w3.org/2000/svg" width="80" height="80" viewBox="0 0 80 80">
  <defs>
    <filter id="silhouetteGlow">
      <feGaussianBlur stdDeviation="3" result="blur" />
      <feComposite in="SourceGraphic" in2="blur" operator="over" />
    </filter>
    <style>
      .ghost { animation: breathe 2.4s ease-in-out infinite; }
      @keyframes breathe {
        0%, 100% { opacity: 0.6; }
        50% { opacity: 1.0; }
      }
    </style>
  </defs>
  <!-- Isometric diamond: top face of a cube token -->
  <polygon class="ghost"
    points="40,12 68,26 40,40 12,26"
    fill="#00E5FF"
    filter="url(#silhouetteGlow)" />
</svg>
```

**Option C — Decorative border (stroke-dashoffset animation):**
A thin rectangular border (2px stroke, `#00E5FF` at 40% opacity) surrounding the viewport edges with 24px inset on all sides. The border is drawn via `stroke-dashoffset` animation: total dash length = full perimeter, animates from full-offset (invisible) to 0 (fully drawn) over 2.0s on page load, easing `ease-out`. After draw completes, the border pulses opacity between 40% and 20% at period 4.0s.
```svg
<!-- Positioned as a fixed overlay, full viewport dimensions (e.g. 800×600) -->
<rect x="24" y="24" width="752" height="552" rx="2"
  fill="none" stroke="#00E5FF" stroke-width="2" stroke-opacity="0.4"
  stroke-dasharray="2608" stroke-dashoffset="2608">
  <animate attributeName="stroke-dashoffset"
    from="2608" to="0" dur="2s" easing="ease-out" fill="freeze" begin="0.3s" />
</rect>
```

**"Press Space or tap to begin" prompt:** `#FFFFFF` at 50% opacity, monospace font, 14px, letter-spacing 0.1em, positioned at 75% of screen height, centered. Blinks at 1.2s period (opacity 50% → 15% → 50%), sine easing. Appears 1.5s after page load (delays until title fadeIn completes).

---

## Implementation Notes (not design, but critical for programmer clarity)

**Layer state variable:** `currentLayer`: integer, `0` = Layer A, `1` = Layer B.
**Phase-switch:** Toggle `currentLayer`. Fire SFX #2 or #3 depending on proximity. Start refractory timer (500ms). Emit membrane ring visual.
**Threat-player collision:** Check every frame after movement resolves. If `player.tile === threat.tile && threat.layer === currentLayer`: trigger death.
**Trail-shadow rendering:** All threats render in both layers. When `threat.layer !== currentLayer`, render at 12% opacity, no bloom, below all other geometry. When `threat.layer === currentLayer`, render at 100% with bloom.
**Membrane collapse (Level 4):** At `levelTimer >= 45.0s && !hasFlashed`: set `dualLayerVisible = true`, play drone SFX, start 1.8s timer. On timer end: set `dualLayerVisible = false`, play flash. During `dualLayerVisible`: render all geometry and threats for both layers simultaneously.
**Pursuer phase-shift (Level 5):** `pursuerShiftTimer` starts at 3.0s. Every 30s of `levelTimer`, subtract 0.2s (floor at 1.0s). When timer fires, toggle `pursuer.layer`, reset timer, play muffled snap SFX (phase-switch snap at 40% volume).
**A* pathfinding:** Pursuer uses A* on its current layer's passable tiles. Player click-to-move also uses A* on player's current layer. Diagonal movement is NOT allowed — 4-directional only.

---

END_DESIGN_DOC
