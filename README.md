# home-calc-2 — Chester County Home Affordability Calculator

An interactive, single-file calculator for figuring out **how much house you can comfortably
afford** in the Downingtown / Exton / West Chester area (WCASD & DASD).

Open **`index.html`** in any browser — no build step, no dependencies.

## What it does

- **Take-home pay (2026, MFJ).** Models federal brackets, FICA (SS cap + Medicare surtax),
  PA flat 3.07%, and local EIT by township. Assumes both spouses **max 401(k) + HSA** and
  subtracts those from spendable income. Shows your effective tax rate and full breakdown.
- **Budget from real expenses.** Instead of an abstract "% of income," you enter your actual
  mandatory monthly spending (food, childcare, cars/transport, insurance, everything else) plus a
  **cushion** you want left over. *Housing* means the mortgage only (P&I, tax, insurance, PMI,
  HOA); *utilities and maintenance* are treated as living expenses (they scale with the home).
  The housing budget = *take-home − all living expenses − debt − cushion*, and the resulting %
  of gross/net is still shown. (Utilities are still included in the emergency-fund calc.)
- **Max affordable home, by strategy.** Side-by-side comparison of *20% down* (the minimum — no
  PMI), an *optimizer* that finds the down-payment % (≥20%) that buys the most house, and *all
  cash above emergency fund*, each respecting both your cash and your monthly budget. Click any
  row to load its exact price + down-% into the explorer below.
- **Live % of gross & take-home.** Every scenario shows the all-in monthly cost as a percentage
  of both gross and net pay, the monthly leftover, plus back-end DTI.
- **"Explore a specific home" panel.** Price + down-payment sliders with a full monthly
  breakdown (P&I, property tax, insurance, PMI, HOA, utilities, maintenance), cash-to-close
  breakdown, monthly leftover, and flags for cash shortfalls / emergency-fund preservation / PMI.
  The down-payment slider goes **below 20%** (down to 5%) — PMI is added automatically — which is
  useful when you'd rather keep cash on hand for renovations than sink it into the down payment.
  An optional **appraised value** field models an *appraisal gap*: if the home appraises below your
  price, the lender only finances against the appraised value, so you cover the difference in cash at
  closing (which shrinks the loan). Applies to the explored home only.
- **Single-income resilience flags.** Per person: whether their current paycheck alone covers
  mortgage + utilities, and a worst case where the other person is jobless and this person halts
  401(k)/HSA to free up every dollar.
- **Points buy-down analysis.** Cost, monthly savings, and break-even for 0–3 discount points.
- **Emergency fund guard.** Define it as *X months of housing payment + utilities* plus a fixed
  dollar buffer; the months portion scales with whatever home you're evaluating. Down payment +
  closing + transfer tax + property-tax prepaids + buyer's agent fee + lender LLPA + lease overlap + points must leave that emergency fund intact.
- **Per-person income & take-home.** Enter each spouse's salary separately for accurate Social
  Security wage caps and the right number of 401(k)s; see each person's monthly take-home.
- **Shareable links.** "Copy a share link" in the header packs every input — including the specific
  home you're exploring — into a URL. Send it and the recipient opens that exact scenario. Opening a
  link is non-destructive: it shows the shared numbers without overwriting the visitor's own saved
  entries, and once they tweak anything it becomes their working state. Normal use still
  auto-saves to the browser at the plain URL.

## Key assumptions (all editable in the UI)

| Item | Value |
|---|---|
| Federal std deduction (MFJ) | $32,200 |
| SS wage base / person | $184,500 |
| 401(k) limit / person | $24,500 |
| HSA family limit | $8,750 |
| PA income tax | 3.07% (taxes 401k, not HSA) |
| Local EIT | 1.0% most townships (editable) |
| Property tax | 2025 millage × Chester County CLR 30.6% → WCASD ≈1.0%, DASD ≈1.25%, East Caln Twp (DASD) ≈1.328% of price |
| Mortgage term | 30-year fixed |
| Closing costs | 1.5% of price default (title insurance + lender/settlement fees + prepaid interest; editable) |
| Transfer tax | 1.0% of price default — PA realty transfer tax (2% total, customarily split; buyer pays ~1%); set to 0 if the seller covers the buyer's half |
| Property-tax prepaids | 6 months default — lender tax-escrow funding + reimbursing the seller for prepaid taxes; timing-dependent (a summer closing right after the school-tax bill can run 12+ months) |
| Buyer's agent fee | 3% of price default (signed buyer's-agency commission; set to 0 if seller covers it) |
| Lender LLPA | Fannie Mae loan-level price adjustment, 780+ tier, 30-yr purchase (eff. 2026-01-28): one-time % of loan by LTV — 0% (≥25% down), 0.375% (20%/15% down), 0.250% (10%/5% down) |
| Appraisal gap | Optional appraised value (explored home only); if below price, the shortfall is cash at closing and reduces the loan dollar-for-dollar |
| Lease overlap | 3 mo notice × $3,900/mo rent + utilities, **net of income** — only the rent your monthly surplus can't absorb during the overlap draws down savings (editable) |
| Utilities (auto) | $250 base + $0.16/sqft when left blank (≈ $700/mo at 2,800 sqft) |

Property tax is the biggest local unknown: Chester County still assesses off a 1998 base, so the
effective rate on a today's-price home is lower than headline millage suggests — but it varies by
the specific property's assessment. There are **no township presets** — set the effective rate
directly with the slider (1–2%) or type any value (you can go below 1% or above 2%) for a real
listing. PA also doesn't spot-reassess on sale, so for a specific older listing you can paste its
**actual annual tax $** to override the percentage; that override applies to the *explored home*
only — the max-affordable strategy table keeps using the % rate.

> Estimates only — not financial advice. Take-home uses the standard deduction (itemizing
> mortgage interest + SALT could raise it), so results lean conservative. Verify any property's
> assessment and get real lender quotes.
