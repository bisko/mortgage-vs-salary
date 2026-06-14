# Bulgaria Reverse-Affordability Mortgage Calculator — Spec

**Date:** 2026-06-14
**Goal:** From a household's net salary, work *backwards* to the apartment they could buy on a mortgage over N years (default 30) in a Bulgarian city — accounting for living expenses.

> Note: not a literal "reverse mortgage" (equity release for retirees). It is an
> affordability calculator run in reverse: income → affordable property.

## Calculation model
1. **Household net income** = income 1 + income 2 (income 2 optional).
2. **Money left for mortgage** = income − (Σ percentage-expense categories × income) − (Σ fixed €/mo line items).
3. **Bank rule**: realistic max monthly payment = `min(money left after expenses, bankCap% × income)`.
   - Bulgarian banks cap loan payments at ~40% of net income (50% legal max, 2025 rules).
   - Report which constraint binds: *budget* (expenses) or *bank* (cap), or *none* (no money left).
4. **Reverse-amortize** to a loan principal: `L = P × (1 − (1+i)⁻ⁿ) / i`, where `i = annualRate/12`, `n = years×12`. If `i = 0`, `L = P × n`.
5. **Affordable price** = `L / (1 − downPayment%)`. **Down-payment cash** = `price × downPayment%`.
6. **Apartment** = `m² = price / (€ per m²)` for the chosen city, mapped to a BG room label.

## Inputs
- Net monthly income 1 (€), net monthly income 2 (€, optional)
- Term years (default 30), annual interest rate % (default 3), down-payment % (default 15), bank cap % (default 40, max 50)
- City dropdown → editable €/m² (Sofia ~2400, Plovdiv ~1500, Varna ~1750, Burgas ~1500, Stara Zagora ~1100, National ~1400, Custom)
- Expense % sliders w/ BG defaults (share of net income): Food 30, Utilities 10, Transport & comms 11, Other 10
- Custom fixed expenses: add/remove rows of `label + €/month` (e.g. School — €800)

## Output (price primary)
- Headline: max affordable apartment **price**
- Secondary: **m²** and **room label** in the chosen city
- Stats: monthly payment, loan amount, down-payment cash, total interest over term, money left after expenses
- Binding-constraint note; warning when nothing is left for a mortgage
- "Where your money goes" stacked bar (food/utilities/transport/other/fixed/mortgage/unused)
- Amortization: yearly table (payment / interest / principal / balance) + balance-over-time chart

## Data sources (Bulgaria, 2025–2026)
- Affordability cap ~40% (50% max): EMF Hypostat 2025; BG bank lending rules.
- Household spending shares (NSI Q2 2025): food 30.1%, taxes/social ins. 15.9%, housing/utilities 14.7%, transport & comms 11.0%. (Taxes excluded here — model uses *net* income.)
- Sofia ~€2,400/m² avg (range €1,250 Obelya → €4,000+ central); other cities estimated.

## Tech
Single `index.html`, vanilla JS, inline CSS, no external libraries (works offline). Inline SVG chart. Live recalculation on every input change.
