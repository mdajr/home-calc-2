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
- **30-yr vs 15-yr comparison.** A side-by-side block in the explorer shows the monthly P&I, total
  housing, monthly leftover, and lifetime interest for both terms on the same home. The 15-yr is
  priced at your base rate minus a configurable discount (default 0.75%, since 15-yr loans typically
  price below 30-yr). Switch the **Loan term** selector to 15 yr to run the full affordability table
  on a 15-yr instead.
- **Single-income resilience flags.** Per person: whether their current paycheck alone covers
  mortgage + utilities, and a worst case where the other person is jobless and this person halts
  401(k)/HSA to free up every dollar.
- **Points buy-down analysis.** Cost, monthly savings, and break-even for 0–3 discount points.
- **30-year wealth: mortgage vs. investing.** Projects how much you'd accumulate in an S&P 500
  index fund (default 10%/yr, compounded monthly) under seven strategies that all share an
  *identical* cash outlay — the 15-yr P&I each month for 30 years plus the buydown's worth up
  front — so the (fully-owned, equal) home cancels out and only the portfolio differs: **(1)** a
  30-yr loan investing the 15-vs-30 monthly gap, **(2)** a 15-yr loan then investing the freed-up
  payment in years 15–30, **(3)** buying the 30-yr rate down to the 15-yr rate and investing the
  larger monthly gap, **(4)** buying it down then paying it off in 15 years before investing,
  **(5)** a 15-yr loan with a further paid buydown, investing the smaller monthly gap then the full
  payment after payoff, and — using that same pot of cash as a **bigger down payment** instead of a
  buydown — **(6)** a 30-yr and **(7)** a 15-yr on the reduced loan. Scenarios 1 & 2 invest that
  cash up front; 3–5 spend it on a buydown; 6 & 7 sink it into the down payment. The table also
  shows each buydown's **true economic break-even** — the month its *net worth* (portfolio minus
  remaining mortgage balance) overtakes investing that same cash instead, fully accounting for the
  reinvested payment savings *and* the opportunity cost of the upfront money (a buydown that can
  never overtake is flagged "never").
- **Emergency fund guard.** Define it as *X months of housing payment + utilities* plus a fixed
  dollar buffer; the months portion scales with whatever home you're evaluating. Down payment +
  closing + buyer's agent fee + lease overlap + points must leave that emergency fund intact.
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
| Mortgage term | 30 yr default; 15-yr modeled at base rate − 0.75% (editable) |
| Closing costs | 3% of price default (lender/title/escrow/transfer; editable) |
| Buyer's agent fee | 3% of price default (signed buyer's-agency commission; set to 0 if seller covers it) |
| Lease overlap | 3 mo notice × $3,900/mo rent + utilities, **net of income** — only the rent your monthly surplus can't absorb during the overlap draws down savings (editable) |
| Utilities (auto) | $250 base + $0.16/sqft when left blank (≈ $700/mo at 2,800 sqft) |
| S&P 500 return | 10%/yr nominal, compounded monthly (editable); no investment taxes |

Property tax is the biggest local unknown: Chester County still assesses off a 1998 base, so the
effective rate on a today's-price home is lower than headline millage suggests — but it varies by
the specific property's assessment. There are **no township presets** — set the effective rate
directly with the slider (1–2%) or type any value (you can go below 1% or above 2%) for a real
listing.

> Estimates only — not financial advice. Take-home uses the standard deduction (itemizing
> mortgage interest + SALT could raise it), so results lean conservative. Verify any property's
> assessment and get real lender quotes.
