---
doc_id: fc.connection.practitioner-discipline-2026-05-05
doc_kind: research
status: draft
depends_on:
  - fc.project-vision
  - fc.flow-state-practice
  - fc.spec-driven-development
relates_to:
  - fc.connection.claude-code
sources:
  - path: ~/.claude/projects/-Users-darrenzal-projects-IndigenomicsAI/memory/feedback_iteration_pattern_step_back.md
    title: "Three iterations on the same axis = step back; the question may be wrong, not the answer"
    type: primary
  - path: ~/.claude/projects/-Users-darrenzal-projects-IndigenomicsAI/memory/feedback_patch_outdated_artifacts_immediately.md
    title: "Patch outdated skills / memory / docs immediately when found wrong"
    type: primary
  - path: ~/.claude/projects/-Users-darrenzal-projects-IndigenomicsAI/memory/feedback_review_prompts_explicit_nothing_to_save_exit.md
    title: "Review/audit prompts must include explicit 'nothing to save' exit clause"
    type: primary
  - path: ~/.claude/projects/-Users-darrenzal-projects-IndigenomicsAI/memory/feedback_check_tools_before_claiming_inability.md
    title: "Check tools before claiming inability"
    type: primary
  - path: ~/.claude/projects/-Users-darrenzal-projects-IndigenomicsAI/memory/feedback_specific_observation_to_specific_claim.md
    title: "Map specific observations to specific claims"
    type: primary
  - path: ~/.claude/projects/-Users-darrenzal-projects-IndigenomicsAI/memory/feedback_verify_integration_mechanisms_before_recommending.md
    title: "Verify integration mechanisms exist in source / docs / account UI before recommending"
    type: primary
  - path: ~/projects/hermes-agent/run_agent.py
    title: "Hermes agent in-session learning loop (counter-based memory/skill nudges, _COMBINED_REVIEW_PROMPT, _summarize_background_review_actions)"
    type: secondary
  - path: ~/projects/hermes-agent/agent/prompt_builder.py
    title: "Hermes MEMORY_GUIDANCE / SKILLS_GUIDANCE — 'patch immediately when outdated' discipline"
    type: secondary
disposition: support existing term
research_subkind: bridge_note
concepts:
  - reflective-practice
  - iteration-discipline
  - reframing
  - canon-curation
  - prompt-engineering
  - tool-discovery
  - verification-discipline
  - anti-overclaim
---

# Practitioner Discipline (2026-05-05) — Flow Coding Bridge Note

Six techniques harvested from a single intensive Claude-Code-driven IndigenomicsAI session (2026-05-04 → 2026-05-05) and validated against a parallel investigation of `hermes-agent`'s in-session learning loop. Each maps a recurring practitioner-discipline failure-mode to a specific technique that, applied as habit, would prevent the failure. This bridge note consolidates the source-citation and the trace-shape for the six derived technique cards under `docs/techniques/`.

