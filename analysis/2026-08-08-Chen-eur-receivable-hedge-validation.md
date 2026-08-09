# Stage 5 Validation — EUR Receivable Hedge

**Author:** Caoyu Chen  
**Date:** 2026-08-08  
**Scenario:** EUR 4,500,000 receivable due in 365 days  
**Independent LLM input:** Stage 2 specification + Stage 4 market-data memo only  
**Raw LLM output:** `analysis/2026-08-08-Chen-eur-receivable-hedge-llm-output.md`

## Part 1 — Independent LLM Execution

I opened a clean analysis run using only the Stage 2 specification and Stage 4 market-data memo. The prompt was:

> Compute all hedge outcomes independently from these two documents and recommend a strategy. Do not use or assume access to the Excel workbook.

No workbook results were included in the prompt, and the run was not corrected while it was producing the analysis. The raw output is saved separately at the path listed above.

The independent run recommended a **full forward hedge** because the forward locks approximately $5.259 million, removes settlement-rate uncertainty, and does not require the explicit option premium. It identified the purchased put as a reasonable alternative if management is willing to pay for upside participation.

## Part 2 — Comparison With the Workbook

The table below compares the fresh LLM calculation with the populated Stage 4 workbook at three settlement-rate points: 95%, 100%, and 105% of `S0_in`.

| Strategy | S_T point | LLM result | Workbook result | Difference | Diagnosis |
|---|---:|---:|---:|---:|---|
| Unhedged | 1.095825 (EUR -5%) | $4,931,212.50 | $4,931,212.50 | $0.00 | No discrepancy |
| Forward | 1.095825 (EUR -5%) | $5,258,997.00 | $5,258,998.01 | $-1.01 | Document precision difference: Stage 4 memo rounds `F0_in` to 1.168666; workbook retains the full CIP value 1.168666225... |
| Money Market | 1.095825 (EUR -5%) | $5,258,998.01 | $5,258,998.01 | $0.00 | No discrepancy |
| Put | 1.095825 (EUR -5%) | $5,085,000.00 | $5,085,000.00 | $0.00 | No discrepancy |
| Call | 1.095825 (EUR -5%) | $4,976,212.50 | $4,976,212.50 | $0.00 | No discrepancy |
| Unhedged | 1.153500 (Base spot) | $5,190,750.00 | $5,190,750.00 | $0.00 | No discrepancy |
| Forward | 1.153500 (Base spot) | $5,258,997.00 | $5,258,998.01 | $-1.01 | Document precision difference: Stage 4 memo rounds `F0_in` to 1.168666; workbook retains the full CIP value 1.168666225... |
| Money Market | 1.153500 (Base spot) | $5,258,998.01 | $5,258,998.01 | $0.00 | No discrepancy |
| Put | 1.153500 (Base spot) | $5,100,750.00 | $5,100,750.00 | $0.00 | No discrepancy |
| Call | 1.153500 (Base spot) | $5,235,750.00 | $5,235,750.00 | $0.00 | No discrepancy |
| Unhedged | 1.211175 (EUR +5%) | $5,450,287.50 | $5,450,287.50 | $0.00 | No discrepancy |
| Forward | 1.211175 (EUR +5%) | $5,258,997.00 | $5,258,998.01 | $-1.01 | Document precision difference: Stage 4 memo rounds `F0_in` to 1.168666; workbook retains the full CIP value 1.168666225... |
| Money Market | 1.211175 (EUR +5%) | $5,258,998.01 | $5,258,998.01 | $0.00 | No discrepancy |
| Put | 1.211175 (EUR +5%) | $5,360,287.50 | $5,360,287.50 | $0.00 | No discrepancy |
| Call | 1.211175 (EUR +5%) | $5,445,000.00 | $5,445,000.00 | $0.00 | No discrepancy |

### Reconciliation

The only discrepancy is the forward result. The fresh LLM used `F0_in = 1.168666` because that is the value written in the Stage 4 market-data memo. The workbook retained the unrounded CIP value `1.168666225001332`. Therefore:

- LLM forward = `4,500,000 × 1.168666` = **$5,258,997.00**
- Workbook forward = `4,500,000 × 1.168666225001332` = **$5,258,998.01**
- Difference = **$1.01**

This is not a workbook formula error or an LLM arithmetic error. It is a **documentation precision issue**: the memo displays six decimal places while the workbook stores more precision. All other strategy outcomes reconcile exactly at the selected points.

## Hand Verification — No Excel

