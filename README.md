# home-calc-2 — 201 Snowberry Way Mortgage Comparison

A single-file tool for **comparing mortgage rate sheets** on our home under contract:
201 Snowberry Way, West Whiteland Twp, Chester County PA — $760,000, settling 8/12/2026.

Open **`index.html`** in any browser — no build step, no dependencies.

*(This replaced the earlier general affordability calculator once we went under contract —
see git history for that version.)*

## What it does

- **Loan scenarios.** One card per lender quote (up to 6): note rate, discount points (% of
  loan, negative = credit priced as points), lender credits ($), and that lender's own fees ($).
  Prepaid interest is computed per loan from its rate and the settlement date.
- **Accurate closing costs.** Defaults come from the KW Buyer's Estimated Closing Cost report
  (v4, 6/22/26) and reproduce it to the penny: at 6.9% / 20% down / 0 points the tool shows
  **$5,028.21/mo** and **$149,995.89 cash at settlement** (after the $30k earnest deposit and
  $2,475 paid outside closing). Every line item is editable.
- **Adjustable down payment.** Slider (5–50%, default 20%). Below 20% adds PMI, which
  auto-terminates in the model when the balance amortizes to 78% of price. An optional
  appraised value models an appraisal gap (extra cash, smaller loan).
- **Side-by-side table.** Per loan: P&I, PMI, total monthly (PITI+HOA), % of take-home,
  monthly leftover, lender charges, cash to close, cash remaining, and 5/10-year true cost
  (10-year best flagged).
- **Cost-over-time chart.** All loans plotted for 30 years with a metric toggle:
  - **True cost** — cumulative interest + PMI + lender charges (principal builds equity, so
    it isn't a cost). Crossover dots + a break-even list show exactly when a bought-down
    rate overtakes a cheaper-upfront loan.
  - **Total outlay** — all cash out the door including principal, taxes, insurance, HOA and
    upfront costs.
  - **Remaining balance.**
  Hover (or focus + arrow keys) for a crosshair readout of every loan at any month; a
  milestone table gives the same values at 3/5/7/10/15/20/30 years.
- **Loan detail.** Full monthly-payment and cash-to-close waterfalls for any loan, matching
  the closing-cost report's structure (closing costs → escrows → seller reimbursements →
  earnest/POC credits).
- **Budget context.** 2026 MFJ take-home (both maxing 401(k) + HSA, standard deduction),
  monthly leftover vs. a cushion target, back-end DTI, and single-income worst-case flags.
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

No separate LLPA is modeled — real rate-sheet pricing already embeds it.

> Estimates only — not financial advice. Final numbers come from each lender's official
> Loan Estimate / Closing Disclosure.
