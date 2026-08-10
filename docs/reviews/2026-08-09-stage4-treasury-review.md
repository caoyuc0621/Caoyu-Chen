# Stage 4 review — EUR receivable market data & population · Treasury sign-off

Caoyu — the best thing in this memo is a sentence most students would have deleted: *"The interactive FX Hedging Lab page itself was not directly operable from this workspace, so the cross-check was performed independently using the lab's published equations rather than by clicking through the live form."* You disclosed a limitation in your own verification instead of letting the reader assume you had done something you hadn't. That is exactly right — and it also happens to be the thing I want to push on hardest.

| Criterion | Score |
|---|---|
| Data quality & provenance | 50 / 50 |
| Model resolves cleanly | 33 / 33 |
| Lab cross-check | 17 / 17 |
| **Total** | **100 / 100** |

**What you did well — and why it matters**

- **Both rate legs are tenor-matched to the exposure, and you said why.** A one-year U.S. Treasury CMT for the dollar leg and a one-year German government yield for the euro leg, each justified as "a maturity matching the exposure." Pulling an overnight policy rate into a 365-day carry calculation is the standard silent error here; you avoided it deliberately.
- **You separated the market date from the retrieval date.** "Market date used: 2026-08-07 (latest market close before work began on Saturday)" against a retrieval timestamp of 2026-08-08 16:47 HST. Weekend work on Friday's close is completely normal on a desk — recording *which* is which is what stops someone later misreading your data as stale or as Saturday quotes.
- **You typed every input as live, computed, or scenario.** `PREM_PUT` and `PREM_CALL` are marked assigned-scenario with a stated reason; `F0_in` is marked CIP-implied with an explicit "no separately verified live one-year forward quote was used." Nobody can mistake a course parameter for a market quote in your table.
- **You quantified the drift from your own Stage 2 placeholder.** 1.1238 → 1.168666, a gap of 0.044866, with the conclusion that "the placeholder forward should not be retained after live population." Publishing the size of your own earlier error is a good habit.

**The one thing to understand — your cross-check cannot fail**

Every row of your comparison table reads $0.00 / 0.000000. That is not evidence the workbook is right; it is arithmetic. You computed the lab column from the lab's *published equations* — the same equations the workbook implements — using the *same ten inputs*. Two correct implementations of one formula on one input set will always agree to the cent. The check has no power to detect anything.

The same issue explains your forward-versus-money-market difference of exactly **$0.00**. At Stage 3 that gap was $52.08, and you correctly traced it to rounding `F0_in` to four decimals. At Stage 4 you set `F0_in` to the CIP-implied value computed from `S0_in`, `R_USD`, and `R_FC` — so the money-market leg and the forward leg are now built from *identical* inputs by construction. Of course they tie. A parity check between a forward you derived from parity and a money-market hedge built from the same rates is a tautology, not a control.

None of this costs you points — the rubric asks you to document a cross-check and you documented one honestly and completely, including its limitation. But be precise about what it proves: it proves your workbook implements the formulas you intended. It says nothing about whether those formulas match the market. The real gap — dealer spread and cross-currency basis — is invisible to your model by construction, because no market forward price ever entered it.

**To make it a real check**

Get one independent number in. Even a single quoted one-year EUR/USD forward from any public source, compared against your 1.168666, would give you something that could disagree — and the size of the disagreement *is* the basis-plus-spread you currently cannot see. That is the number a treasurer actually wants.

**Next — Stage 5**

Already in and reviewed separately.

— Treasury

---

### How to work this review — professional workflow

Treat this PR the way an analyst treats feedback from Treasury — a review is a proposal to engage with, not a checklist to rubber-stamp:

1. **Read it yourself first.** Understand each point and form your own view before changing anything. Disagreeing *with a documented reason* is a legitimate, senior response.
2. **Stress-test it with an LLM (pushback pass).** Paste this review and your spec into your AI assistant and ask it to (a) explain anything you're unsure of more deeply, and (b) argue the *other side* — where might the reviewer be wrong, and what would you give up by making each change. You're building judgment, not just executing edits.
3. **Decide, then draft the changes with the LLM.** For the points you accept, have the AI help implement them — you specify exactly what and why. Your spec is the prompt; precise in, correct out.
4. **Verify — non-negotiable.** Re-run your own checks (`scripts/recalc.py`, the parity tie-out, sensitivity continuity, no error cells) and confirm the numbers before you commit. An AI will hand you a confident wrong edit; verification is what makes the result *yours*.
5. **Close the loop on the PR.** Reply in the thread with what you changed, what you pushed back on and why, then commit and push. Writing down the reasoning is exactly how this works on a real team.

*This is the same human-in-the-loop discipline the whole project is built on: the LLM drafts, you edit and verify, and you own the result.*
