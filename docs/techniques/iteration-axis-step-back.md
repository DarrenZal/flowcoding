---
doc_id: fc.technique.iteration-axis-step-back
doc_kind: pattern
status: draft
depends_on:
  - fc.flow-state-practice
relates_to:
  - fc.spec-driven-development
concepts:
  - iteration-discipline
  - reflective-practice
  - reframing
---

# Iteration on the Same Axis — Step Back at Three

When a draft, proposal, plan section, or response cycles through three or more iterations on the **same dimension of pushback**, stop refining the answer and inspect the question. Repeated thrash on a single axis is a signal that the framing is wrong, not the wording.

## Context

A practitioner is iterating an artifact — a written reply, a plan section, a design proposal, a debugging hypothesis, a refactor approach — in collaboration with a reviewer (human operator, AI reviewer, peer). Each round of feedback adjusts something specific. The artifact converges, or the artifact thrashes. Thrash on different axes (clarity, then tone, then scope) is normal exploration. Thrash on the **same axis** repeatedly is the symptom this pattern names.

## Problem

How do you tell the difference between productive iteration and iteration that is silently optimizing inside a wrong frame?

The asymmetry: each individual round of pushback feels local — "tighten this default", "try X instead", "still not right, try Y" — and each individual response feels reasonable. The pattern is only visible across the sequence. Without a counter, the iterator can spend many rounds refining a wording when the question itself was the problem.

## Forces

- **Local responsiveness vs. structural reframing.** Each round of feedback rewards immediate adjustment. Stopping to ask "wait, am I answering the right question?" feels like ignoring the operator's specific note.
- **Sunk-cost momentum.** The more rounds invested in an artifact, the harder it is to abandon the frame the artifact is shaped around.
- **Operator articulation lag.** The operator may not realize the framing is wrong until round 3 — they may be discovering it in real time as they push back. The iterator's job is to catch the pattern before the operator has to surface it explicitly.
- **Phase confusion.** Build-phase iteration (refining wording) is mistaken for design-phase iteration (resolving the question). The two have different stuck points and different counters.

## Pattern

**Track iterations per axis, not per artifact.** An artifact may go through ten rounds of feedback. The discipline is to track which dimension each round addresses. When the same dimension surfaces three times, fire a step-back, regardless of how the artifact is otherwise progressing.

**The three-round trigger.** Suggested decision rule:

- Round 1 on axis A: adjust the answer.
- Round 2 on axis A: adjust the answer differently.
- Round 3 on axis A: stop. Step back. The framing is suspect.

**Step-back questions.** When the trigger fires, the iterator asks (silently, or out loud to the operator):

1. What was the operator's actual end-goal for this artifact?
2. What did I assume that they did not say?
3. Is there a simpler question that, if answered, makes this artifact unnecessary or trivial?

**The fix is often outside the artifact, not inside it.** A draft that is thrashing on "what is the right default integration flow" may dissolve when the iterator asks "what does the operator actually need from this email?" — and the answer is something far simpler than any flow under consideration. The reframing replaces the artifact rather than refining it.

**This pattern is a flow-state primitive.** It maps to the Reflect phase of the flow-coding arc (`flow-state-practice.md`). Three iterations on the same axis is the moment to switch from build-phase movement (refine the artifact) to reflect-phase movement (extract the learning, then re-spec). Without the switch, working memory continues to carry the wrong question and execution stays stuck even while wording improves.

## Evidence

**Trace 1 — Read.ai integration reply, IndigenomicsAI session 2026-05-04 / 2026-05-05.** A reply to a collaborator about meeting-notes integration cycled through six versions:

- v1 (prior session): proposed a custom-webhook integration → wrong because the architecture was already locked.
- v2: proposed native Read.ai → Notion auto-sync → wrong because of consent default.
- v3: proposed default-off, opt-in by tag → wrong because still pre-supposed the collaborator wanted *some* auto-flow.
- v4: "what did you have in mind?" → too cautious; lost the original specific ask.
- v5: corrected an inaccurate Otter claim → narrow surgical fix, sidestepping the framing.
- v6: direct path — ask the meeting organizer to add the Gmail address to the invite → simple, after the operator surfaced that the assumed integration target was not actually joining the relevant meetings.

The thrash from v1→v3 was all on the same axis (auto-sync default posture). The right move at v3 was not v4's "ask her what she had in mind" — it was the step-back: "what does the operator actually need from this email?" If the iterator had stepped back at iteration 3, v6's framing would have been v3.

Source memory rule: `feedback_iteration_pattern_step_back.md` in the IndigenomicsAI project memory, discovered 2026-05-05.

## Generalization

The pattern surfaced in collaborative writing but generalizes across the development arc:

| Phase | Axis-thrash signature | Step-back question |
|---|---|---|
| Spec / plan iteration | Same constraint or acceptance criterion keeps getting reworded | Is this constraint at the right altitude? |
| Code refactor | Same edit gets reverted across rounds | Is the refactor solving the right problem? |
| Debugging loop | Same hypothesis class keeps being eliminated | Is the symptom the right symptom to chase? |
| Design proposal | Same trade-off keeps being re-litigated | Is the trade-off real or is it a frame artifact? |

In every case, three iterations on the same dimension is the signal to step back from the artifact and inspect the question.

## See Also

- [Flow State Practice](../foundations/flow-state-practice.md) — Reflect phase, where step-back lives in the development arc.
- [Spec-Driven Development](../foundations/spec-driven-development.md) — the plan-review loop is the structured form of axis-tracking at design time.

## Open Questions

- **What counts as the "same axis"?** When axes overlap (clarity and scope; default and consent) the trigger can fire too early or too late. A clearer rubric for axis-identity would sharpen the rule.
- **Does the rule survive multi-reviewer cases?** When two reviewers push on the same axis from different directions, the rule may fire prematurely. Multi-reviewer dynamics are not yet observed.
- **Is three the right number?** The number is borrowed from a single trace. Two might catch faster but produce false positives; four might be too late.
