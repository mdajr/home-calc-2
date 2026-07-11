# home-calc-2 — 201 Snowberry Way Mortgage Comparison

A single-file tool for **comparing mortgage rate sheets** on our home under contract:
201 Snowberry Way, West Whiteland Twp, Chester County PA — $760,000, settling 8/12/2026.

Open **`index.html`** in any browser — no build step, no dependencies.

*(This replaced the earlier general affordability calculator once we went under contract —
see git history for that version.)*

## What it does

- **Loan scenarios.** One card per lender quote (up to 6): note rate, discount points entered
  as **% or $** (kept in sync; negative = credit priced as points), lender credits ($), that
  lender's own fees ($), and an optional per-lender **escrow override** (LE Section G — escrow
  demands differ by lender). Prepaid interest is computed per loan from its rate and the
  settlement date. An optional **"Sheet P&I"** field verifies transcription: the card shows
  ✓ when the computed payment matches the lender's quoted P&I. **Quoted-on / rate-good-through
  dates** flag quotes ≥3 days old (rates reprice daily) and pricing that expires before
  settlement (lock too short, or an unlocked LE's cost-expiration date).
- **Copy/paste guide.** A collapsible "Where to find these numbers on a lender's sheet" note
  maps official Loan Estimate sections (A/B/G/J) and Chase-style rate menus onto the card
  fields — including the trick of deriving points $ from fee differences between menu rows.
  Sections C/E/F/H (title, government taxes, prepaids, realtor) stay shared so lenders are
  compared on identical third-party costs.
- **Accurate closing costs.** Shared-cost defaults (property tax, insurance, HOA, title,
  transfer tax, escrows, seller reimbursements) come from the KW Buyer's Estimated Closing
  Cost report (v4, 6/22/26) — the realtor's worksheet, not a lender quote. As a math
  cross-check, its illustrative 6.9% / 20% / 0-point scenario is reproduced to the penny:
  **$5,028.21/mo** and **$149,995.89 cash at settlement** (after the $30k earnest deposit and
  $2,475 paid outside closing). Every line item is editable.
- **Adjustable down payment.** Slider (5–50%, default 20%). Below 20% adds PMI, which
  auto-terminates in the model when the balance amortizes to 78% of price. An optional
  appraised value models an appraisal gap (extra cash, smaller loan).
- **Side-by-side table.** Per loan: P&I, PMI, total monthly (PITI+HOA), lender charges,
  **break-even**, cash to close, cash remaining, and 5/10-year true cost. The 10-year best is
  flagged; every other loan shows **"+$X credit to tie"** — the extra lender credit that would
  make it match the best loan over 10 years (the number to take back to that loan officer).
- **Opportunity cost of upfront cash.** A **cash yield** input (default 4%, e.g. VUSXX)
  discounts future interest/PMI to today's dollars in the true-cost metric, since points are
  paid upfront while rate savings arrive over years. Set 0 for plain nominal totals.
- **Break-even vs the cheapest-upfront baseline.** The loan with the lowest lender charges is
  the baseline; every other loan's break-even is the month its cumulative (discounted) cost
  drops below the baseline's — i.e. how long you must keep the loan before refinancing/selling
  stops forfeiting the points/fee premium ("never" = can't repay it in 30 years).
- **Cost-over-time chart.** All loans plotted for 30 years with a metric toggle:
  - **True cost** — cumulative interest + PMI + lender charges, discounted at the cash yield
    (principal builds equity, so it isn't a cost). Dots mark each loan's break-even vs the
    baseline.
  - **Total outlay** — all cash out the door including principal, taxes, insurance, HOA and
    upfront costs (nominal, undiscounted).
  - **Remaining balance.**
  Hover (or focus + arrow keys) for a crosshair readout of every loan at any month; a
  milestone table gives the same values at 3/5/7/10/15/20/30 years.
- **Loan detail.** Full monthly-payment and cash-to-close waterfalls for any loan, matching
  the closing-cost report's structure (closing costs → escrows → seller reimbursements →
  earnest/POC credits).
- **Budget context.** 2026 MFJ take-home (both maxing 401(k) + HSA, standard deduction),
  back-end DTI, and single-income worst-case flags.
- **Persistence & sharing.** Everything (including the loan list) auto-saves to the browser;
  "copy a share link" packs the exact scenario into a URL. Opening a link is non-destructive.

## Key defaults (all editable in the UI)

| Item | Value | Source |
|---|---|---|
| Price / down / term | $760,000 / 20% / 30-yr fixed conventional | contract |
| Settlement | 8/12/2026 → 20 days prepaid interest | KW estimate |
| Property tax | $8,459/yr ($704.92/mo, ≈1.11% of price) | KW estimate |
| Homeowners insurance | $2,100/yr | KW estimate |
| HOA | $144/mo | KW estimate |
| Title insurance | $5,239.66 (enhanced + endorsements) | KW estimate |
| Transfer tax | 1% of price (buyer's half of PA's 2%) | KW estimate |
| Recording + notary | $239 | KW estimate |
| HOA capitalization + misc | $2,195 ($1,700 + $495 KW admin) | KW estimate |
| Lender escrows at closing | $3,456.63 | KW estimate |
| Seller reimbursements | $6,591.86 | KW estimate |
| Earnest already paid | $30,000 | sent |
| Paid outside closing | $2,475 (appraisal $375 + year-1 insurance $2,100) | KW estimate |
| Default lender fees | $750 (appraisal + credit report + doc prep + flood cert + tax service) | KW estimate |
| PMI (if <20% down) | 0.6%/yr until 78% LTV | assumption |
| Cash yield (discount rate) | 4%/yr (≈ VUSXX) | assumption |

Default loan cards are the actual lender quotes in hand: the US Financial/UWM Loan Estimate
of 7/9/26 (6.995% + 0.879 pts, $3,088 fees, $7,525 escrow — **not rate-locked**; its pricing
expires 7/23/26) and all five rows of Chase's rate menu of 7/10/26 (6.625% ≈0-pt baseline
down to 6.125%, points derived from menu fee deltas; menu prices a 45-day lock → good
through 8/24/26).

No separate LLPA is modeled — real rate-sheet pricing already embeds it.

> Estimates only — not financial advice. Final numbers come from each lender's official
> Loan Estimate / Closing Disclosure.
