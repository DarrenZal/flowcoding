---
doc_id: fc.canon-decision.methodology-migration-convention
doc_kind: decision-record
status: active
adr_number: "0002"
decision: edit
authored-on: 2026-04-22
authored-by: Darren Zal
---

# FC-ADR-0002 — Methodology Migration Convention

## Context

Spore currently hosts several method documents that govern canon-bearing work across repos, including canon review, foundational reframing, corpus foundational review, moratorium mechanics, and adjacent review protocols. Those documents are load-bearing, but their subject matter is methodology rather than Spore-domain canon.

Moving every existing method document immediately would create unnecessary churn. Existing references already resolve, the validator baseline must be preserved, and Spore ADR-0020 still keeps Flow Coding outside the canon-bearing trio. What is needed now is a stable convention for where future revisions belong, not a risky mass relocation in the same pass.

## Decision

Flow Coding adopts an attrition-based migration convention for canon-bearing methodology.

1. **Future-home rule.** v2+ revisions of canon-bearing method protocols must land in `flowcoding/docs/methods/`.
2. **No-mass-migration rule.** Existing v1 method documents remain at their current locations until a concrete superseding revision exists.
3. **Supersession rule.** When a v2+ method revision lands in Flow Coding, the prior Spore-hosted version is updated to `status: deprecated` and gains `superseded-by:` metadata pointing to the new Flow Coding document.
4. **Stable-reference rule.** Cross-repo references must use `doc_id`, not hard file paths, so relocation can happen without rewriting the citation surface.
5. **Deprecation-stub rule.** After migration, the original Spore location retains a minimal deprecation stub or equivalent validator-safe forwarding surface that directs readers to the Flow Coding version while preserving existing `doc_id` resolution expectations.
6. **Migration-log rule.** As methods migrate, Flow Coding records them in `docs/methods/migration-log.md`.
7. **Topology-trigger rule.** This convention does not itself change repo topology. A separate topology reframing is triggered only when operator judgment deems critical mass reached, with `>=3` migrated methods as the default threshold for reconsidering Flow Coding's analysis-only status.

## Rationale

This convention creates a clean path without breaking the present state.

- It preserves the current working references in Spore.
- It gives future method work an unambiguous home in Flow Coding.
- It separates methodology from domain canon without forcing all past method history to move at once.
- It keeps Flow Coding aligned with FC-ADR-0001: methodology host now, possible topology change later only if separately justified.

Attrition-based migration is the lowest-risk path because it lets each method move when there is actual revision energy, rather than treating relocation itself as the work product.

## Consequences

- New method-protocol revisions about canon-bearing practice belong in `flowcoding/docs/methods/`.
- Existing Spore-hosted v1 method documents remain authoritative until superseded.
- Future migrations must carry both reader-facing forwarding and machine-readable `doc_id` continuity.
- `docs/methods/migration-log.md` becomes the audit trail for the migration program once the first v-next method lands.
- Flow Coding remains analysis-only under ADR-0020 unless a later topology proposal explicitly changes that status.
