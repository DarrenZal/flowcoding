---
doc_id: fc.methods.governance-architecture
doc_kind: methods
status: active
depends_on:
  - fc.project-vision
  - fc.canon-decision.flowcoding-identity-and-scope
  - fc.canon-decision.methodology-migration-convention
---

# Canon-Bearing Governance — Architecture Overview

This document synthesizes the governance architecture used by Spore (Agent Commons), Intelligence Commons, Poietic Match, and Flow Coding for canon-bearing work. It is the entry point for the `docs/methods/` layer: subsequent method protocols (canon-review, foundational-reframing, corpus-foundational-review, etc.) are specific mechanisms within this architecture.

The architecture was designed in Spore and stress-tested through corpus-foundational-review-v1 (2026-02 → 2026-04-21). Flow Coding is its methodology host going forward (per FC-ADR-0001) — this overview lives here because methodology about canon-bearing practice is Flow Coding's domain.

## The core insight

**Canon is multi-layered, not binary.** The common mental model — "is it canon or not?" — hides the real structure. A canon-bearing repo actually has several distinct layers, each with its own update mechanism, evidence bar, and change-cost profile. Treating all documents uniformly leads either to over-ceremony (expensive review for trivial edits) or under-governance (important decisions made silently).

The learning-membrane metaphor from Spore's vision describes how external material enters the corpus. The governance architecture below describes what happens once material is inside, across the full gradient from intake to active canon to the protocols that govern protocol revision.

## Layer diagram

