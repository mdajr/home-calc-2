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

Shared-cost defaults (tax, insurance, HOA, title, transfer tax, escrows, reimbursements) come
from the **KW Buyer's Estimated Closing Cost report (v4, 6/22/26)** — the *realtor's
worksheet*, not a lender quote, so it is never a loan card. Lender quotes (US Financial/UWM
Loan Estimate, Chase rate-menu screenshot) seed the loan cards. All of these are PDFs/images
kept in this directory but **gitignored (never commit them)**. Math cross-check: at the KW
worksheet's illustrative 6.9% / 20% down / 0 points, the tool must show **$5,028.21/mo** and
**$149,995.89 cash at settlement** ($182,470.89 total cash − $30,000 earnest − $2,475 POC).
If you touch the closing-cost math, re-verify those figures (see Testing below).

## Workflow

**Always commit and push to `main`** when a change is done and verified — no need to ask.
Never commit the gitignored source documents (PDFs, screenshots).

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
  amortization arrays `bal/cumInt/cumPmi/cumCostD` indexed 0..360 — `cumCostD` is interest+PMI
  discounted at the cash-yield rate `p.disc` (opportunity cost of upfront cash; 0 reduces it
  to nominal) — PMI until 78% of price, lender charges, cash-to-close waterfall),
  `costAt/outlayAt/metricAt` (chart metrics; `costAt` = lender charges at face value +
  `cumCostD`, i.e. present value; outlay is deliberately nominal),
  `baselineIdx(lcs)` / `breakEvenMonth(lcBase,lc)` (the cheapest-upfront loan is the
  break-even baseline; break-even = first month a loan's cumulative true cost drops below
  the baseline's, i.e. when its points/fee premium is repaid — refi before then loses it),
  `takeHome(p)` / `soloNet(...)`.
- **Loans state** — `loans` is an array of
  `{name,rate,pts,credits,fees,escrow,quotedPI,quoted,goodThru,slot}`.
  `pts` (a % of loan) is canonical; the card's **Points $ box is a synced mirror** (editing
  either recomputes the other via `loanAmount(p)`; the $ mirror also refreshes on price/down
  changes, skipping whichever input has focus). `escrow` (nullable) is that lender's own
  LE Section-G escrow demand, overriding the shared `p.escrow` in cash-to-close only.
  `quotedPI` (nullable) is the sheet's quoted P&I, used purely as a transcription check
  (✓/⚠ badge on the card, ±$1 tolerance). `quoted`/`goodThru` (nullable `YYYY-MM-DD` strings)
  drive the card's freshness/lock badges: amber when the quote is ≥3 days old, red when
  `goodThru` < settlement. The US Financial LE is **not rate-locked** (page 1 Rate Lock: NO) —
  its `goodThru` is the LE's cost-expiration 7/23/26; Chase menus price a 45-day lock
  (`goodThru` = quote date + 45). `slot` is the **permanent color slot** (palette
  index): colors follow the loan, never its row position, so deleting a loan must not repaint
  survivors (`freeSlot()` assigns the lowest unused slot to new loans). Max 6 loans (palette
  size). Defaults are the actual lender quotes in hand (US Financial/UWM LE; all five Chase
  rate-menu rows with points $ derived from menu fee deltas vs the 6.625% baseline row) — the
  KW worksheet is not one of them.
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
  with leader lines when lines converge, per-loan break-even dots vs the baseline (cost
  metrics only; the table's Break-even column carries the exact dates — there is deliberately
  no pairwise-crossover list), crosshair tooltip on hover **and keyboard** (arrows/Home/End on
  the hit rect), and a milestone table as the chart's table view. `metric` global:
  `cost | outlay | balance`.
  “True cost” = interest + PMI + lender charges, future interest/PMI discounted at the
  cash-yield input (principal is equity, not cost); escrows and seller reimbursements are
  cash-to-close items but *not* true cost. The table's 10-yr column shows “+$X credit to tie”
  on non-best loans — the extra lender credit that would match the 10-yr best (negotiation ask).
- **Persistence & sharing** — `collectState()` = `{v:3, inputs, metric, loans}`;
  `applyState()` restores it (sanitizing loans and deduping slots; states saved with `v<3`
  get their income/removed-spending input overrides dropped so current defaults apply).
  `localStorage` key
  `homecalc_v2`; share links pack the state into a base64 `#s=` hash (`loadFromURL()` applies
  non-destructively and strips the hash).

## Conventions & gotchas

- **Keep it single-file and dependency-free.** No build step, framework, or external libs.
- **PDF-exact defaults.** Don't "round off" the odd-looking defaults ($5,239.66 title,
  $3,456.63 escrows…) — they're real line items and the to-the-penny match is the point.
- **Per-loan vs shared split is intentional.** Anything that varies by lender (rate, points,
  credits, fees → prepaid interest, lender charges, and optionally the Section-G escrow) is on
  the loan card; everything else (title, transfer tax, reimbursements, tax/insurance/HOA) is
  shared, so loan differences in the table/chart are purely lender pricing. The in-app
  "Where to find these numbers" guide maps LE sections A/B/G/J and Chase-style rate menus onto
  the card fields — keep it accurate if fields change.
- **No LLPA table.** Rate sheets already embed LLPAs in their pricing; don't reintroduce it.
- **Colors are load-bearing.** The `--s1..--s6` ordering was validated with the dataviz
  skill's palette validator against the panel surface; don't reorder or eyeball-replace hues.
- **Wide tables need `minmax(0,1fr)`.** The layout grids use `minmax(0,1fr)` so `.tblwrap`
  can scroll horizontally; a bare `1fr` lets intrinsic table width blow out the page.
- **Conservative take-home.** Standard deduction only (no itemized mortgage interest/SALT).
- When you change a default or assumption, update the in-app **Assumptions & sources** block
  (`renderAssump`) and the `README.md` defaults table so they stay in sync.
