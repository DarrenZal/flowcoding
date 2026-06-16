---
doc_id: fc.connection.sheaf-spec-coherence
doc_kind: research
status: draft
research_subkind: bridge_note
disposition: new connection (speculative; credits established prior art)
depends_on:
  - fc.project-vision
  - fc.spec-driven-development
relates_to:
  - spore.connection.sheaf-substrate-multi-source-synthesis
concepts:
  - sheaf-coherence
  - source-projection-check
  - spec-drift-as-failed-gluing
  - discourse-sheaf-unification
  - higher-order-obstruction
sources:
  - path: "OpenCivics-Labs/civic-intelligence-engine: docs/specs/STRUCTURE.md"
    title: "CIE source-of-truth map (one home per fact-kind; derivation DAG)"
    type: primary
  - path: "OpenCivics-Labs/civic-intelligence-engine: docs/specs/build/check_crossrefs.py"
    title: "CIE cross-reference integrity validator (R->T gates, R->M build-core, Q register)"
    type: primary
  - path: "OpenCivics-Labs/civic-intelligence-engine: docs/specs/build/requirements_data.py"
    title: "CIE requirements model + generators (one source -> CSV + markdown projections)"
    type: primary
  - path: "Goguen & Burstall, Institutions: Abstract Model Theory for Specification and Programming, JACM 39(1), 1992"
    title: "Specifications as a category (the foundational credit)"
    type: reference
  - path: "Goguen, Sheaf Semantics for Concurrent Interacting Objects, MSCS, 1992"
    title: "Objects as sheaves; gluing for system composition"
    type: reference
  - path: "Robinson, Sheaves are the canonical data structure for sensor integration, Information Fusion 36, 2017 (consistency radius: arXiv:1603.01446)"
    title: "Consistency radius = drift metric; H^1 = obstruction to gluing"
    type: reference
  - path: "Hansen & Ghrist, Opinion Dynamics on Discourse Sheaves, arXiv:2005.12798, 2020"
    title: "Sheaf Laplacian as aggregated disagreement (the discourse-sheaf bridge)"
    type: reference
  - path: "Gebhart, Hansen, Schenck, Knowledge Sheaves, arXiv:2110.03789 (PMLR 2023)"
    title: "Sheaf consistency over a knowledge graph (closest structural analog to an artifact graph)"
    type: reference
  - path: "Josh Gibson, Sheaves as a Means of Maintaining Consistency in Model-based Systems Engineering, arXiv:2605.08609, 2026 (verified 2026-06-16)"
    title: "Cellular sheaf over engineering views, Lean-4 verified; nearest prior art — must distinguish"
    type: reference
  - path: "Halley Young, Sheaf-Cohomological Program Analysis: Unifying Bug Finding, Equivalence, and Verification via Cech Cohomology, arXiv:2603.27015, 2026 (verified 2026-06-16)"
    title: "Cohomology-as-inconsistency for program code (the code analog)"
    type: reference
  - path: "Deepak Babu Piskala, Spec-Driven Development: From Code to Contract in the Age of AI Coding Assistants, arXiv:2602.00180, 2026 (verified 2026-06-16); GitHub Spec Kit"
    title: "The practical SDD side — has no categorical/sheaf framing"
    type: reference
  - path: "Tibor Sloboda, Sheaf-Laplacian Obstruction and Projection Hardness for Cross-Modal Compatibility on a Modality-Independent Site, arXiv:2604.07632, 2026 (verified 2026-06-16)"
    title: "Active sheaf-Laplacian-obstruction work — for cross-modal ML, NOT documentation/SDD (adjacent, not the docs application)"
    type: reference
---

# Sheaves and Spec-Driven Development — Bridge Note

**Disposition: speculative synthesis, not discovery.** This note connects Flow Coding's
[spec-driven-development](../../foundations/spec-driven-development.md) foundation to the
sheaf-theoretic machinery used in the operator's discourse-sheaf work (CIE / Spore). The honest verdict
from a 2026-06 literature scan is that **the mathematics is settled and the nearest application landed
~one month before this note** — so this is a *domain transfer + reframing*, written with full credit to
prior art, plus one genuinely-open question that would make it earn its keep. Filed `draft`; promotion
requires the open question below to be answered with real evidence, not just the reframing.

