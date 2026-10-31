# Pressure

Bubbles rise in a sealed jar and pile up under the lid. Tap a group of 3 or
more same-colour bubbles: they fuse into one big bubble that races up and
bursts against the lid. The shockwave shoves the jar around and can press
same-colour bubbles into groups of 5+ that fuse on their own — chain
reactions. If the jar fills to the gauge's 100%, or a bubble reaches the
intake line at the bottom, the glass starts to crack: you have two seconds
to land a fusion and relieve the pressure, or the jar bursts.

A tiny, self-contained arcade puzzle: one file, no dependencies, no server.
Works with mouse, touch and keyboard, in English, French and Spanish.

Play: open `index.html`, or visit the GitHub Pages URL of this repository.

## Rules in one minute

- Bubbles float **up** and stack under the lid. New bubbles enter through
  intakes at the bottom, faster and faster (1.2 s apart at the start, 0.35 s
  after ~6 minutes).
- **Tap / click a group of 3+ same-colour bubbles** to fuse them. The fused
  bubble's area is the sum of its members'; it flies to the lid and bursts.
- The **shockwave** pushes nearby bubbles away. Its force and range grow with
  the size of the burst. A wave that presses 5+ same-colour bubbles together
  fuses them automatically: that's a **cascade**, worth ×2, ×3, ×4… on the
  chain.
- **Lead** bubbles (from level 3, grey with a "no" sign) never fuse and sink
  slowly. Only a shockwave destroys them (+50 each). If one settles on the
  intake line, the alarm starts.
- **Rainbow** bubbles (from level 4, rare) join any group they touch — even
  bridging two colours — and double the force and range of the wave.
- Tapping an isolated bubble or a pair just goes *plop* and locks input for
  0.5 s.
- Every ~90 s a **pressure surge** doubles the inflow for 5 seconds. A
  rumble warns you 2 seconds before.
- **Defeat**: pressure gauge at 100%, or any bubble on the intake line. The
  glass cracks for 2 s — fuse something in time to cancel the alarm (+300
  *cool* bonus if you do it during the alarm).

## Scoring

| Event | Points |
|---|---|
| Fusion of *n* bubbles | 10 × n² |
| Cascade step (same chain) | ×2, ×3, ×4… (cap ×8) |
| Lead destroyed by a wave | +50 |
| Fusion during the alarm (cool) | +300 |

Best score is saved locally (localStorage, wrapped in try/catch).

## Controls

| Input | Action |
|---|---|
| Mouse / touch | tap a group (generous hit zone) |
| Arrow keys / Tab | select the nearest (or biggest) group |
| Space / Enter | fuse the selection |
| P / Esc | pause (auto-pause when the tab loses focus) |
| M | mute |

Hovering or selecting highlights the whole group and shows its size.
A **Reduce motion** option (home and pause screens) disables screen shake
and slow-motion; it defaults to on if your OS prefers reduced motion.

Every colour also carries a symbol at the centre of the bubble (circle,
triangle, square, diamond, plus) for colour-blind players.

## Development

- Single self-contained `index.html` (Canvas 2D + Web Audio, everything
  inline, no build step, no external resource, ~79 KB).
- Fixed-timestep simulation (60 Hz), decoupled from rendering; bubbles are
  pre-rendered sprites so ~120+ bubbles stay cheap (measured ≈0.19 ms of
  physics per step at 141 bubbles).
- All sounds are synthesized at runtime with Web Audio — no audio files.
- Append `?debug=1` to the URL to display FPS, bubble count, pressure and
  level.
- The unit-tested core (grouping, physics, waves, scoring, difficulty
  curves) lives between `LOGIC-START` / `LOGIC-END` markers in the source
  and runs under Node without a browser (tests kept outside this repo).

## Credits & licence

- Concept: Cédric Bernadac, with Claude
- Game made by Z-Code
- Visuals and sounds generated for this game; MIT licence (see `LICENSE`).

Delivery notes: see `DELIVERY.md`. LOB7 manifest: `LOB7-GAME.json`.
