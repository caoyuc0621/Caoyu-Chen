---
title: "EUR Receivable Hedge Model Specification"
author: "Caoyu Chen"
date: "2026-08-04"
version: "1.0"
---

# EUR Receivable Hedge Model Specification

> All market values below are **indicative — replaced with live market data at Stage 4**.

## 1. Problem Statement

The company expects to receive **EUR 4,500,000 in 365 days**. Because the receivable is fixed in euros while the company reports and budgets in U.S. dollars, the final USD value depends on the EUR/USD exchange rate at settlement. A weaker euro would reduce the USD proceeds. The workbook must compare an unhedged position, a forward hedge, a money-market hedge, and option-based protection so management can evaluate certainty, flexibility, and cost.

## 2. Inputs — Named-Range Contract

| Named range | Description | Indicative placeholder | Unit | Stage 4 source |
|---|---|---:|---|---|
| `FC_AMT` | Foreign-currency receivable | 4,500,000 | EUR | Confirmed receivable amount from company records |
| `S0_in` | Spot rate at inception | 1.1000 | USD per EUR | Live EUR/USD spot quote from an approved market-data source |
| `F0_in` | One-year forward rate | 1.1238 | USD per EUR | Live one-year EUR/USD forward quote from an approved dealer or market-data source |
| `R_USD` | USD interest rate | 5.30% | Annual %, ACT/360 | Federal Reserve H.15 or comparable USD money-market rate |
| `R_FC` | EUR interest rate | 3.10% | Annual %, ACT/360 | ECB or comparable EUR money-market rate |
| `K_PUT` | Put option strike | 1.1000 | USD per EUR | Live dealer quote for an at-the-money or near-the-money EUR put |
| `K_CALL` | Call option strike | 1.1500 | USD per EUR | Live dealer quote for an out-of-the-money EUR call |
| `PREM_PUT` | Put premium per EUR | 0.0200 | USD per EUR | Live option premium from an approved dealer |
| `PREM_CALL` | Call premium per EUR | 0.0100 | USD per EUR | Live option premium from an approved dealer |
| `T_DAYS` | Days to settlement | 365 | Days | Contract settlement date minus valuation date |

All ten names must be created exactly as written. Input cells must be clearly distinguished from calculated cells, and every input must show its unit and source.

## 3. Tab Architecture

1. **Cover** — workbook title, purpose, author, version, valuation date, and a short executive summary.
2. **Legend_Key** — definitions of input, formula, output, warning, and check cells; list of named ranges and units.
3. **Inputs** — all ten named-range inputs, placeholder labels, units, sources, and Stage 4 replacement notes.
4. **Forward_Hedge** — forward formula, locked USD proceeds, and comparison with unhedged proceeds.
5. **Money_Market_Hedge** — three visible steps: EUR borrowing, spot conversion, and USD investment, plus parity check.
6. **Options_Hedge** — purchased put calculation, optional covered-call/collar calculation, premiums, payoffs, and net proceeds.
7. **Sensitivity** — settlement-rate scenarios, proceeds by strategy, comparison table, and chart.
8. **Notes_Assumptions** — assumptions, constraints, transaction-cost treatment, premium treatment, and model limitations.

## 4. Assumptions and Constraints

- Interest is calculated using **simple interest and ACT/360**.
- The receivable is collected in full on the settlement date.
- Forward, borrowing, lending, and option transactions are assumed executable in the full notional amount.
- Transaction fees, bid-ask spreads, taxes, credit charges, and collateral requirements are excluded in this stage unless entered separately in Stage 4.
- The money-market hedge uses the same borrowing and investment rates shown in the Inputs tab.
- Covered interest rate parity is expected to hold approximately. Small differences may result from market spreads or rounding.
- The put premium is paid regardless of whether the option is exercised. For this specification, the premium is deducted directly from settlement proceeds without financing or time-value adjustment.
- The call premium is treated as cash received and added to proceeds. A sold call caps the benefit from euro appreciation at `K_CALL`.
- No hard-coded market values may appear in formulas; formulas must reference named ranges.
- The workbook must contain no circular references and no error cells.

## 5. Calculation Flow

### A. Unhedged Position

For each settlement spot rate `S_T`:

`Unhedged_Proceeds = FC_AMT × S_T`

This amount changes directly with the future EUR/USD rate.

### B. Forward Hedge

`Forward_Proceeds = FC_AMT × F0_in`

The result is fixed and does not change with `S_T`.

### C. Money-Market Hedge

Show each step in a separate calculated cell:

1. Borrow the present value of the EUR receivable:

   `FC_Borrow = FC_AMT / (1 + R_FC × T_DAYS / 360)`

