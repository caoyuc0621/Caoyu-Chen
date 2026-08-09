## Stage 3 — AI-Assisted Build + Audit

**Date:** 2026-08-08

**Prompt:**

Read my committed Stage 2 specification for the EUR 4,500,000 receivable hedge model and build the workbook exactly from that specification. Treat every requirement as binding. Include all ten required named ranges, formulas instead of pasted calculated values, a Cover tab with data provenance, a Legend/Key tab using Yellow = inputs, Blue = assumptions, Green = formulas, and Gray = outputs, the forward hedge, the money-market hedge in three explicit steps, put and call option calculations with premiums, the collar comparison, a formula-driven sensitivity table from 0.95× to 1.05× `S0_in` in 1% increments, a comparison chart, and visible validation checks. Save the workbook as `models/builds/2026-08-08-Chen-eur-receivable-hedge-model.xlsx`.

**Audit / iteration notes:**

1. I checked that all ten required workbook-level named ranges exist and point to the intended cells on the Inputs tab.
2. I changed `S0_in` from 1.1000 to 1.2000 to verify that the sensitivity scenarios recalculate. The scenario range changed from 1.0450–1.1550 to 1.1400–1.2600, confirming the rows are formula-driven. I then restored `S0_in` to 1.1000.
3. I checked forward versus money-market parity. Forward proceeds were approximately $5,057,100 and money-market proceeds approximately $5,057,047.92. The approximately $52.08 difference was traced to rounding of the indicative forward input rather than a formula defect.
4. I reviewed the put floor, sold-call cap, collar behavior, and workbook error scan. The visible validation checks passed and no spreadsheet error values remained.
