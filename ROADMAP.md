# flowcoding — Roadmap & Task List

Last updated: 2026-05-15

**How this file is organized:**
- **Currently Active** — workstreams in motion right now
- **Parked Threads** — deferred work with explicit resume conditions
- **Plans** — index of active plan files outside the repo

---

## Currently Active (2026-04-21)

### Methodology-host canon bootstrap
Plan: `~/.claude/plans/flowcoding-methodology-host-declarative.md`
- Establish Flow Coding's methodology-host role in canon structure.
- Open the canon-decisions and methods surfaces.
- Author FC-ADR-0001 and FC-ADR-0002.

### Agentic workflow portability method — DRAFTED (2026-05-15)

Operational trigger: `darren-workflow` Codex / Claude parity pass and OpenAI `migrate-to-codex` audit. Draft method landed at `docs/methods/agentic-workflow-portability.md`. It defines primitive-level translation for agentic coding workflows: inventory source surfaces, classify by primitive, choose conversion operator, preserve safety contract, verify in target runtime. IC owns the intelligence-primitives bridge note (`ic.connection.agentic-workflow-portability`); Flow Coding owns the practitioner method.

Next:
- [ ] Exercise the method against one non-trivial Claude slash command that is not already a shared skill.
- [ ] Add a command coverage table to `darren-workflow` and use it as evidence for method revision.
- [ ] Decide whether this method graduates from draft after a second runtime-pair translation.

## Parked Threads

Each parked thread should name its resume conditions.

### Method protocol v-next drafts (relocated from darren-workflow 2026-04-21)

Relocated here per FC-ADR-0002. These are methodology-host drafts, not domain-canon work.

- **foundational-reframing-protocol v2** — formally codifies FR-13.1 solo-operator-exception rule (currently handled via documented-override pattern). See `~/projects/spore/tmp/phase-6/solo-operator-cooling-off-override.md`.
- **canon-review-protocol v4** — consolidates §§12-14 (constitutional guard, post-adoption appeal, vocabulary governance) into cleaner protocol shape. Current v3 layered these in via ADRs; v4 would rewrite for coherence.
- **corpus-foundational-review-protocol v2** — tightens AC3 (R-claim format verifier design), AC12 (phase-boundary tagging discipline), and codifies the per-slug-branch pattern for parallel Codex execution.

Resume: any of the three when motivated to harvest v3 lessons formally, OR when another corpus-review round is scheduled and v2+ discipline would pay off.

## Plans

| Plan | Path | Status |
|------|------|--------|
| Methodology host declarative bootstrap | `~/.claude/plans/flowcoding-methodology-host-declarative.md` | Active |
