# CLAUDE.md

Guidance for working in this repo.

## What this is

A **single-file** mortgage-comparison tool for a specific home under contract: **201 Snowberry
Way, West Whiteland Twp, Chester County PA — $760,000, settling 8/12/2026**. Its job is to
compare lender rate sheets (rate / points / credits / fees) side by side and over time.
Everything lives in `index.html`: markup, CSS, and an inline `<script>`. There is **no build
step and no dependencies** — open `index.html` in a browser.

The pre-contract general affordability calculator (solvers, strategy table, explorer) lives in
git history if ever needed again.

## Ground truth

Closing-cost defaults come from the **KW Buyer's Estimated Closing Cost report (v4, 6/22/26)**
— a PDF kept in this directory but **gitignored (never commit it)**. The defaults reproduce it
exactly: at 6.9% / 20% down / 0 points the tool must show **$5,028.21/mo** and **$149,995.89
cash at settlement** ($182,470.89 total cash − $30,000 earnest − $2,475 POC). If you touch the
closing-cost math, re-verify those figures (see Testing below).

## Running / testing

- Open `index.html` in a browser, or `python3 -m http.server`.
- No test suite/CI, but the calc core is **pure and extractable**: everything between
  `// ==== CALC CORE` and `// ==== END CALC CORE` in the `<script>` has no DOM dependencies.
  To verify math, extract it to a file, append assertions (PDF reference numbers above), and
  run under `node`. Headless Chrome works for render checks:
  `"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless --screenshot=… file://…/index.html`.
- Inputs persist to `localStorage` under **`homecalc_v2`**. If state looks stale after a
  change, use the header's **"reset to defaults"** link (`resetSaved()`).

## Code map (all inside the `<script>` in `index.html`)

- **CALC CORE (pure, no DOM)** — tax constants (2026 MFJ), `pmt()`, `prepaidDays(dateStr)`
  (settlement day → month-end, inclusive; 8/12 → 20 days), `loanCalc(p,ln)` (the heart:
  amortization arrays `bal/cumInt/cumPmi` indexed 0..360, PMI until 78% of price, lender
  charges, cash-to-close waterfall), `costAt/outlayAt/metricAt` (chart metrics),
  `crossovers(fa,fb)`, `takeHome(p)` / `soloNet(...)`.
- **Loans state** — `loans` is an array of `{name,rate,pts,credits,fees,slot}`. `slot` is the
  **permanent color slot** (palette index): colors follow the loan, never its row position, so
  deleting a loan must not repaint survivors (`freeSlot()` assigns the lowest unused slot to
  new loans). Max 6 loans (palette size).
- **`I()`** — reads all *shared* inputs (purchase, ongoing costs, closing costs, budget) into
  `p`. Loan-card inputs are **not** in `I()`; they live in `loans`.
- **Loan cards** — `buildLoanCards()` rebuilds the card DOM **only on structural change**
  (add/remove/duplicate/state-load), never on keystrokes — rebuilding would drop input focus.
  Card inputs carry `data-f` attributes and their own listeners that mutate `loans` and call
  `renderOutputs()`. The global input listener intentionally excludes `[data-f]` elements.
- **`render()`** = `renderLabels()` (input-side value labels) + `renderOutputs()` (everything
  computed: card summaries, comparison table, chart, detail, budget, assumptions).
- **Chart** — hand-rolled SVG (`renderChart`), viewBox 960×430, styled per the dataviz skill:
  series palette `--s1..--s6` (validated for CVD/contrast against `#1e293b`), 2px lines,
  hairline solid gridlines, legend for ≥2 series, two-line end labels placed into 28px slots
  with leader lines when lines converge, crossover dots + break-even sentences (cost metrics
  only), crosshair tooltip on hover **and keyboard** (arrows/Home/End on the hit rect), and a
  milestone table as the chart's table view. `metric` global: `cost | outlay | balance`.
  “True cost” = interest + PMI + lender charges (principal is equity, not cost); escrows and
  seller reimbursements are cash-to-close items but *not* true cost.
- **Persistence & sharing** — `collectState()` = `{v:2, inputs, metric, loans}`;
  `applyState()` restores it (sanitizing loans and deduping slots). `localStorage` key
  `homecalc_v2`; share links pack the state into a base64 `#s=` hash (`loadFromURL()` applies
  non-destructively and strips the hash).

## Conventions & gotchas

- **Keep it single-file and dependency-free.** No build step, framework, or external libs.
- **PDF-exact defaults.** Don't "round off" the odd-looking defaults ($5,239.66 title,
  $3,456.63 escrows…) — they're real line items and the to-the-penny match is the point.
- **Per-loan vs shared split is intentional.** Anything that varies by lender (rate, points,
  credits, fees → prepaid interest, lender charges) is on the loan card; everything else
  (title, transfer tax, escrows, reimbursements, tax/insurance/HOA) is shared, so loan
  differences in the table/chart are purely lender pricing.
- **No LLPA table.** Rate sheets already embed LLPAs in their pricing; don't reintroduce it.
- **Colors are load-bearing.** The `--s1..--s6` ordering was validated with the dataviz
  skill's palette validator against the panel surface; don't reorder or eyeball-replace hues.
- **Wide tables need `minmax(0,1fr)`.** The layout grids use `minmax(0,1fr)` so `.tblwrap`
  can scroll horizontally; a bare `1fr` lets intrinsic table width blow out the page.
- **Conservative take-home.** Standard deduction only (no itemized mortgage interest/SALT).
- When you change a default or assumption, update the in-app **Assumptions & sources** block
  (`renderAssump`) and the `README.md` defaults table so they stay in sync.
