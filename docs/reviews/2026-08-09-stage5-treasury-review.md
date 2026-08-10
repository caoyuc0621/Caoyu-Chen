# Stage 5 review — EUR receivable LLM analysis & validation · Treasury sign-off

Caoyu — you found the crossover. Section C states that the put overtakes the forward at approximately **1.188666 USD/EUR**, roughly 3.05% above spot. I checked it: the put nets `max(S_T, K_PUT) − PREM_PUT` per euro against the forward's `F0_in`, so breakeven is exactly `F0_in + PREM_PUT` = 1.168666 + 0.0200 = 1.188666. Correct, and correctly converted to a percentage. That single number does more work than any table in the memo, because it turns "the put has upside" into "the euro must rise 3% before the upside is worth what you paid for it."

| Criterion | Score |
|---|---|
| LLM execution & comparison | 25 / 25 |
| Hand verification | 25 / 25 |
| Recommendation & executive voice | 25 / 25 |
| Spec retrospective | 17 / 17 |
| Repo polish | 4.8 / 8 |
| **Total** | **97 / 100** |

**What you did well — and why it matters**

- **Your arithmetic reconciles end to end.** I spot-checked four figures against your stated inputs: put at base spot `4.5M × (1.1535 − 0.02)` = $5,100,750 ✓; sold call `4.5M × (1.1535 + 0.01)` = $5,235,750 ✓; collar `4.5M × (1.1535 − 0.02 + 0.01)` = $5,145,750 ✓; put floor at −5% `4.5M × (1.15 − 0.02)` = $5,085,000 ✓. Every one lands. Numbers that survive an independent recomputation are the whole point of this stage.
- **You refused to let the sold call pass as a hedge.** "It should not be viewed as a complete downside hedge on its own because the proceeds still decline when the euro weakens. Its main economic role is as a funding component for a collar." That is precisely right, and it is the single most common error in student Stage 5 memos.
- **You framed the recommendation on exposure, not forecast.** "The recommendation is based on the firm's exposure rather than a forecast of the euro." A treasurer who hedges on a currency view has taken a position, not removed one. You stated the distinction explicitly.
- **You named the cost of your own recommendation.** Section E ends on foregone upside and calls it "a deliberate trade." A recommendation that lists only its benefits reads as advocacy; naming what it costs is what makes it advice.
- **You left a real option open without hedging your bets.** The partial-hedge suggestion — forward the budget-critical portion, put the remainder — is a genuine alternative, and you correctly said the model gives no evidence to prefer it. Offering an alternative *and* declining to recommend it is a senior move.

**The one substantive correction — the forward does not earn you $68,248**

Section B says the forward "locks roughly **$68,248.01** more in expected dollar proceeds" than converting at today's spot. Arithmetically that is right ($5,258,998 − $5,190,750), but economically it compares two different things: dollars received *in one year* against dollars received *today*.

That $68,248 is the forward premium, and it exists precisely because `R_USD` (4.01%) exceeds `R_FC` (2.678%). It is not a gain — it is compensation for the interest differential, and you would capture the identical amount by converting at spot today and depositing the dollars for a year. Run it: $5,190,750 × (1 + 0.0401 × 365/360) ≈ $5,401,800, which is *more* than the forward, because that route also earns a full year of USD interest on the whole amount.

The correct comparison discounts the forward proceeds back to today, or grows the spot proceeds forward to settlement — never one of each. This matters on a real desk because "the forward pays more than spot" is the reasoning that leads someone to describe a forward premium as free money and size a position on it. Your recommendation does not depend on the $68,248 claim, so nothing else in the memo breaks; just cut it or reframe it as the interest differential it is.

**Repo polish — 3.2 points, ten minutes of work**

The only mechanical points you lost: no `LICENSE` file and no repository description set on GitHub. Add a LICENSE (MIT is fine for coursework) and write a one-line description in your repo's About panel. Also finish the filing cleanup from the Stage 2 review — the four root-level prompt-log files, the `promt-log.md` typo, and the colon-mangled Stage 1 memo filename. Your analysis is at 100; the repo around it is what is costing you.

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
