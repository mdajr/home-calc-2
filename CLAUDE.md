# CLAUDE.md

Guidance for working in this repo.

## What this is

A **single-file** home-affordability calculator for Chester County, PA (Downingtown / Exton /
West Chester — WCASD & DASD school districts), tuned for married-filing-jointly households that
max out 401(k) + HSA. Everything lives in `index.html`: markup, CSS, and an inline `<script>`.
There is **no build step and no dependencies** — open `index.html` in a browser.

## Running / testing

- Open `index.html` directly in a browser, or serve it: `python3 -m http.server` then visit the page.
- There is no test suite, linter, or CI. "Testing" means opening the page and exercising the inputs.
- Inputs persist to `localStorage` under the key `homecalc_v1`. If state looks stale after a change,
  use the **"reset to defaults"** link in the header (calls `resetSaved()` → clears the key + reloads).

## Code map (all inside the `<script>` in `index.html`)

- **Constants (2026 tax year)** — `STD_DED`, `SS_WAGE_BASE`, `MAX_401K`, `HSA_FAMILY`, `FED` brackets,
  etc. Update these when tax figures change for a new year.
- **`I()`** — reads every input into one params object `p`. Utilities auto-estimate when the field is
  blank (`250 + 0.16*sqft` ≈ $700/mo at 2,800 sqft).
- **`takeHome(p)`** — 2026 MFJ take-home: federal brackets, FICA (SS cap + Medicare surtax), PA 3.07%,
  local EIT. Subtracts 401(k)×earners + HSA as non-spendable. Returns combined **and** per-person net.
  `soloNet(p,e,contribute)` models a single-income survivor case (optionally halting 401k/HSA).
- **`scenario(p,price,downFrac)`** — monthly cost for a home. Splits **`core`** (HOUSING: P&I, tax,
  insurance, PMI, HOA) from **`homeLiving`** (utilities + maintenance, treated as living expenses that
  scale with the home). PMI applies when LTV > 80%. **`cashToClose`** = down + closing + buyer's agent
  fee + discount points + **lease overlap** (`leaseNotice × leaseRent` — rent + utilities still owed
  through a month-to-month lease's notice period after closing).
- **`efFor(p,s)`** — emergency fund = N months of (housing payment + utilities) + fixed buffer.
- **Solvers** — `maxForDownFrac` (binary search; jointly respects monthly budget and cash-after-EF),
  `maxAllCash` (iterates because down ↔ EF are mutually dependent), `optimize` (sweeps 20–100% down for
  max price). Housing budget = `netMo − expenses − debt − cushion`. The strategy table keeps a **20%
  minimum** (no PMI); the explorer is where sub-20% scenarios are explored manually.
- **`render()`** — single function that recomputes everything and repaints the whole page (take-home
  summary, strategy table, explorer, points analysis, assumptions).
- **`renderPoints()`** — discount-points buy-down table (cost / monthly savings / break-even).
- **Wiring** — `pair(numId,rangeId)` keeps a number input and its range slider in sync; the number is
  the source of truth, so typing a value outside the slider's min/max still works (the slider just
  clamps visually). A global `input` listener calls `render()` + `saveState()` on every change.
- **Persistence & sharing** — `collectState()` snapshots all `input`/`select` values; `applyState()`
  writes a snapshot back (without firing input events). `saveState()`/`loadState()` use these against
  `localStorage` (key `homecalc_v1`). For share links, `encodeState`/`decodeState` pack a snapshot into
  a UTF-8-safe base64 URL hash (`#s=...`); `copyShare()` copies `shareURL()` to the clipboard, and
  `loadFromURL()` (run before the first `render()`, preferred over `loadState()`) applies a `#s=`
  scenario and strips the hash. Loading a link is **non-destructive** — it does not overwrite the
  visitor's saved `localStorage`; their first edit (via the global `input` listener) becomes the new
  working state.

## Conventions & gotchas

- **One render path.** All UI updates go through `render()` — there's no incremental DOM updating.
  After any programmatic input change, call `render()` (and `saveState()` if it won't fire an `input`
  event — see `loadStrat`).
- **Housing vs. living split is intentional.** Utilities & maintenance are *living* expenses, not
  *housing*, so "% of income on housing" reflects the mortgage payment only. Keep this distinction.
- **Property tax has no presets.** It's entered directly as an effective % of price via a slider
  (1–2%) with manual entry allowed outside that range. Don't reintroduce a township→rate lookup table.
- **Conservative by design.** Take-home uses the standard deduction (no itemized mortgage-interest /
  SALT), so results lean low. Note this when touching the tax math.
- **Keep it single-file and dependency-free.** Don't add a build step, framework, or external libs.
- When you change an assumption or constant, also update the in-app **Assumptions & sources** block
  (`#assump` in `render()`) and the `README.md` assumptions table so they stay in sync.
