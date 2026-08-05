# Prompt Log

## Stage 2 — Model Specification

### Prompt 1 — Initial Draft

**Date:** 2026-08-04

**Prompt:**  
Using my Stage 1 memo for a EUR 4,500,000 receivable due in one year and the Stage 2 assignment instructions, draft a 2–3 page technical model specification. Include all eight required sections, the ten exact named ranges, tab architecture, named-range calculation logic for the forward, money-market, and option hedges, a ±5% sensitivity plan in 1% steps, validation rules, and exact outputs. Clearly flag all placeholder market data as indicative and replaced with live data at Stage 4.

### Human Review and Specific Iteration

**Gap found in the first draft:**  
The first draft did not make the money-market hedge audit trail sufficiently visible and did not provide a concrete covered-interest-parity check figure.

**Fix made:**  
I separated the money-market hedge into three distinct calculations: `FC_Borrow`, `USD_Now`, and `MM_Proceeds`. I also added `F_Implied`, required it to approximately equal `F0_in`, and included an indicative check figure of approximately 1.1238 USD/EUR. I added a second validation rule requiring money-market proceeds to approximately equal forward proceeds.

### Prompt 2 — Revision Check

**Prompt:**  
Review the specification against the Stage 2 rubric. Confirm that it has all ten exact named ranges, all eight sections, no cell addresses, separate money-market steps, a fully specified sensitivity table, concrete validation checks, exact output names, and explicit Stage 4 data sources. Identify and correct any missing requirement.