## The mapping (one source, many views = a sheaf)

Flow Coding's SDD foundation already says a spec is the **control plane**: a single governed artifact
from which a build is driven. The modern docs-as-code instance of that is sharper — *one source of
truth, many **generated projections** (SDK, rendered API docs, READMEs, an agent "constitution"), and
**cross-reference validators** that fail the build on drift.* That shape is exactly a **cellular sheaf
over the artifact dependency graph**:

- **Base / sites** = the artifacts and their shared interfaces (the places two artifacts must agree).
- **Restriction maps** = the generators (source → projection) *and* the cross-artifact consistency
  relations ("this doc section must match that schema field").
- **A global section** = a coherent spec-set: every artifact agrees on every overlap.
- **Drift** = a failed gluing condition — local data that cannot be glued into a global section, i.e. a
  **nonzero class in the relevant (Cech/cellular) cohomology**.
- **Consistency radius** (Robinson) = a *continuous* drift metric, in place of a binary
  lint pass/fail: "how far is this artifact-set from coherent?"
- **Existing linters** (Spectral, link-checkers, contract tests) = a **coarse, hand-built approximation
  of the H^0 / coboundary check** — which both explains *why they work* and predicts *what they miss*.

The bridge to the operator's discourse-sheaf thread is that this is *literally the same machinery*
(Hansen–Ghrist sheaf Laplacian; Knowledge Sheaves) pointed at an artifact graph instead of an
opinion/knowledge graph. "Private opinion → public discourse" becomes "source-of-truth → projected
artifact"; "neighbors agree in discourse space" becomes "artifacts agree on shared fields." **One
Laplacian, two domains** (civic opinion *and* spec coherence) is the defensible original framing here.

## Prior art — credited up front (so this claims nothing it shouldn't)

Every theorem this would use is done, in adjacent domains:

- **Specs as a categorical object:** institution theory (Goguen & Burstall 1992) already treats a body
  of specification artifacts categorically — signatures form a category, theories are objects,
  structuring is colimit, refinement is morphism. **Goguen's sheaf semantics** (1992) already does
  "objects as sheaves" with gluing for composition.
- **Structure-preserving projection between artifacts:** Spivak's functorial data migration (categorical
  databases) — schemas as categories, migrations as adjoint functors that provably preserve constraints.
- **Drift = cohomological obstruction:** Robinson's sheaves-for-data-integration *is* the
  consistency-radius / H^1-obstruction formalism, with tooling (PySheaf).
- **The discourse-sheaf lineage:** Hansen–Ghrist (2020) and Knowledge Sheaves (2023) — the
  sheaf-Laplacian-as-disagreement, which is the direct CIE/Spore connection.
- **The must-distinguish nearest neighbors (2026):** Gibson 2026 does the *cellular-sheaf-over-an-
  artifact-graph* construction for MBSE engineering views (Lean-4-verified); Young 2026 does
  sheaf-cohomology-as-inconsistency for program code. Neither targets the SDD / docs-as-code / OpenAPI /
  linter world — but they are close enough that this note is *timely synthesis, not frontier*.

What is **not** prior art: the modern, practical **SDD/docs-as-code** developer workflow (Spec Kit,
OpenAPI-as-source, "make drift a build failure") has, as of the Feb-2026 SDD survey, *no* categorical or
sheaf framing at all. The two worlds — sheaf-consistency math and practical SDD — remain unconnected.

## What's actually unoccupied (the narrow, real slice)

1. Casting the practical SDD pattern (generated projections + cross-ref linters) as the sheaf above, with
   **linters as degenerate cohomology checks** — which reframes existing dev tooling rather than
   inventing math.
2. The **cross-domain unification** with discourse sheaves: the same sheaf-Laplacian governs civic
   opinion coherence (CIE) and spec-artifact coherence (Flow Coding). That is a Coding-Commons-native
   observation precisely because Flow Coding is the practitioner layer where Spore/IC abstractions meet
   real development.

## The open question (where this earns promotion)