```
┌─ CANON-BEARING REPO (e.g., Spore, Intelligence Commons, Poietic Match) ──┐
│                                                                           │
│  Tier 1: ADR-eligible canon              ← canon-review-protocol v3       │
│    - project-vision.md                                                    │
│    - foundations/ (Spore, IC) | grammar.md + protocol.md (PM)             │
│    - research/canon-decisions/ (ADRs)                                     │
│                                                                           │
│  Tier 2: Extended scope                  ← foundational-reframing         │
│    - docs/governance/, docs/patterns/, docs/synthesis/ (Spore)            │
│    - docs/learning-field/, docs/patterns/ (IC)                            │
│    - repo-specific extensions                                             │
│                                                                           │
│  Meta-corpus: the protocols themselves   ← FR-20 double-cooling           │
│    - canon-review-protocol, foundational-reframing-protocol,              │
│      corpus-foundational-review-protocol, moratorium-protocol,            │
│      learning-field-intake-protocol, learning-field-protocol,             │
│      frozen-concept vocabulary (concepts-p2p-wiki.yaml)                   │
│                                                                           │
│  Intake layer: research/connections/     ← learning-field-intake-protocol │
│    - bridge notes (external material mapped to canon primitives)          │
│    - research/corpus-review/ (periodic research syntheses)                │
│                                                                           │
│  Planning / tmp: plans, audit, scratch   ← no formal governance           │
│    - ~/.claude/plans/*.md (plan files)                                    │
│    - tmp/ (work-in-progress artifacts)                                    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘

┌─ ANALYSIS-ONLY REPOS (darren-workflow, flowcoding, koi-processor, …) ─────┐
│                                                                           │
│  No canon-bearing status. Own dev cadence + tooling.                      │
│  Path to canon: comparative-intake → bridge note → canon-review round     │
│  in the target canon-bearing repo.                                        │
│                                                                           │
│  Flow Coding also hosts the methodology layer for the architecture above. │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

## What counts as canon per repo

### Tier 1 — ADR-eligible canon

Canon-review-v2 rounds can edit these directly via ADRs in the accepted frame.

- **Spore**: `docs/project-vision.md`, `docs/foundations/`, `docs/research/canon-decisions/`
- **Intelligence Commons**: `docs/project-vision.md`, `docs/foundations/`, `docs/research/canon-decisions/`
- **Poietic Match**: `docs/project-vision.md`, `docs/grammar.md`, `docs/protocol.md`, `docs/research/canon-decisions/`

### Tier 2 — Extended scope (diagnostic + foundational-reframing)

Surveyed in Phase 4 diagnostic passes. ADRs can touch these only if a foundational-reframing proposal authorizes it.

- **Spore**: `docs/governance/`, `docs/patterns/`, `docs/positioning/`, `docs/protocols/`, `docs/synthesis/`
- **Intelligence Commons**: `docs/learning-field/`, `docs/patterns/`
- **Poietic Match**: `docs/phase-0-spec.md` (excluded post-canon-review-v1 reframing), `docs/downstream-products.md` (reclassified to governance — no longer canon)

### Pattern across repos

All three canon-bearing repos share the spine: `project-vision.md` + `docs/research/canon-decisions/`. Spore and IC both use `docs/foundations/` for primitive enumeration. PM uses a flatter `grammar.md` + `protocol.md` structure reflecting its own design conventions. Each repo's Tier 2 layers reflect its specific domain structure.

The 3-repo topology itself was ratified as canon via `reframing-topology-trunk` (Spore ADR-0020), with a **freeze-at-three scale guardrail**. No new canon-bearing repos without another topology reframing. Flow Coding explicitly remains analysis-only per this constraint, even in its methodology-host role.

## Canon update mechanisms (4 layers)

The architecture provides four distinct update mechanisms, each proportionate to the scope and cost of the change involved.

### 1. Ordinary canon edits → `canon-review-protocol` v3

Used for changes within the accepted frame: clause-level edits, clarifications, adjustments to existing canon without dissolving or restructuring anything. Produces ADRs landed in `canon-decisions/`. Session-atomic cross-repo discipline (30-min + 5-min tolerance commit window, author-date basis) for coordinated changes across Spore/IC/PM.

Canon-review-protocol v3 incorporates §§12-14 added during canon-review-v1: constitutional-amendment guard, post-adoption appeal path, vocabulary governance.

### 2. Changes exceeding ADR scope → `foundational-reframing-protocol` v1.1

Used when an ADR alone cannot carry the change. Eight trigger criteria (FR-1): dissolve a concept, rename across the corpus, re-layer a doc, add/remove a canon layer, change canon-scope definition, revise a declared prior, repo-topology change, meta-corpus-protocol revision.

Each reframing authorizes follow-up ADRs via `authorized-by:` trail. Cooling-off 7 days default, 14 days for meta-corpus amendments. Solo-operator override pattern documented.

### 3. Review of the whole corpus → `corpus-foundational-review-protocol` v1

Used periodically when drift accumulates. 9-phase arc: pre-flight → methodology declaration → inventory → deep research → diagnostic → triage → reframing → canon rounds → editorial → propagation/merge. Produces 20 CFR-N rules per v1 harvest.

v1 ran 2026-02 → 2026-04-21 and closed 39/39 findings across Spore/IC/PM with zero rollback.

### 4. Intake of external material → `learning-field-intake-protocol` v1

Used when external artifacts (papers, repos, protocols, products) need to be related to canon. Produces bridge notes in `research/connections/` with R-claim format, concept-slug-aware, disposition-labeled. The path by which external material can eventually inform canon changes via subsequent canon-review rounds.

Paired with `learning-field-protocol-v1` which names the four surfaces and governed membrane.

## Supporting machinery

Three additional pieces govern the mechanics of the four update layers above.

### Moratorium protocol (`moratorium-protocol-v1`)

Suspends canon edits during a corpus-foundational-review. Phase-state table, safety-critical exception categories, approver, audit log.

### Validator (`scripts/validate_spec_dag.py`, Spore)

Enforces doc_kind discrimination (post-F-031 ADR-0012) and `ALLOWED_STATUSES` per doc-kind:
- `doc_kind: decision-record` → `{draft, active, deprecated, superseded}`
- `doc_kind: proposal` → `{draft, cooling-off, eligible, authorized-ADR, executed, closed}`

### Frozen-concept vocabulary governance (`concepts-p2p-wiki.yaml` v2)

Governs ADR `concepts:` references, shared framing notes, and bridge-note R-claim `concept:` values. New slugs require vocabulary admission; silent minting blocked.

## Analysis-only repos and their relationship to canon

Repositories outside the canon-bearing trio are analysis-only. Current examples: `darren-workflow`, `flowcoding`, `koi-processor`. They have their own development cadence and tooling but do not participate directly in canon-review machinery.

### Path to canon from analysis-only repos

External artifacts or patterns in an analysis-only repo can influence canon via:

1. **Comparative intake** (`/comparative-intake` skill) — produces a bridge note in the target canon-bearing repo's `research/connections/`
2. **Bridge note maturation** — accumulated bridge notes on a topic may inform a canon-review round
3. **Canon-review round** — existing canon-review-protocol v3 mechanics then apply

Analysis-only repos cannot be promoted to canon-bearing without a topology reframing under FR-21/FR-22/FR-23 — a foundational-reframing proposal that breaks the freeze-at-three guardrail. This is intentionally high-cost.

### Flow Coding's dual role

Flow Coding is analysis-only like the other peripheral repos, but additionally serves as **methodology host** for the architecture described here. The methodology-host role is orthogonal to canon-bearing status (FC-ADR-0001).

Practically this means:

- Flow Coding does not participate in Spore/IC/PM canon-review rounds
- Flow Coding's own engineering patterns (spec-driven development, flow-state practice, technique cards) are its domain canon, managed with light-touch discipline rather than full canon-review-v2 ceremony
- Future revisions of the method protocols above (v2, v3) land in `flowcoding/docs/methods/` per the attrition-based migration convention (FC-ADR-0002). v1 methods remain at their original Spore locations until individually superseded

## Canonical pointers to method protocols

As of 2026-04-21, method protocols live at these paths. FC-ADR-0002 attrition-based migration relocates them to `flowcoding/docs/methods/` when each is revised to v-next:

| Protocol | Current path | Will migrate to (on v-next) |
|---|---|---|
| `canon-review-protocol` v3 | `spore/docs/research/planning/canon-review-protocol.md` | `flowcoding/docs/methods/canon-review-protocol.md` (when v4 drafts) |
| `foundational-reframing-protocol` v1.1 | `spore/docs/research/planning/foundational-reframing-protocol-v1.md` | `flowcoding/docs/methods/foundational-reframing-protocol.md` (when v2 drafts) |
| `corpus-foundational-review-protocol` v1 | `spore/docs/research/planning/corpus-foundational-review-protocol.md` | `flowcoding/docs/methods/corpus-foundational-review-protocol.md` (when v2 drafts) |
| `moratorium-protocol` v1 | `spore/docs/research/planning/moratorium-protocol-v1.md` | `flowcoding/docs/methods/moratorium-protocol.md` (when v2 drafts) |
| `learning-field-intake-protocol` v1 | `spore/docs/research/planning/learning-field-intake-protocol.md` | Classification deferred — may stay in Spore or migrate to Flow Coding |
| `learning-field-protocol` v1 | `spore/docs/research/planning/learning-field-protocol-v1.md` | Classification deferred — may stay in Spore or migrate to Flow Coding |

## References

- Flow Coding `FC-ADR-0001` — identity and scope
- Flow Coding `FC-ADR-0002` — methodology migration convention
- Spore `ADR-0020` — repo-topology ratification (three-repo shared-canon-hybrid with freeze-at-three guardrail)
- Spore `docs/research/planning/corpus-foundational-review-protocol.md` — v1 harvest doc
- Spore `docs/research/planning/canon-review-protocol.md` v3 — canon edits within the accepted frame
- Spore `docs/research/planning/foundational-reframing-protocol-v1.md` — exceeds-ADR-scope changes
- Corpus-foundational-review-v1 plan: `~/.claude/plans/corpus-foundational-review-v1.md` (CLOSED 2026-04-21)
- Canon-review-learnings-integration plan: `~/.claude/plans/corpus-review-learnings-integration.md` (CLOSED 2026-04-21)
- Flow-coding-methodology-host-declarative plan: `~/.claude/plans/flowcoding-methodology-host-declarative.md` (CLOSED 2026-04-21)
