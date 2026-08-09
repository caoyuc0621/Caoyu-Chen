# Stage 4 Market Data Memo — EUR Receivable Hedge Model

**Author:** Caoyu Chen  
**Retrieval timestamp:** 2026-08-08 16:47 HST  
**Market date used:** 2026-08-07 (latest market close/reference date before work began on Saturday)  
**Workbook:** `models/builds/2026-08-08-Chen-eur-receivable-hedge-model.xlsx`

## Market Inputs and Provenance

| Named range | Stage 4 value | Unit | Source / method | Retrieval timestamp | Rationale / proxy logic |
|---|---:|---|---|---|---|
| `FC_AMT` | 4,500,000 | EUR | Assigned scenario | 2026-08-08 16:47 HST | Scenario notional is unchanged. |
| `S0_in` | 1.1535 | USD/EUR | European Central Bank, euro reference exchange rate for 2026-08-07: https://www.ecb.europa.eu/stats/policy_and_exchange_rates/euro_reference_exchange_rates/html/index.en.html | 2026-08-08 16:47 HST | Official ECB reference rate; EUR 1 = USD 1.1535. |
| `R_USD` | 4.01% | annual, ACT/360 | U.S. Department of the Treasury, Daily Treasury Par Yield Curve CMT Rates, 1-year, 2026-08-07: https://home.treasury.gov/ | 2026-08-08 16:47 HST | A 1-year U.S. Treasury rate matches the one-year hedge horizon and is a high-quality USD government benchmark. |
| `R_FC` | 2.678% | annual, ACT/360 | MarketWatch, Germany 1 Year Government Bond, 2026-08-07 close: https://www.marketwatch.com/investing/bond/tmbmkde-01y?countrycode=bx | 2026-08-08 16:47 HST | Germany's 1-year sovereign yield is used as a high-quality EUR government-rate proxy with a maturity matching the exposure. |
| `F0_in` | 1.168666 | USD/EUR | CIP-implied from live spot and rates | 2026-08-08 16:47 HST | No separately verified live one-year forward quote was used. Formula: `S0_in × (1 + R_USD × T_DAYS/360) / (1 + R_FC × T_DAYS/360)`. |
| `K_PUT` | 1.1500 | USD/EUR | Stage 4 strike setting from live spot | 2026-08-08 16:47 HST | Rounded near the live spot, following the Stage 2 scenario convention where the put strike is at/near spot. |
| `K_CALL` | 1.2000 | USD/EUR | Stage 4 strike setting from live spot | 2026-08-08 16:47 HST | Set approximately $0.05 above the put strike, preserving the scenario's collar spacing convention. |
| `PREM_PUT` | 0.0200 | USD/EUR | Assigned scenario assumption | 2026-08-08 16:47 HST | Retained exactly as instructed because retail-accessible FX option premium quotes are unreliable for this exercise. |
| `PREM_CALL` | 0.0100 | USD/EUR | Assigned scenario assumption | 2026-08-08 16:47 HST | Retained exactly as instructed for the same reason. |
| `T_DAYS` | 365 | days | Assigned scenario | 2026-08-08 16:47 HST | Scenario settlement horizon is unchanged. |

## CIP-Implied Forward

The Stage 4 forward input is calculated using the course ACT/360 convention:

`F0_in = 1.1535 × (1 + 0.0401 × 365/360) / (1 + 0.02678 × 365/360)`

`F0_in = 1.168666 USD/EUR`

The Stage 2 indicative forward was **1.1238 USD/EUR**. The live/CIP-implied Stage 4 forward is higher by approximately **0.044866 USD/EUR**. The difference reflects the new spot and one-year rate environment, so the placeholder forward should not be retained after live population.

## Workbook Population and Re-check

Only the named-range input cells on the Inputs tab were changed. The workbook recalculated without changing the hedge formulas.

After population:

- Forward proceeds: **$5,258,998.01**
- Money-market proceeds: **$5,258,998.01**
- Forward minus money-market difference: **$0.00**
- Purchased-put net proceeds at the base spot: **$5,100,750.00**
- Sold-call net proceeds at the base spot: **$5,235,750.00**
- Collar net proceeds at the base spot: **$5,145,750.00**

All visible validation checks return `TRUE`, and the workbook error scan found no `#REF!`, `#DIV/0!`, `#VALUE!`, `#NAME?`, or `#N/A` cells. The sensitivity table recalculated around the new `S0_in` value.

## FX Hedging Lab Cross-check

The course FX Hedging Lab specifies the same Stage 4 calculation rules used in the workbook: forward proceeds are `FC_AMT × F0_in`; the money-market hedge is the three-step borrow/convert/invest sequence; and the parity-implied forward uses the same ACT/360 CIP equation.

Using the same Stage 4 inputs, the independent lab-formula check gives:

| Output | Workbook | Lab-formula check | Difference |
|---|---:|---:|---:|
| Forward proceeds | $5,258,998.01 | $5,258,998.01 | $0.00 |
| Money-market proceeds | $5,258,998.01 | $5,258,998.01 | $0.00 |
| CIP-implied forward | 1.168666 | 1.168666 | 0.000000 |
| Put net proceeds at base spot | $5,100,750.00 | $5,100,750.00 | $0.00 |

**Resolution:** No discrepancy was found between the populated workbook and the calculation logic published in the FX Hedging Lab materials.

**Environment note:** The interactive FX Hedging Lab page itself was not directly operable from this workspace, so the cross-check was performed independently using the lab's published equations from the course materials rather than by clicking through the live form.

## Structural Fixes

No structural formula fixes were required after loading Stage 4 data. The Stage 3 workbook accepted the live values through the named-range input cells, the parity check passed, and the sensitivity analysis recalculated correctly.
