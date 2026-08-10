# Stage 2 review — EUR receivable · Treasury sign-off

Caoyu — this stage was sitting at **0** because the spec was not in the repository. You pushed it, and then pushed Stages 3, 4, and 5 as well. The 0 is gone; this is a 100. Getting a stalled submission unstuck and then clearing four stages in two days is worth naming.

| Criterion | Score |
|---|---|
| Named-range contract & tab architecture | 30 / 30 |
| Calculation flow | 30 / 30 |
| Validation & sensitivity plan | 20 / 20 |
| Reproducibility & prompt log | 5 / 20 *(instructor-adjusted — see below)* |
| **Total** | **100 / 100** |

**A note on the grade.** The automated scanner looks for a file named exactly `prompt-log.md` at the repo root and scored your reproducibility 5/20 — "prompt-log missing; iteration not shown." Both are wrong. Your `prompt-log-stage2-addition.md` records the drafting prompt, the gap you found in the first draft (the money-market audit trail was not visible and there was no concrete parity check figure), and the specific fix you made in response. I verified it by hand and restored the full 15 points.

**What you did well — and why it matters**

- **Your prompt log documents a real revision, not a transcript.** "The first draft did not make the money-market hedge audit trail sufficiently visible" → you split it into `FC_Borrow`, `USD_Now`, and `MM_Proceeds`, then added `F_Implied` with an indicative check figure of ~1.1238. That is the entire point of a prompt log: showing the version that missed, the diagnosis, and the correction. Most students log only their final prompt, which proves nothing.
- **You pre-committed a numeric check figure.** Writing down "`F_Implied` should be approximately 1.1238" *before* building means the Stage 3 workbook had a target to hit rather than a number to rationalize. Deciding what "correct" looks like in advance is what keeps a validation honest.
- **You required money-market proceeds to tie to forward proceeds.** That second validation rule is the covered-interest-parity relationship stated as a testable assertion. It gives the build something that can actually fail.
- **All ten named ranges, exact, plus a seven-tab architecture.** The contract is complete and the tabs each have a job.

**To push it further (real-desk nuance)**

- **File your prompt logs where a reader will find them.** You now have `promt-log.md` (still misspelled), `prompt-log-stage2-addition.md`, and three more stage additions sitting at the repo root. The content is good; the filing is not. A reviewer opening your repo cannot tell which is current. Consolidate into a single `prompt-log.md` with one section per stage — and rename the typo'd file, which is what tripped the scanner in the first place: `git mv promt-log.md prompt-log.md`.
- **Your Stage 1 memo filename is still mangled.** `docs/decisions/docs:decisions:2026-07-30-...md` has the folder path typed into the filename. It cost you nothing, but it is misfiled: `git mv "docs/decisions/docs:decisions:2026-07-30-Chen-eur-receivable-hedge-framing.md" "docs/decisions/2026-07-30-Chen-eur-receivable-hedge-framing.md"`.
- **Name the tolerance, not just the target.** You specify `F_Implied ≈ F0_in`. How close is close enough — a tenth of a cent, a basis point? Sizing the band before you see the answer is what stops the tolerance from being quietly widened to whatever the model produced.

**Next — Stages 3, 4, 5**

All three are in and reviewed separately. Read those next.

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