2. Convert the borrowed euros to dollars at the current spot rate:

   `USD_Now = FC_Borrow × S0_in`

3. Invest the dollars until settlement:

   `MM_Proceeds = USD_Now × (1 + R_USD × T_DAYS / 360)`

Also calculate the parity-implied forward rate:

`F_Implied = S0_in × (1 + R_USD × T_DAYS / 360) / (1 + R_FC × T_DAYS / 360)`

### D. Purchased Put Hedge

At each settlement spot rate `S_T`, calculate the effective conversion rate:

`Put_Effective_Rate = MAX(S_T, K_PUT)`

Gross proceeds:

`Put_Gross_Proceeds = FC_AMT × Put_Effective_Rate`

Premium cost:

`Put_Premium_Cost = FC_AMT × PREM_PUT`

Net proceeds:

`Put_Net_Proceeds = Put_Gross_Proceeds − Put_Premium_Cost`

The put establishes a floor while preserving upside above the strike, less the premium.

### E. Sold Call / Collar Component

For a sold EUR call, calculate the capped conversion rate:

`Call_Effective_Rate = MIN(S_T, K_CALL)`

Gross proceeds:

`Call_Gross_Proceeds = FC_AMT × Call_Effective_Rate`

Premium received:

`Call_Premium_Received = FC_AMT × PREM_CALL`

Net proceeds:

`Call_Net_Proceeds = Call_Gross_Proceeds + Call_Premium_Received`

For an optional collar comparison:

`Collar_Net_Proceeds = FC_AMT × MIN(MAX(S_T, K_PUT), K_CALL) − FC_AMT × PREM_PUT + FC_AMT × PREM_CALL`

The collar creates a floor at `K_PUT`, a cap at `K_CALL`, and a net premium equal to put premium paid minus call premium received.

## 6. Sensitivity Plan

Create settlement spot-rate scenarios from:

`0.95 × S0_in` through `1.05 × S0_in`

Use 1% increments of `S0_in`, producing these eleven multipliers:

`0.95, 0.96, 0.97, 0.98, 0.99, 1.00, 1.01, 1.02, 1.03, 1.04, 1.05`

For each `S_T`, calculate:

- Unhedged proceeds
- Forward proceeds
- Money-market proceeds
- Put net proceeds
- Call net proceeds
- Collar net proceeds

Create one line chart with `S_T` on the horizontal axis and USD proceeds on the vertical axis. The chart must allow the CFO to see which strategies provide fixed proceeds, which create a floor or cap, and which preserve upside when the euro strengthens.

## 7. Validation Rules and Check Figures

1. **Forward formula check:**  
   `Forward_Proceeds` must equal `FC_AMT × F0_in`.

2. **Loan repayment check:**  
   `FC_Borrow × (1 + R_FC × T_DAYS / 360)` must equal `FC_AMT`, subject only to rounding.

3. **Parity check:**  
   `F_Implied` should approximately equal `F0_in`. With the indicative placeholders, `F_Implied` is approximately **1.1238 USD/EUR**.

4. **Forward versus money-market check:**  
   `MM_Proceeds` should approximately equal `Forward_Proceeds`. With the indicative placeholders, both are approximately **USD 5.057 million**, subject to rounding.

5. **Put floor check:**  
   When `S_T < K_PUT`, `Put_Effective_Rate` must equal `K_PUT`. When `S_T > K_PUT`, it must equal `S_T`.

6. **Call cap check:**  
   When `S_T > K_CALL`, `Call_Effective_Rate` must equal `K_CALL`. When `S_T < K_CALL`, it must equal `S_T`.

7. **Sensitivity check:**  
   The sensitivity table must contain exactly eleven settlement-rate rows from `0.95 × S0_in` to `1.05 × S0_in`.

8. **Formula integrity check:**  
   Every output must be produced by a formula, with no manually typed output values.

9. **Error check:**  
   The workbook must contain no `#DIV/0!`, `#VALUE!`, `#NAME?`, `#REF!`, or other error cells.

10. **Named-range check:**  
    All ten required named ranges must exist and refer to the correct input cells.

## 8. Required Outputs

The workbook must clearly label and display:

- `Forward_Proceeds`
- `FC_Borrow`
- `USD_Now`
- `MM_Proceeds`
- `F_Implied`
- `Put_Premium_Cost`
- `Put_Net_Proceeds`
- `Call_Premium_Received`
- `Call_Net_Proceeds`
- `Collar_Net_Proceeds`
- Sensitivity comparison table
- Strategy comparison chart
- Validation status for each check

The Cover tab must summarize the main trade-off: the forward and money-market hedges provide certainty, the put provides downside protection while preserving upside at a premium cost, and the collar reduces premium cost but limits upside.
