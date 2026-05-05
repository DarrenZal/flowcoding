---
doc_id: fc.technique.patch-outdated-artifacts-immediately
doc_kind: pattern
status: proposed
depends_on:
  - fc.project-vision
derived_from:
  - fc.connection.practitioner-discipline-2026-05-05
relates_to:
  - fc.spec-driven-development
  - fc.flow-state-practice
concepts:
  - canon-hygiene
  - self-correcting-memory
  - provenance
---

# Patch Outdated Artifacts Immediately

When mid-session you discover that a memory rule, skill description, project context file, plan doc, or shipped artifact you previously authored is now demonstrably wrong, patch it in place at the moment of discovery — not at end of session, not on a TODO list, not "next time I think about it."

## Context

A practitioner working in a long-running practice — across sessions, across projects, with persistent memory and shared canon — accumulates artifacts whose claims must remain accurate to be useful: project CLAUDE.md, memory rules, skill descriptions, plan docs, README files, code comments, runbooks. Every encounter that proves an artifact wrong is a chance to either update the artifact or let staleness accumulate.

In a learning commons, accumulated staleness is a structural problem: future sessions load the wrong claim, and the operator has to detect and surface the gap manually — labor that the artifact's author should have absorbed at the moment of discovery.

## Problem

How do you keep a practitioner's long-lived artifacts (memory, skills, project docs, plans, runbooks) honest as the practice evolves, without forcing the operator to act as a continuous proofreader?

## Forces

- **Patch-now vs. flow continuity.** The current task has its own momentum; pausing to fix an unrelated stale artifact threatens flow.
- **Permission cost vs. coverage.** Some artifacts (global config, committed project files, shared skills) need explicit confirmation before patching; some (personal memory rules, in-progress plans) do not. A flat "always ask first" rule under-corrects; a flat "always patch silently" rule over-reaches.
- **Fix-in-place vs. queue.** A queued fix that lives in chat or a TODO list is a fix that probably never lands. The artifact stays stale until the operator surfaces it.
- **Surfacing vs. silent edit.** For confirmation-required scope, the patch itself is the surfacing — a one-line ask with the patch already drafted, not a vague "we should update X someday."

## Pattern

**Default is patch-now, not "I'll add it to a TODO list."** When you discover an artifact is wrong, the next move is the patch. Wait-to-be-asked is the failure mode.

**Two scopes, two rules:**

| Scope | Examples | Rule |
|---|---|---|
| **Auto-apply** | Personal memory files, runbook corrections in artifacts you're already editing, plan docs authored in the current session, inline code comments contradicted by the code | Patch immediately, no permission ask |
| **Confirmation-required** | Global config, committed project context files, shared skill definitions, vault content, code in repos owned by others | Prepare the patch + surface as a one-line ask: "found §5 of probe doc is wrong; here's the fix; ok to apply?" |

**Cross-link the patch to the discipline rule that should have prevented the original error.** When the original error stemmed from a specific failure mode (e.g., conflating co-occurrence with causation, pattern-matching across services), the patch should reference the discipline rule that names that failure mode — so the artifact carries forward both the fix and the meta-lesson.

**The patch IS the surfacing.** Do not write a separate "things-to-fix" doc and queue the actual edit for later. The fix-and-the-flag are the same act. A separate doc accumulates entries that never become edits.

## Why this matters in a learning commons

The commons is governed by provenance. An artifact whose claims are stale is an artifact whose provenance is silently degrading. Patch-immediately is the load-bearing self-correcting move that keeps the commons alive: every encounter that proves an artifact wrong becomes the moment the artifact gets fixed, rather than another tick on the staleness clock.

The contrast with the alternative is sharp. Without patch-immediately:
- Memory accumulates contradictions; future sessions load the wrong claim.
- Skill descriptions drift from skill behavior; trigger accuracy degrades.
- Project context files describe outdated active threads; orientation work compounds.
- The operator becomes the de-facto proofreader, surfacing gaps the AI should have closed.

With patch-immediately, the commons is closer to a regenerative artifact than a static knowledge base.

## Evidence

**Trace 1 — Hermes-agent borrowed-from rule.** The pattern is borrowed verbatim from hermes-agent's `SKILLS_GUIDANCE` system prompt at `agent/prompt_builder.py:144-177`: *"When a skill is outdated, patch it immediately — don't wait to be asked."* The Hermes investigation surfaced this as the load-bearing self-correcting move that most practitioner setups lack.

**Trace 2 — Self-catch, IndigenomicsAI session 2026-05-05.** A Phase-0 probe doc had been pushed to origin with §5 marked "Otter ↔ Teams calendar integration ✓ PASS — verified by direct observation." The actual observation was: 5 recent transcripts existed for *other* projects on a different calendar source. The probe-doc claim was wrong for the pipeline's actual target meetings. The error sat in the committed doc until the operator surfaced it. The right behavior would have been: at the moment the iterator realized §5 was an inference error (during a downstream investigation), patch the probe doc in place AND link the discipline rule about specific-observation-to-specific-claim mapping.

Source memory rule: `feedback_patch_outdated_artifacts_immediately.md` in the IndigenomicsAI project memory, discovered 2026-05-05.

## See Also

- [Project Vision](../project-vision.md) — Provenance over assertion; reflexivity as a constitutional commitment.
- [Specific Observation to Specific Claim](./specific-observation-to-specific-claim.md) — companion discipline; the failure mode that often produces the staleness this pattern fixes.
- [Spec-Driven Development](../foundations/spec-driven-development.md) — specs are first-class artifacts that benefit most from patch-immediately discipline.

## Open Questions

- **Boundary of "auto-apply" scope.** The list is provisional. As more practitioners adopt the rule, the boundary between auto-apply and confirmation-required will tighten.
- **Cross-session continuity.** Patch-immediately is a within-session discipline. The harder problem — patching artifacts authored in *prior* sessions when discovery happens later — needs structural support (e.g., session-end harvest hooks). Hermes-agent's `Stop`-hook session-harvest pattern is one candidate.
- **Auditability.** A patched artifact loses its prior state unless versioned. Git solves this for committed artifacts; non-committed artifacts (memory files, local plans) need a parallel audit trail.
