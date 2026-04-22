---
doc_id: fc.canon-decision.flowcoding-identity-and-scope
doc_kind: decision-record
status: active
adr_number: "0001"
decision: edit
authored-on: 2026-04-22
authored-by: Darren Zal
---

# FC-ADR-0001 — Flow Coding Identity and Scope

## Context

Flow Coding already had a coherent identity as Coding Commons: a governed body of engineering-practice canon about how humans and AI systems build software together. At the same time, corpus-foundational-review-v1 surfaced another need: canon-bearing repos also need a stable home for the methods that govern canon review, foundational reframing, corpus-wide review, and related process machinery.

Keeping those methods only inside Spore blurs domain canon with methodology. The method documents are about how canon-bearing work is conducted across repos, not about Spore's domain specifically. Flow Coding is the natural home for that work because it already governs practice infrastructure, technique, and learning-field machinery rather than a single domain corpus.

This decision must still respect Spore ADR-0020. The current canon-bearing topology is explicitly frozen at Spore, Intelligence Commons, and Poietic Match. Flow Coding therefore cannot declare itself a fourth canon-bearing repo by implication or drift.

## Decision

Flow Coding is ratified as having a dual role:

1. It remains the governed home of Coding Commons as engineering-practice canon.
2. It is also the methodology host for canon-bearing work: the place where reusable review and reframing methods can be authored, revised, and maintained for use by Spore, Intelligence Commons, Poietic Match, and future canon-bearing repos.

This methodology-host role is declared at the level of Flow Coding's own identity and structure. It governs Flow Coding's self-description and where Flow Coding expects methodology artifacts to live.

This decision does **not** reframe repo topology. Flow Coding remains analysis-only under Spore ADR-0020, and no fourth canon-bearing host is created by this ADR.

## Rationale

The dual-role framing clarifies a distinction that was already present in practice:

- Flow Coding's existing docs govern engineering craft, technique, and learning infrastructure.
- Canon-review and reframing methods are also practice infrastructure, but of a cross-repo governance kind rather than a domain-canon kind.
- Housing those methods in Flow Coding preserves the boundary between domain canon and methodology without requiring immediate migration of existing method files.

This keeps the current topology stable while opening a clean path for future method revisions to land in a more coherent home.

## Consequences

- Flow Coding's `project-vision.md`, canon-decision surface, methods surface, and roadmap may legitimately describe and track methodology-host work.
- Future Flow Coding ADRs may govern Flow Coding's own structure, scope, and migration conventions for methodology.
- Spore, Intelligence Commons, and Poietic Match may reference Flow Coding's methodology decisions voluntarily, but this ADR does not unilaterally bind their canon.
- Any future proposal to make Flow Coding a canon-bearing repo still requires a separate topology reframing that explicitly revisits ADR-0020's freeze-at-three guardrail.
