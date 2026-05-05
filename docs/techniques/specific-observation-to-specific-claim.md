---
doc_id: fc.technique.specific-observation-to-specific-claim
doc_kind: pattern
status: draft
depends_on:
  - fc.spec-driven-development
derived_from:
  - fc.connection.practitioner-discipline-2026-05-05
relates_to:
  - fc.technique.patch-outdated-artifacts-immediately
  - fc.technique.verify-integration-mechanisms
concepts:
  - verification-discipline
  - acceptance-criteria
  - co-occurrence-vs-causation
---

# Specific Observation to Specific Claim

When marking a probe, spike, or test as PASS, name the exact observation that supports the exact claim. Refuse hand-waves like "verified by direct observation" or "confirmed working" — they do not survive contact with adversarial review, and they propagate co-occurrence as if it were causation.

## Context

A practitioner runs a probe, spike, smoke test, or capability check, and writes a result into a probe doc, runbook, status summary, or PASS/FAIL row in a plan. The result is consumed by future sessions, downstream agents, deploy decisions, and operator hand-offs. Every PASS marker is a load-bearing claim: someone will rely on it without re-running the probe.

## Problem

How do you write PASS markers that survive reuse — that another reader (or your future self) can trust without re-doing the probe — and that fail loudly when the underlying mapping breaks?

## Forces

- **Brevity vs. traceability.** A short PASS marker is easy to write and scan; a long one with full observation→claim mapping is verbose. The traceability is what makes the marker actually trustable.
- **Co-occurrence vs. causation.** Two facts being true at the same time ("recent transcripts exist," "the daemon was running") does not establish that one caused the other. Hand-wavy PASS markers conflate the two by default.
- **Path-dependent chains.** A probe sequence often establishes a chain (capability A → therefore B → therefore C-at-deploy). Each link needs its own observation. Skipping a link silently propagates assumption all the way to deploy-day surprise.
- **Implicit scope.** "Tests pass" without naming the test set conceals what was *not* tested. A reader assumes broader coverage than was actually exercised.

## Pattern

**Refuse hand-wave phrases.** When you find yourself writing any of these, force the substitution:

| Hand-wave | Substitution |
|---|---|
| "verified by direct observation" | "verified by [exact command]; result was [exact output]; this maps to [claim] because [linkage]" |
| "confirmed working" | "ran [input set]; got [outputs]; the [specific behavior] is what was confirmed" |
| "tests passed" | "[test list] passed; coverage includes [X]; not covered: [Y]" |
| "feature works" | "user-flow [exact path] exercised; outputs match [acceptance criterion]" |

**The PASS template.** Three lines, every time:

1. **Observation:** "I ran command X; it returned Y."
2. **Claim:** "This maps to [exactly what is being claimed]."
3. **Linkage:** "[X→Y supports the claim because ...]" — and if the linkage is empty or thin, the PASS is provisional.

**The PASS-WITH-CAVEAT shape.** When the observation supports a *related* claim but not the exact one, mark `PASS-WITH-CAVEAT (observed X but did not test Z)`. This is honest and lets future readers see the gap.

**Path-dependent probes need link-by-link observation.** If the probe establishes "capability A → daemon will work → end-to-end pipeline runs," each arrow needs a direct observation. The temptation is to observe A, then assert B and C as inferences. The trap is that adversarial reality (different cal source, different account, different config) breaks the inference, and the failure surfaces only at deploy time.

**Co-occurrence is not enough.** Transcripts existing while a daemon was "running" does not mean the daemon produced them. (Mobile apps, manual uploads, browser extensions, other accounts can all produce the same artifacts.) Before claiming causation, name the specific evidence that rules out the alternative producers.

## Evidence

**Trace 1 — Phase-0 probe doc, IndigenomicsAI session 2026-05-05.** A probe doc had been pushed to origin with the line: *"§5: Otter ↔ Teams calendar integration ✓ PASS — verified by direct observation."* The actual observation: 5 recent transcripts existed via a list-transcripts tool. None of those 5 transcripts were from the meetings the pipeline was supposed to target — they were from other projects on a different calendar source. The PASS marker had collapsed three distinct claims:

1. *"The auto-join feature exists for some calendar source"* — supported by the observation.
2. *"The auto-join feature works for the pipeline's target calendar"* — NOT supported; required a separate probe that was never run.
3. *"The pipeline will produce transcripts at deploy time"* — NOT supported; depended on (2).

The hand-wave "verified by direct observation" hid the gap. The operator surfaced it later: *"my Otter has not been joining the meetings thus far."* If the PASS marker had used the three-line template, the gap would have been visible at write time and the probe could have been completed properly.

Source memory rule: `feedback_specific_observation_to_specific_claim.md` in the IndigenomicsAI project memory, discovered 2026-05-05.

## Composes with the spec's acceptance criteria

A spec's acceptance criteria are the eval contract (`spec-driven-development.md`). Each criterion deserves a specific-observation-to-specific-claim mapping when checked off. Hand-waved acceptance ("the feature works") undermines the spec's role as the eval contract — the spec is only as strong as the criteria's verification discipline. This pattern is the verification-side complement to the spec's authoring-side discipline.

## See Also

- [Spec-Driven Development](../foundations/spec-driven-development.md) — acceptance criteria are the eval contract this pattern verifies against.
- [Verify Integration Mechanisms Before Recommending](./verify-integration-mechanisms.md) — companion discipline for forward-looking claims (verify before recommending), where this pattern is for backward-looking claims (verify before marking PASS).
- [Patch Outdated Artifacts Immediately](./patch-outdated-artifacts-immediately.md) — when this pattern catches a hand-wave in a previously-shipped probe doc, the patch-immediately rule applies.

## Open Questions

- **Cost calibration.** The three-line template is heavier than a one-word PASS. The right granularity probably scales with claim consequence — high-stakes deploy gates deserve full templates; low-stakes lint passes may not. The threshold is not codified.
- **Linkage formalism.** "Linkage" is currently a free-form natural-language step. A more structured form (e.g., predicate logic, claim-graph edges) might tighten the discipline at the cost of authoring overhead.
- **Adversarial review surface.** This pattern depends on a reviewer (human or agent) who can detect the hand-wave. Without adversarial review, hand-waves still ship. The pattern needs to compose with a review loop to be load-bearing.
