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
  **cushion** you want left over. The housing budget = *take-home − expenses − debt − cushion*.
  The resulting % of gross/net is still shown so you can see where you land.
- **Max affordable home, by strategy.** Side-by-side comparison of *all cash above emergency
  fund*, *20% down (no PMI)*, *10% down*, and an *optimizer* that finds the down-payment % that
  buys the most house — respecting both your cash and your monthly budget.
- **Live % of gross & take-home.** Every scenario shows the all-in monthly cost as a percentage
  of both gross and net pay, the monthly leftover, plus back-end DTI.
- **"Explore a specific home" panel.** Price + down-payment sliders with a full monthly
  breakdown (P&I, property tax, insurance, PMI, HOA, utilities, maintenance), cash-to-close
  breakdown, monthly leftover, and flags for cash shortfalls / emergency-fund preservation / PMI.
- **Single-income resilience flags.** Per person: whether their current paycheck alone covers
  mortgage + utilities, and a worst case where the other person is jobless and this person halts
  401(k)/HSA to free up every dollar.
- **Points buy-down analysis.** Cost, monthly savings, and break-even for 0–3 discount points.
- **Emergency fund guard.** Define it as *X months of housing payment + utilities* plus a fixed
  dollar buffer; the months portion scales with whatever home you're evaluating. Down payment +
  closing + points must leave that emergency fund intact.
- **Per-person income & take-home.** Enter each spouse's salary separately for accurate Social
  Security wage caps and the right number of 401(k)s; see each person's monthly take-home.

## Key assumptions (all editable in the UI)

| Item | Value |
|---|---|
| Federal std deduction (MFJ) | $32,200 |
| SS wage base / person | $184,500 |
| 401(k) limit / person | $24,500 |
| HSA family limit | $8,750 |
| PA income tax | 3.07% (taxes 401k, not HSA) |
| Local EIT | 1.0% most townships (East Whiteland 0.8%) |
| Property tax | 2025 millage × Chester County CLR 30.6% → WCASD ≈1.0%, DASD ≈1.25% of price |

Property tax is the biggest local unknown: Chester County still assesses off a 1998 base, so the
effective rate on a today's-price home is lower than headline millage suggests — but it varies by
the specific property's assessment. Adjust the field for a real listing.

> Estimates only — not financial advice. Take-home uses the standard deduction (itemizing
> mortgage interest + SALT could raise it), so results lean conservative. Verify any property's
> assessment and get real lender quotes.
