# Delivery notes — Pressure (v1.1.0 « Agency », 31 October 2026)

One of a batch of three prototypes (Accretion, Counterweight, Pressure)
requested directly by Cédric. LOB7 publication is intentionally **deferred
until Cédric has played this prototype**; this repository is the deliverable.

## What was actually tested

**Unit tests (Node, pure logic extracted from the shipped `index.html`) —
18/18 passing:**

- difficulty curves: colours 3→5 and intakes 1→3 by level, lead gated to
  level ≥3 (1/12), rainbow to level ≥4 (1/40), spawn interval 1.2 s → 0.35 s
  over 6 min, halved during surges;
- surge state machine (warn rumble → 5 s active);
- grouping (union-find): same colour touching groups, 4 px gap does not,
  rainbow joins and bridges two colours, lead never groups, rainbow-only
  groups are not tappable;
- pressure with pack factor (a wall-to-wall full jar reads ~100 %);
- scoring: 10×n², chain ×2/×3/… capped ×8;
- waves: force/range grow with size, rainbow doubles both exactly (×2.000);
  hits once per bubble; pushes away from the burst; kills close lead,
  spares far lead;
- physics: colour bubbles rise, lead sinks, nothing leaves the jar,
  velocities bounded; a 24-bubble overlapping blob separates (residual
  overlap < 4 px under constant buoyancy); a pressed one-colour stack forms
  a 5-group.

**Real browser session (Chromium/IAB, 480×820 and 1280×720), played through
the game's own input handlers:**

- home screen renders (checked by an external vision model: layout clean,
  two-tone title, all buttons present; its only finding — jar too faint —
  was fixed and the shipped file reloaded);
- tutorial: the pre-formed group of 3 stays a 3-group, "Tap this group!"
  hint shows, first tap fuses (90 = 10×3²), step 2's five amber bubbles
  fuse on their own after the demo wave (+250) and the tutorial completes;
- invalid tap (pair): plop, 0.5 s lock verified, valid taps blocked during
  the lock, working again after;
- alarm: a lead settling on the intake line triggers it, glass crack grows
  at the 2 s rate; fusing during the alarm cancels it and grants the +300
  cool bonus (score 90 + 300 = 390 observed); the alarm legitimately
  re-arms after the 1.2 s grace if the menace is still there; crack at
  100 % → shatter → game-over screen visible, score/best/chain/fusions
  correct, best persisted to localStorage, "New best!" shown;
- replay: measured 0.3 ms from click to a fresh running game (spec: < 1 s);
- rainbow: joins a two-bubble group (3 members, rainbow counted), wave
  power and range measured at exactly ×2;
- lead: destroyed by a wave, +50;
- level-up at the 12th fusion (banner shown), keyboard play (Tab selects
  the biggest group, arrows navigate, Space fuses), pause via P and resume
  via Escape, landscape resize keeps every bubble inside the jar;
- i18n: EN default, FR and ES switches verified in the DOM, choice
  persisted;
- physics cost measured in the page: 0.185 ms per 60 Hz step at 141
  bubbles (≈1 % of the frame budget); 60 FPS observed live on the debug
  overlay at low bubble counts.

## Known limits

- Full-game **session-length balancing** (first game between 1.5 and 4 min,
  natural ×2 cascades in most 3-minute games) is tuned on paper from the
  spawn/fuse rates but has not been measured over many full playthroughs —
  it needs human play.
- The **sound design** was implemented and exercised (no console errors) but
  not critically listened to end-to-end in this session.
- In the test session the in-app browser's screenshot/compositing stopped
  working partway (local tooling issue), so the evidence folder has one
  gameplay screenshot (home); the functional checks above are DOM/state
  assertions from the live page instead. A 120-bubble **live** FPS reading
  could not be captured for the same reason — the physics-side measurement
  (0.185 ms/step) plus cached-sprite rendering is the performance argument.
- Multi-touch gestures are not used; each tap is independent. The page has
  no offline/service-worker story (static single file, opens from disk).

## Chosen settings (all in the `SETTINGS` object at the top of the script)

Spawn 1.2 s → 0.35 s over 360 s; 3 colours at level 1, +1 every 2 levels
(max 5); 1 intake, +1 every 2 levels (max 3); lead 1/12 from level 3,
rainbow 1/40 from level 4; tap minimum 3, cascade minimum 5; wave
force = 430 + 30·r (px/s impulse), range = 190 + 9.5·r, both ×2 with a
rainbow; alarm crack over 2.0 s, decay 0.22/s, 1.2 s grace after a
cancelling fusion; level every 12 fusions; surge every ~90 s (first ~75 s),
2 s rumble warning, 5 s at 2× inflow; scoring 10·n², chains ×2/×3/… cap ×8,
lead +50, cool +300; pressure reads bubble area ÷ (play area × 0.82) so a
packed jar ≈ 100 %; physics: buoyancy 400 px/s² (scaled to jar height),
lead sink 88, damping 2.1, restitution 0.06; bubbles capped at 145.

## Palette

"Mediterranean pool" family, chosen from the workshop's art-direction bank
to differ from every other game of the day: cream paper `#faf3e3`, pool
blue `#37b3c8` / deep `#1d8aa3`, single accent sun orange `#f5820b`
(gauge, alarms, UI), danger red `#e5484d`, ink `#17323a`; five bubble
colours with distinct symbols (red circle, amber triangle, green square,
violet diamond, blue plus).

---

## v1.1.0 — Agency update (31 October 2026, same day)

Cédric's playtest verdict on v1.0.0: the game felt luck-driven and the win
condition was unclear. v1.1.0 answers with three levers and a win state:

1. **Choose the next colour** — a visible queue of 3 upcoming bubbles; tap a
   colour to bring it next. Colour generation biases 55 % toward colours
   already floating (groups form), lead/rainbow items are locked: they
   cannot be promoted and nothing can jump over them at the front.
2. **Aim the intake** — tap a mouth to arm it (orange glow); the next bubble
   enters there. Without player input the least-blocked mouth is picked.
3. **Stir** — a swipe sends a current (push + swirl) through the jar; 2
   charges to start, 3 max, one earned back per cascade ×2+.
4. **Win state** — each level has a fusion quota (12, +5/level, cap 45)
   shown as a progress bar in the HUD. Reaching it pops the whole jar
   bottom-to-top (+150 × level bonus) and starts the next, faster level.
   Game over now also reports the level reached.

**Verified for v1.1.0 (in addition to the v1.0.0 suite below):**
- Node 22/22: queue-item generation (bias bounds, specials gated by level),
  promote rules (colours yes; specials never; locked front blocks), level
  quota growth and cap, stir impulse direction/magnitude/swirl bounds.
- Real browser, real event paths: queue renders (3 chips), chip click
  promotes, lead at the front blocks promotion through the DOM handler,
  swipe (pointerdown→pointerup, 120 px) consumes a charge and spawns the
  current visual, armed mouth is used by the spawner (queue visibly
  shifts), tap (pointerup path) fuses a trio for 90, full level cycle:
  4 trios = quota 12 → drain animation → level 2 quota 17, HUD
  "Lv 2 · 0/17", chip "Level cleared! +150"; FR strings for every new key
  (À venir / Remous / Niveau atteint / new rule line).

**Known limits added:** balance of quotas vs. speed ramp tuned by feel on
single full runs, not across many playthroughs; mouth aiming matters only
from level 3 (second mouth) — the first two levels teach colour choice
first, by design.
