<!-- solve-problem:be60764d-87f5-4f39-ba76-43f63474e20c -->
# Zero-liquidation lending without hidden subsidies

A [Tangle Solve problem](https://solve.tangle.tools/problems/be60764d-87f5-4f39-ba76-43f63474e20c).

## What is the problem?

"Zero-liquidation" lending protocols replace forced liquidation with mechanisms that let a borrower keep a position open when collateral value falls below the debt — typically by converting the position to a non-recourse structure, pausing withdrawals, dynamically adjusting rates, or waiting for the borrower to repay or default voluntarily. The census record asks for a **formal characterization of solvency and welfare** in such protocols when:

- Collateral value follows a stochastic process (e.g., geometric Brownian motion, or a jump-diffusion for crypto tail risk) $V_t$;
- The borrower holds debt $D$ (possibly accruing interest at rate $r$) and chooses a repayment time $\tau$, i.e., an optimal stopping problem — the debt is effectively an **American-style claim** on the collateral: the borrower repays when $V_t > D$ (or never, defaulting when underwater);
- The lender/protocol absorbs the shortfall $\max(D - V_\tau, 0)$.

Precise problem: (1) Compute the fair value of the embedded options the protocol grants the borrower — the American put (default option) and prepayment/refinancing option — as functions of protocol parameters (LTV at origination, rate adjustment rules, pause rules). (2) Characterize the optimal stopping boundary $\tau^*$ and resulting default distribution. (3) Determine who bears the residual risk when no liquidations occur — equity holders, LPs, insurers, or future borrowers via rate subsidies — and characterize when the protocol is solvent without an external subsidy. A reasonable formalization: infinite-horizon American option pricing under a collateral diffusion, with endogenous borrower exercise, and a "no hidden subsidy" constraint that the protocol's expected loss must be covered by explicitly priced fees.

## Why does it matters?

Zero-liquidation products are already deployed and marketed as removing liquidation risk, but avoided liquidations do not destroy risk — they transfer it. A rigorous characterization would tell protocol designers and regulators exactly where the default-option value is being funded (often implicitly by LPs or token holders), enable fair pricing of the embedded borrower options, and give a principled answer to when "no liquidations" is genuinely safer versus merely opaque. It also connects DeFi design to a deep classical literature (mortgage prepayment, callable debt) where analogous mispriced borrower options caused historical losses.

## What's already known?

- The referenced SoK (Kotzer et al., 2026) provides a **taxonomy and empirical measurements** of DeFi lending, including zero-liquidation-style designs, but is descriptive — it identifies this pricing question as open rather than solving it. (verify exact content; the census cites it as discussion context only)
- **Classical finance:** Merton (1974) models default as a European put on firm assets; mortgage literature (e.g., Dunn & McConnell 1981; Kau et al. 1992-95 on rational prepayment/default) solves exactly the two-option borrower problem for mortgages. This maps nearly one-to-one onto zero-liquidation lending, but with crypto-specific complications: jump/large-tail collateral dynamics, variable protocol rates, and gas/behavioral frictions.
- **DeFi theory:** liquidation cascade and incentive-compatibility results for overcollateralized lending exist; these typically assume liquidation occurs and characterize liquidator competition. Little formal work prices the borrower's American options in non-liquidating designs.
- Gap: no published model jointly (a) solves the borrower's optimal stopping problem with a jump-diffusion crypto collateral process, and (b) does protocol accounting showing where the option cost is borne, with a solvency condition. That is the target.

## What would count as solving it?

1. **Pricing theorem:** closed-form or provably convergent numerical characterization of the borrower's default and prepayment option values and the optimal exercise boundary, under a stated collateral process (GBM baseline; jump-diffusion extension).
2. **Solvency condition:** a theorem stating necessary and sufficient conditions (on fees, rate spreads, origination LTV) for the protocol's expected revenue to cover expected option payouts — i.e., no hidden subsidy — including an explicit decomposition of which stakeholder class bears each component of risk.
3. **Welfare statement:** comparison of borrower/lender/protocol welfare under zero-liquidation vs. liquidation-based design, showing conditions where each dominates.
4. Optional but valuable: calibration to real collateral data (e.g., ETH volatility/jumps) demonstrating magnitude of the mispriced subsidy in deployed protocols.

## How to start

1. **Read the SoK first** (Kotzer et al., ePrint 2026/675) to extract the concrete mechanism designs marketed as "zero-liquidation" — the exact pause/conversion rules determine which options the borrower holds. Note the census is vague here; you must pin down the specific mechanism before modeling.
2. **Map to the mortgage literature:** Dunn–McConnell-style rational prepayment models and the GSPTS/Kau et al. option-theoretic mortgage papers give you the baseline two-option optimal-stopping formulation almost verbatim. Adapt the payoff to the protocol's accounting.
3. **Solve the baseline:** infinite-horizon American put/prepayment with GBM collateral — use variational inequality / free-boundary methods or least-squares Monte Carlo; then add Poisson jumps to check whether solvency conditions survive tail events (this is where crypto differs from housing).
4. **Main obstacle:** protocol specifics — variable rates and dynamic LTV adjustments make the stopping problem non-stationary; and the "no hidden subsidy" accounting requires modeling who the residual claimant is, which is design-dependent and may not be well-specified by deployed protocols.

## Key references

- Kotzer, A. et al. (2026), *SoK: DeFi Lending and Yield Aggregation Protocol Taxonomy, Empirical Measurements, and Security Challenges*, IACR ePrint 2026/675 — taxonomy and empirical characterization of DeFi lending designs including zero-liquidation products; identifies the open pricing question. https://eprint.iacr.org/2026/675 (verify)
- Merton, R. (1974), "On the Pricing of Corporate Debt: The Risk Structure of Interest Rates," *Journal of Finance* 29(2) — models debt default as a put option on assets; the foundational valuation identity underlying this problem.
- Dunn, K. & McConnell, J. (1981), "A Comparison of Alternative Models for Pricing GNMA Mortgage-Backed Securities," *Journal of Finance* 36(2) — rational prepayment modeling treating borrower prepayment as an option; the closest classical analog to borrower optimal repayment timing.
- Kau, J., Keenan, D., Muller, W., Epperson, J. (1992), "A Generalized Valuation Model for Fixed-Rate Residential Mortgages," *Journal of Money, Credit and Banking* 24(3) — unified option-theoretic framework for simultaneous prepayment and default options; template for the two-option structure here.

## Warnings

- The census record is thin and the SoK is cited secondhand (dated 2026, "verify"); confirm the actual mechanism designs before committing to a payoff structure. "Zero-liquidation" is a marketing term covering heterogeneous mechanisms — your results are only as general as your mechanism abstraction.
- Do not assume GBM suffices: crypto collateral has fat tails and jumps; a solvency result true under GBM can fail catastrophically under jumps, and referees in this space will check.
- The "no hidden subsidy" constraint is easy to satisfy trivially (charge huge fees); the interesting result is the *minimal* fee schedule or the welfare comparison, so state your objective precisely.
- Beware of conflating protocol solvency with LP solvency — the risk transfer chain (borrower → protocol → LP/token holder → future borrowers via rates) must be traced explicitly; that tracing is itself a contribution.

---
*`BCGT-000344` · flagged for review*

## Acceptance guidance

1. **Pricing theorem:** closed-form or provably convergent numerical characterization of the borrower's default and prepayment option values and the optimal exercise boundary, under a stated collateral process (GBM baseline; jump-diffusion extension).
2. **Solvency condition:** a theorem stating necessary and sufficient conditions (on fees, rate spreads, origination LTV) for the protocol's expected revenue to cover expected option payouts — i.e., no hidden subsidy — including an explicit decomposition of which stakeholder class bears each component of risk.
3. **Welfare statement:** comparison of borrower/lender/protocol welfare under zero-liquidation vs. liquidation-based design, showing conditions where each dominates.
4. Optional but valuable: calibration to real collateral data (e.g., ETH volatility/jumps) demonstrating magnitude of the mispriced subsidy in deployed protocols.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/be60764d-87f5-4f39-ba76-43f63474e20c?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000344.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000344)
