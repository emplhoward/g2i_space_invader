# Space Attack

## Structure

`index.html` is the entire game: semantic HTML menus and score bar, inline CSS,
and plain JavaScript drawing into a 2D canvas. It has no libraries, downloads,
external assets, build step, or server requirement. All ships, stars, bullets,
and explosion particles are drawn procedurally.

The commented JavaScript sections cover world setup, run lifecycle, wave logic,
collisions, rendering, input, and the animation loop. `game` holds the state,
score, wave, lives, entity arrays, and timers; `player` holds position and weapon
and invulnerability timers. States are `menu`, `playing`, `paused`, and `over`.
HTML buttons remain keyboard accessible, with visible focus indicators.

The simulation uses a 960 × 640 world and fixed 1/120-second updates driven by
`requestAnimationFrame`. Long frame gaps are capped at 0.1 seconds. CSS fits the
arena to the window; the backing canvas accounts for pixel density (up to 2×).
Narrow layouts contain the canvas without stretching ships. Resizing does not
change gameplay positions. The starfield animates independently of the paused
simulation.

## Gameplay

- A new run begins with three lives, zero score, and the selected starting wave:
  Normal = 1, Medium = 5, Hard = 10. Restart remembers this selection.
- Each wave contains exactly `10 + 5 × wave` enemies: 15, 35, and 60 at the three
  starting difficulties. Enemies arrive from above and settle into a moving
  formation. Timers select enemies to fire aimed bullets and dive toward the
  player's current position. Divers that leave the bottom return from above.
- Each higher wave increases formation movement, enemy bullet speed, and dive
  speed, and reduces the intervals between shots and dives. `difficulty()`
  centralizes the tuning. Later waves always advance by one.
- Holding Space fires every 0.115 seconds. Normal bullet kills award 100 points;
  kills during a dive award 150. Clearing a wave awards `250 × wave` once,
  clears hostile bullets, and starts the next wave after two seconds.
- Enemy bullets and direct ship collisions remove one life. A hit clears enemy
  bullets and grants two seconds of flashing, shielded invulnerability. Direct
  ship collisions destroy the enemy without awarding kill points. New runs
  grant 1.5 seconds of initial protection.
- Bullet collisions use the segment traveled during the update, preventing
  fast bullets from passing through a ship between frames. Ship collisions use
  circular hit regions. Movement is normalized diagonally and bounded by the
  arena.
- Losing the third life shows the final score and reached wave. Pausing freezes
  movement, damage, shots, particles, wave advancement, and gameplay timers.
  Returning to the main menu ends the current run and allows a new difficulty.

The HUD shows score, personal best, wave, and lives. Best score uses the
`space-attack-best` localStorage key and is saved on score increases, game over,
return to menu, and page exit. Storage failures are caught so play still works.
Persistence depends on browser permission and its local-file storage policy;
moving the file or switching browser profiles may give it a separate best score.

## Controls

| Input | Action |
| --- | --- |
| Arrow keys or WASD | Move in any direction |
| Hold Space | Repeatedly fire |
| Enter at main menu | Start wave 1 (focused buttons also support Enter) |
| Start Medium / Start Hard | Start wave 5 / 10 |
| P or Escape | Pause or resume |
| HUD pause button / Resume | Pause / resume |
| Window blur or hidden tab | Pause automatically; resume explicitly |
| Main Menu | End run, retain best, choose difficulty again |
| R or Try Again at game over | Restart the originally selected starting wave |

Held keys are cleared at state changes and focus loss to avoid stuck movement.
A compact controls reminder remains below the arena during play. Gameplay
requires a keyboard; touch steering is not implemented.

## Verification

Verified on 2026-10-02 using headless Google Chrome 154, opening the actual
`file:///home/leo/Documents/github/g2i_space_invader/index.html` directly.
A temporary Python standard-library harness used Chrome DevTools Protocol;
it is a development check, not a game dependency. Controlled scenarios ran the
actual JavaScript simulation inside Chrome to make collisions and wave-clear
boundaries deterministic. Native browser keyboard input exercised Enter,
held movement and firing in the live animation loop, and P to pause.

The browser suite passed checks covering movement and bounds, held fire,
enemy entrance/shooting/diving/re-entry, both kill scores, projectile and ship
collisions, invulnerability and expiry, all three lives, final score, one-time
wave bonuses, progression from waves 1/5/10, difficulty scaling, pause freeze,
P/Escape and buttons, focus loss and cleared inputs, main menu, R/Try Again,
best-score persistence after reload, fast-projectile collision, responsive
layouts, and no uncaught JavaScript exceptions. Layout checks covered
390 × 844, 800 × 600, 1366 × 768, and 1920 × 1080, including the visible controls
reminder. Menu, gameplay, pause, and narrow-screen
captures were inspected visually; the narrow-screen menu clipping found during
review was corrected and the suite rerun.

To manually verify:

1. Open the HTML file and press Enter. Confirm wave 1, zero score, and 3 lives.
2. Move with both control schemes and hold Space. Watch kills add 100 or 150;
   clear a wave and check the `250 × wave` bonus and next wave.
3. Let bullets or ships hit you. Confirm one lost life, a brief flashing shield,
   and game over after three separate unprotected hits.
4. Pause using P, Escape, and the HUD button; confirm the fight freezes. Switch
   to another tab and return; the game should remain paused until resumed.
5. Choose Main Menu, then Medium or Hard. Check fresh lives/score and starting
   waves 5/10. Lose, then use R or Try Again to repeat the chosen difficulty.
6. Reload to confirm best-score persistence. Resize the window and check the
   battlefield, HUD, controls reminder, and menu buttons remain usable.
