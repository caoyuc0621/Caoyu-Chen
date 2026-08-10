# Stage 3 review — EUR receivable build & audit · Treasury sign-off

Caoyu — Finding 2 is the strongest thing here. You changed `S0_in` from 1.1000 to 1.2000, recorded that the scenario ladder moved from 1.0450–1.1550 to 1.1400–1.2600, **and then restored the input and confirmed the original range came back.** Reverting a test and verifying the revert is a discipline most people skip. It is what stops a diagnostic from quietly becoming a permanent change to the model.

| Criterion | Score |
|---|---|
| Contract compliance | 50 / 50 |
| Structure & presentation | 25 / 25 |
| Audit note | 12.5 / 25 *(instructor-adjusted — see below)* |
| **Total** | **100 / 100** |

**A note on the grade.** The audit-note scanner counts findings by matching bulleted or numbered lists and returned zero for your note, scoring the criterion 12.5/25. Your five findings are `## Finding N` headings with a checked / found / did structure — a format the counter cannot see. I read the note by hand and scored it as five substantive findings.

**What you did well — and why it matters**

- **Your findings carry numbers, not adjectives.** Finding 3 reports forward proceeds ≈ $5,057,100, money-market proceeds ≈ $5,057,047.92, a difference of $52.08, and a parity-implied forward of 1.123788 against an `F0_in` of 1.1238. A reader can check every one of those. "The parity check passed" would have told me nothing.
- **You traced the $52.08 to its cause rather than waving at it.** Rounding of the indicative forward placeholder, not a formula error — and you said why that meant no code change was needed. Distinguishing a benign residual from a real break is the actual skill in a reconciliation.
- **Finding 5 names what you scanned for.** `#REF!`, `#DIV/0!`, `#VALUE!`, `#NAME?`, `#N/A` — listed explicitly, so a reader knows the scope of the clean result rather than trusting the word "clean."
- **You kept the Validation tab as a standing control.** Your note that it exists so the checks "can be reviewed again after Stage 4 market data replaces the indicative placeholders" shows you were building for the next stage, not just passing this one. That is exactly what happened.

**To push it further (real-desk nuance)**

- **Four of five findings confirm; one investigates.** Findings 1, 4, and 5 check that things exist and behave as designed — necessary, but a workbook can pass all of them and still be wrong. Finding 2 is different in kind: you perturbed an input and predicted what should happen. Aim for more findings that could have come back "no."
- **A $52.08 difference deserves one more question.** You attributed it to rounding `F0_in` to four decimals. Confirm that: 4,500,000 × (1.123788 − 1.1238) is about −$54, which is the right order of magnitude and the right sign. Showing that the residual *equals* the rounding, rather than merely resembling it, converts a plausible explanation into a proven one.
- **Your validation tab returns all `TRUE`.** With indicative placeholders you chose yourself, that is nearly guaranteed — the inputs were selected to be mutually consistent. The check only becomes informative at Stage 4, when the numbers come from a market that has no obligation to agree with you.

**Next — Stage 4**

Already in and reviewed separately. Your parity residual goes from $52.08 to $0.00 on live data, which is worth understanding rather than celebrating — I take that up in the Stage 4 review.

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
