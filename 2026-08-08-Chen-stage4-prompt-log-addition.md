## Stage 4 — Market Data + Population

**Date:** 2026-08-08

**Prompt:**

Use the Stage 4 instructions and my Stage 3 EUR receivable hedge workbook. Retrieve defensible market data for the latest market close before I begin, document source and retrieval timestamp for every input, keep the scenario option premiums as required, compute a CIP-implied one-year forward if a reliable live forward quote is not available, populate only the named-range input cells, re-run all checks, confirm the sensitivity table recalculates around the new spot, and cross-check the workbook against the FX Hedging Lab calculation logic.

**AI-assisted data choices and checks:**

- `S0_in` = 1.1535 from the ECB 2026-08-07 EUR/USD reference rate.
- `R_USD` = 4.01% from the U.S. Treasury 1-year CMT for 2026-08-07.
- `R_FC` = 2.678% using the Germany 1-year government bond yield as the EUR sovereign proxy.
- `F0_in` = 1.168666, calculated with covered interest parity and ACT/360.
- `K_PUT` = 1.1500 and `K_CALL` = 1.2000, set near live spot while preserving the scenario's strike-spacing convention.
- `PREM_PUT` = 0.0200 and `PREM_CALL` = 0.0100 were retained from the scenario as required.
- The populated workbook passed all visible validation checks and contained no spreadsheet error values.
- The workbook outputs matched an independent reproduction of the FX Hedging Lab's published equations. The interactive lab form itself was not directly operable from the AI workspace, so this limitation is documented in the market-data memo.