Complementary to `fc.connection.claude-code` (the architectural-substrate bridge note for Claude Code's design): that note traces patterns from the Claude Code source itself; this one traces patterns from **a single practitioner's session running on top of Claude Code**, surfaced through the operator's own real-time pushback as the validation signal.

## Artifact profile

**Type:** practitioner-session memory + comparative cross-trace from a second-system investigation.

**Source-1 — IndigenomicsAI session 2026-05-04/05.** ~12-hour single-operator session executing Phases 0–4 of an Otter→Notion meeting-transcript pipeline build. Orchestrator Claude (Opus 4.7) authoring code via subagents; operator (the practitioner) reviewing in-session and pushing back when the orchestrator made discipline-class errors. Each pushback that surfaced a recurring failure-mode was harvested as a memory rule in `~/.claude/projects/-<proj>/memory/feedback_*.md` for future-session protection. Six of these memory rules generalized cleanly to flowcoding's practitioner-discipline scope; two were too narrow (calendar-organizer specific; Indigenous-data-sovereignty meeting-consent specific) and stayed in project-local memory.

**Source-2 — `hermes-agent` investigation (read-only, same session).** A subagent investigated the `hermes-agent` and `hermes-agent-self-evolution` repos to surface adoptable patterns for self-correcting agent practice. The report (preserved at session level; not in flowcoding) revealed that **two of the six harvested techniques have direct precedent in hermes-agent's system prompts and review machinery**: `patch-outdated-artifacts-immediately` is verbatim from hermes' `SKILLS_GUIDANCE`, and `nothing-to-save-exit-clause` is a verbatim port of the closing sentence in hermes' three review prompts (`_MEMORY_REVIEW_PROMPT` / `_SKILL_REVIEW_PROMPT` / `_COMBINED_REVIEW_PROMPT` at `run_agent.py:3025-3058`). These two techniques therefore have a documented secondary trace and arguably qualify for `proposed` status; the others remain at `draft` pending additional traces.

## Why this is technique-card-shaped, not foundation-shaped

Each of the six rules is a **specific move applicable in a specific moment**: "when you would say 'I don't have access to X', do Y instead." That granularity is technique-card territory. None is a load-bearing primitive that would warrant a foundation doc. They compose with the existing foundation docs (`flow-state-practice.md`, `spec-driven-development.md`) rather than replacing or extending them.

## Mapping to technique cards

| Memory rule | Technique card | Status | Hermes trace |
|---|---|---|---|
| feedback_iteration_pattern_step_back | `fc.technique.iteration-axis-step-back` | draft | none |
| feedback_patch_outdated_artifacts_immediately | `fc.technique.patch-outdated-artifacts-immediately` | proposed (was draft) | hermes `SKILLS_GUIDANCE` verbatim |
| feedback_review_prompts_explicit_nothing_to_save_exit | `fc.technique.nothing-to-save-exit-clause` | proposed (was draft) | hermes `_COMBINED_REVIEW_PROMPT` closing line verbatim |
| feedback_check_tools_before_claiming_inability | `fc.technique.check-tools-before-claiming-inability` | draft | none |
| feedback_specific_observation_to_specific_claim | `fc.technique.specific-observation-to-specific-claim` | draft | none |
| feedback_verify_integration_mechanisms_before_recommending | `fc.technique.verify-integration-mechanisms` | draft | none |

## Patterns flagged as NOT bridging into flowcoding

Two source memory rules from the same session were evaluated and excluded:

- **`feedback_verify_organizer_before_proposing_actions`** — calendar/meeting-organizer specific. The generalized shape ("verify provenance before naming agents") is real but the source rule is too narrow to carry as a flowcoding pattern on its own. Already partly covered by `verify-integration-mechanisms.md` and `specific-observation-to-specific-claim.md`. Promote later if a wider trace emerges (e.g. git-ownership / decision-provenance variants).
- **`feedback_third_party_meeting_consent_default`** — Indigenous-data-sovereignty-anchored meeting-consent rule. Generalization ("when collaborator makes open-ended offer, ask rather than propose") is more relational/communication than coding-practice. Out of Coding Commons scope. May revisit if a development-context analog appears (e.g. consent in shared infrastructure).

## Provenance shape and status bar

This bridge note is **draft** (single-session evidence even at the bridge layer). Promotion criteria for the bridge note → `proposed`:
- Second independent practitioner running through the same techniques on a different project, surfacing comparable failure-modes
- OR: an external published source (paper / talk / engineering memoir) corroborating one of the patterns at the same shape

Promotion of individual technique cards from draft → proposed → validated → active follows flowcoding's lightweight technique-card status-enum, NOT spore's canon-review-protocol (flowcoding is analysis-only per `methods/governance-architecture.md`).

## Open questions

1. **ORN claim IDs.** Existing technique cards include `related_claims:` ORN ids from KOI's claims engine. The 6 cards from this intake don't yet — the source memory rules predate any claims engine submission. If/when they're submitted to KOI, backfill the IDs.
2. **Cross-bridge composition.** Two of the six (patch-immediately, nothing-to-save-exit-clause) have a secondary trace into `fc.connection.claude-code` indirectly via the hermes-agent investigation, since hermes-agent and Claude Code share architectural lineage. Worth marking explicitly if the hermes-agent investigation matures into its own bridge note (`fc.connection.hermes-agent`) — at which point the two cards' `derived_from` could become a list of two bridges.
3. **Self-curation flywheel.** Five of the six rules are about self-correcting practitioner discipline in real-time. Together they sketch the substrate of a self-curating practice that's part of what flowcoding's `flow-state-practice.md` Reflect phase points at. Worth considering whether a future foundation-level doc captures this composition rather than leaving it implicit across six technique cards.

## Cross-references

- Composes with `fc.foundation.flow-state-practice` (Reflect phase explicitly)
- Composes with `fc.foundation.spec-driven-development` (verification + acceptance criteria as eval contract)
- Parallel to `fc.connection.claude-code` (architectural lens vs. practitioner lens — different sources, different trace shapes, mutually informing)
