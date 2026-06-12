# fusion — Fusion + Temperature Digital Model

Standalone single-file sim deployed to `ghostoutfit.github.io/fusion/`.
Derived from the canonical `v14/index.html` in the `protons` repo.
Vanilla HTML + CSS + JS only. No build step — edit `index.html` directly.

To run locally: `python3 -m http.server` in this directory, then open `localhost:8000/`.

## Differences from canonical v14

- **Defaults to Temperature tab** on load (unless `#fusion` hash is present)
- Color picker (`#bgColorPicker`) moved into `#v14ColorRow` floating panel
- `body.screenshot-mode #themeLight { display: none }` added
- Image paths use `images/` (no `../images/` parent prefix)

## Hash routing

| Hash | Effect |
|------|--------|
| `#fusion` | Opens Fusion tab |
| anything else (including no hash) | Opens Temperature tab |

`applyHash()` bound to both init and `hashchange`.

## Tabs

- **Temperature** (default) — 20 protons in a rounded-rect container, short-range 1/r⁴ repulsion, thermostat slider, slow-motion slider.
- **Fusion** — two clusters approach along the x axis; combo selector wheels; bar chart; energy-vs-time graph.

`switchTab(name)` is the single funnel. It cancels both `animId` and `tempRAF`, resets state, swaps control panels, and dispatches to `initTempProtons() + drawTemp()` or `setupScenario() + draw()`.

## Speed slider

`#simSpeedSlider` — integer steps 1–10, default **7** (`SIMSPEED_DEFAULT`).
Blue tick mark at default position; snaps within ±1 step (`SIMSPEED_SNAP = 1`).

## Physics constants

```javascript
const PARTICLE_RADIUS     = 10;
const SUBSTEPS            = 20;
const ZOOM_SCALE          = 1.5;
const strongForceStrength = 4;      // constant, no slider
const strongForceRange    = 2.0;
let   coulombStrength     = 400;
const COULOMB_CUTOFF_SQ   = (PARTICLE_RADIUS * 40) ** 2;
const VIEW_OFFSET_X       = 100;    // world units; × ZOOM_SCALE ≈ 150px left bias
```

Temperature tab:
```javascript
const TEMP_N        = 20;
const TEMP_R        = 24;       // visual radius (px)
const TEMP_SUBSTEPS = 4;
let   tempCoulomb   = 5e6;      // F = tempCoulomb / r⁴
```

## History ring buffer (fusion tab)

```javascript
const MAX_HISTORY = 4000;
phGet(i) / phLen() / phReset() / phPush(fr)
```

All former `particleHistory.xxx` call sites use these helpers.

## Cluster types and COMBO_TABLE

Clusters: `p`, `n`, `d`, `t`, `he`. `leftChoice` / `rightChoice` pick the pair.

Four behavioral categories:
- **Cat 1** — bouncy single-particles
- **Cat 2** — productive fusion (`d+t`, `d+d`, `t+t`) — large KE out
- **Cat 3** — gamma capture (`p+p`, `n+n`, etc.)
- **Coulomb-only** — scatter only (`p+he`, `he+he`, etc.)

## Scaled energy mode

`scaledEnergy` toggle (checkbox `#scaledEnergyToggle`). Shows keV labels on bar chart. Disabled (`simHasStarted = true`) once Go is pressed; re-enabled on reset.

## Key panels

- `#fusionControls` — shown on Fusion tab
- `#tempControls` — shown on Temperature tab
- `#v14ColorRow` — floating theme/color row, visible on **both** tabs; contains theme buttons, `#bgColorPicker`, and bug report button

## Images

```
images/
  favicon.png / favicon.svg
  Turtle.png / Rabbit.png / Stopwatch.png
  logo-placeholder.png
```

## Deployment

GitHub Pages from `main` branch root. Push to `origin` to deploy.
Remote: `https://github.com/ghostoutfit/fusion.git`

## Cross-sim search

```bash
# Compare with canonical v14
grep -n "functionName" index.html /path/to/protons/v14/index.html
```

Changes to canonical `v14/index.html` in the protons repo should generally be ported here. They are not auto-synced.
