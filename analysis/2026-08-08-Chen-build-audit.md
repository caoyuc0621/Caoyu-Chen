# Stage 3 Build Audit — EUR Receivable Hedge Model

**Author:** Caoyu Chen  
**Date:** 2026-08-08  
**Workbook:** `models/builds/2026-08-08-Chen-eur-receivable-hedge-model.xlsx`  
**Stage 2 spec:** `docs/specs/2026-08-04-Chen-eur-receivable-hedge-spec.md`

I audited the generated workbook against the committed Stage 2 specification and the Stage 3 build contract.

## Finding 1 — Named ranges

**What I checked:** I inspected the workbook-level defined names and verified the required contract names.

**What I found:** All ten required names are present: `FC_AMT`, `S0_in`, `F0_in`, `R_USD`, `R_FC`, `K_PUT`, `K_CALL`, `PREM_PUT`, `PREM_CALL`, and `T_DAYS`. Each points to the intended placeholder input cell on the Inputs tab.

**What I did:** I confirmed the names are workbook-scoped and that the calculated hedge formulas use those names rather than retyping the market inputs.

## Finding 2 — Sensitivity table recalculation

**What I checked:** I changed `S0_in` from 1.1000 to 1.2000 and reviewed the sensitivity table.

**What I found:** Before the change, the settlement-rate scenarios ran from 1.0450 to 1.1550. After changing `S0_in` to 1.2000, the scenarios recalculated to 1.1400 through 1.2600. This confirms that the eleven scenario rows are formula-driven instead of hand-typed.

**What I did:** I restored `S0_in` to 1.1000 after the test and confirmed the original scenario range returned.

## Finding 3 — Forward versus money-market parity

**What I checked:** I compared the forward proceeds, money-market proceeds, and parity-implied forward rate.

**What I found:** Forward proceeds are approximately **$5,057,100**, while money-market proceeds are approximately **$5,057,047.92**. The difference is about **$52.08**. The parity-implied forward rate is approximately **1.123788**, compared with the indicative `F0_in` value of **1.1238**.

**What I did:** I traced the small difference to rounding of the indicative forward placeholder rather than to a formula error. The difference is within the workbook's validation tolerance, so no formula change was required.

## Finding 4 — Option payoff behavior

**What I checked:** I reviewed the put, sold-call, and collar formulas across the sensitivity range.

**What I found:** The purchased put establishes the required floor at `K_PUT`; the sold call caps the conversion rate at `K_CALL`; and the collar combines the floor and cap while reflecting the net option premium. The option proceeds change with `S_T` rather than remaining hard-coded.

**What I did:** I confirmed the formulas use `MAX` and `MIN` as specified and that the visible validation checks pass for the floor and cap behavior.

## Finding 5 — Spreadsheet errors and visible checks

**What I checked:** I scanned the workbook for `#REF!`, `#DIV/0!`, `#VALUE!`, `#NAME?`, and `#N/A` errors and reviewed the Validation tab.

**What I found:** The error scan returned no matching error cells. All visible checks on the Validation tab return `TRUE`, including forward formula, loan repayment, parity, forward-versus-money-market, option floor/cap behavior, sensitivity-row count, named ranges, formula integrity, and error checks.

**What I did:** I retained the Validation tab as a visible audit trail so the checks can be reviewed again after Stage 4 market data replaces the indicative placeholders.

## Final Audit Status

The workbook satisfies the Stage 3 contract based on the checks above: all ten named ranges are present, calculated cells are formula-driven, required hedge families are included, the sensitivity table contains eleven dynamic scenarios with a comparison chart, and the visible validation checks are passing.