### 1. Forward proceeds

Named-range formula:

`Forward_Proceeds = FC_AMT × F0_in`

Using the workbook's full-precision Stage 4 input:

`= 4,500,000 × 1.168666225001332`

`= $5,258,998.01`

**Reconciliation:** This equals the workbook's forward proceeds.

### 2. Money-market hedge — all three steps

**Step 1 — Borrow the present value of the EUR receivable**

`FC_Borrow = FC_AMT / (1 + R_FC × T_DAYS / 360)`

`= 4,500,000 / (1 + 0.02678 × 365 / 360)`

`= EUR 4,381,046.08`

**Step 2 — Convert the borrowed euros to dollars**

`USD_Now = FC_Borrow × S0_in`

`= 4,381,046.08 × 1.1535`

`= $5,053,536.65`

**Step 3 — Invest the dollars to settlement**

`MM_Proceeds = USD_Now × (1 + R_USD × T_DAYS / 360)`

`= 5,053,536.65 × (1 + 0.0401 × 365 / 360)`

`= $5,258,998.01`

**Reconciliation:** This equals the workbook's money-market proceeds. Because `F0_in` was generated from covered interest parity, the money-market and forward results are economically identical apart from displayed rounding.

### 3. Purchased put at the 95% settlement-rate scenario

At 95% of spot:

`S_T = 0.95 × S0_in = 0.95 × 1.1535 = 1.095825`

Because `1.095825 < K_PUT (1.1500)`, the put is exercised at the strike.

`Put_Effective_Rate = MAX(S_T, K_PUT) = 1.1500`

`Put_Gross_Proceeds = 4,500,000 × 1.1500 = $5,175,000.00`

`Put_Premium_Cost = 4,500,000 × 0.0200 = $90,000.00`

`Put_Net_Proceeds = 5,175,000.00 - 90,000.00 = $5,085,000.00`

**Reconciliation:** This equals the workbook's put result at the 95% sensitivity point.

## Additional Decision Checks

At the base spot, the unhedged receivable would convert to **$5,190,750.00**, while the forward locks **$5,258,998.01**. The forward therefore protects approximately **$68,248.01** more than the base-spot conversion.

The purchased put costs **$90,000.00**. Above the strike, the put's net proceeds equal `FC_AMT × S_T - $90,000`. It overtakes the forward only when `S_T` is approximately:

`S_T = F0_in + PREM_PUT = 1.168666 + 0.0200 = 1.188666`

That is about **3.05% above the current spot**. This quantifies the trade-off: the firm must give up about $90,000 of certain value to retain upside, and the euro must appreciate materially before that flexibility beats the forward.

## Part 4 — Spec Retrospective

The independent run mostly reproduced the workbook correctly, but the exercise exposed several specification/documentation gaps.

First, the Stage 4 memo rounded `F0_in` to six decimals while the workbook retained the full CIP result. That produced a small but real **$1.01** discrepancy in the forward proceeds. A v2 specification would state a precision rule such as: *store rates at full calculation precision and display six decimals; comparison tolerances should use the stored value*. This would prevent a fresh model from treating a displayed rounded number as the exact computational input.

Second, the specification includes a **sold call** as a strategy output, but it does not explicitly state that a standalone short call is not downside protection for a foreign-currency receivable. The independent analysis had to interpret its role. A v2 specification would label the sold call as a comparison component used primarily to fund a collar, and would caution that it leaves EUR depreciation exposure in place while capping appreciation.

Third, the Stage 2 input table originally described option strikes as dealer-quote inputs, while Stage 4 set strikes near the live spot using a scenario convention. The market-data memo makes the final values clear, but a v2 specification would explicitly say that Stage 4's populated values supersede all indicative Stage 2 sourcing language. That would make the document hierarchy clearer to a context-free reader.

Finally, the spec is strong on formulas but lighter on the firm's **risk posture**. The LLM can calculate the outcomes, but choosing between a forward and a put requires a decision criterion. A v2 specification would add a management objective such as: *primary objective is budget certainty; upside participation is secondary unless its expected benefit exceeds the option premium*. That would make the final recommendation less dependent on the model's inferred risk preference.

## Validation Conclusion

The independent LLM run and the workbook reconcile. The only numerical difference is a roughly **$1.01** forward-proceeds variance caused by the market-data memo's rounded `F0_in`, not by a model error. The hand calculations reproduce the workbook for the forward, all three money-market steps, and the put floor. Based on those verified results, the forward recommendation is supported by the live-data model.