The caveat *is* the research direction: **if every restriction map is just a projection and the base is
discrete, the sheaf collapses to "check all pairwise field equalities" — which is what linters already
do.** Then the cohomology is trivial and this is "mathematical dress on `grep`." The note earns
promotion to `active` only by answering:

> **Where in a real spec do higher-order (3-way, cyclic) obstructions actually occur — coherence
> failures that no pairwise validator can catch, but a cohomology computation can?**

Candidates to test: a fact generated into three artifacts under two different transforms (a 2-cocycle
that's locally-consistent pairwise but globally impossible); cyclic generation dependencies (A's
projection feeds B's source feeds A); a thresholds/constants file compiled into multiple lanes where
pairwise checks pass but a triangle disagrees. If none exist in practice, the honest disposition is
"reframing only, do not promote."

## Evidence (real development work)

The CIE spec is a concrete instance of the source·projection·check pattern and supplies the first
evidence:
- `STRUCTURE.md` is, in this language, a partial **specification of the sheaf**: it names one canonical
  home per fact-kind (the sources) and the derivation DAG (the restriction structure).
- `check_crossrefs.py` is a **degenerate H^0 check**: it verifies that every R/T/M/Q reference resolves
  and every requirement→gate/build-core edge exists — pairwise coboundary checks, exactly the linter-as-
  H^0 claim, and exactly the level at which the cohomology is currently trivial.
- The **Notion↔git drift incident** (2026-06: a condense pass dropped the requirements table from the
  Notion projection while the git source kept it) is a real **failed-gluing event** — and notably one
  that *no existing validator caught*, because the two artifacts were hand-maintained twins rather than
  source-and-generated-projection. That is the empirical hook: it's a coherence failure that a
  sheaf-aware "is this artifact a generated section or an independent source?" check would flag by
  construction.

## Claims (for the learning field — contestable)

1. The docs-as-code SDD pattern (one source, generated projections, cross-ref validators) is a cellular
   sheaf over the artifact graph; coherence = a global section. *(reframing; credit Robinson, Gibson)*
2. Spec drift is a failed gluing condition; the consistency radius is a continuous drift metric superior
   to binary lint pass/fail. *(speculative; no SDD empirical evidence yet)*
3. Existing linters are coarse, pairwise approximations of the H^0/coboundary check — which explains
   their successes and bounds their blind spots to higher-order obstructions. *(testable)*
4. A hand-maintained "projection" is not a projection but a second source; two sources of one fact always
   drift. This is the practical, sheaf-free takeaway and the strongest immediately-useful claim.
5. The sheaf-Laplacian that measures civic-opinion disagreement (discourse sheaves) and the one that
   measures spec-artifact incoherence are the same operator on different graphs. *(original framing)*
6. The whole construction is only non-trivial where genuine higher-order obstructions exist; absent
   those, it is reframing, not method. *(the promotion gate)*

## Status & caveats

- **`draft` / speculative-programmatic.** No empirical evidence yet that real SDD drift is well-modeled
  by cohomology or that consistency radius beats existing CI checks.
- **The fresh window is thin.** Gibson 2026 (MBSE engineering views) and Young 2026 (program code) are
  the nearest neighbors, and the sheaf-Laplacian-obstruction machinery is under active development (e.g.
  Sloboda 2026, arXiv:2604.07632 — but for *cross-modal ML* compatibility on a "modality-independent
  site," **not** documentation). As verified 2026-06-16, **no published paper yet names the documentation /
  SDD-artifact-coherence application** — which is exactly what keeps the slice open. (An earlier research
  pass mis-claimed that arXiv:2604.07632 mentions "documentation consistency in software systems"; it does
  not — corrected here.)
- **2026 references verified 2026-06-16** — titles, authors, and arXiv IDs confirmed against the arXiv
  abstract pages (Gibson 2605.08609, Young 2603.27015, Piskala 2602.00180, Sloboda 2604.07632). Gibson and
  Young remain the must-distinguish baselines; re-scan at any external-publication time, since the 2026
  arXiv space moves fast.
- Promotion to `active` requires answering the open question with a real, found higher-order obstruction
  — not the reframing alone.
