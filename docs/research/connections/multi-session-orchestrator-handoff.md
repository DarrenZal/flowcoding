---
doc_id: fc.connection.multi-session-orchestrator-handoff
doc_kind: research
status: draft
depends_on:
  - fc.project-vision
  - fc.methods.governance-architecture
  - fc.connection.claude-code-flow-coding
relates_to:
  - spore.connection.canon-rebuild-arc-method-retrospective
sources:
  - path: projects/claude-code/src/services/SessionMemory/sessionMemory.ts
    title: "Claude Code Session Memory subsystem (per-conversation extraction)"
    type: primary
  - path: projects/claude-code/src/memdir/paths.ts
    title: "Claude Code auto-memory / memdir subsystem (cross-session memory store)"
    type: primary
  - path: projects/claude-code/src/schemas/hooks.ts
    title: "Claude Code hook schema (HooksSchema, HookMatcherSchema)"
    type: primary
  - path: projects/claude-code/src/utils/hooks/hooksConfigManager.ts
    title: "Claude Code hook event semantics (PreCompact / SessionStart matchers, stdout behavior)"
    type: primary
  - path: projects/claude-code/src/utils/swarm/teamHelpers.ts
    title: "Claude Code agent teams (TeamFile schema, addressing, mailbox)"
    type: primary
  - path: projects/claude-code/src/tools/AgentTool/AgentTool.tsx
    title: "Claude Code sub-agent dispatch (Agent tool internals)"
    type: primary
  - path: projects/claude-code/src/utils/worktree.ts
    title: "Claude Code worktree mode (filesystem isolation primitive)"
    type: primary
  - path: projects/hermes-agent/agent/context_compressor.py
    title: "Hermes structured compression with handoff framing"
    type: primary
  - path: projects/hermes-agent/hermes_state.py
    title: "Hermes session schema (parent_session_id chains, FTS5 search)"
    type: primary
  - path: projects/hermes-agent/agent/memory_manager.py
    title: "Hermes three-layer memory architecture"
    type: primary
  - path: projects/openclaw-upstream/src/agents/compaction.ts
    title: "OpenClaw three-tier compaction fallback"
    type: primary
  - path: projects/openclaw-upstream/extensions/memory-core/src/flush-plan.ts
    title: "OpenClaw memory-flush turn before compaction"
    type: primary
  - path: projects/openclaw-upstream/VISION.md
    title: "OpenClaw roadmap guardrail rejecting nested-hierarchy default"
    type: primary
  - url: https://code.claude.com/docs/en/agent-teams
    title: "Claude Code agent teams documentation"
    type: secondary
  - url: https://code.claude.com/docs/en/sub-agents
    title: "Claude Code sub-agents documentation"
    type: secondary
  - url: https://code.claude.com/docs/en/agent-sdk/sessions
    title: "Claude Agent SDK sessions documentation"
    type: secondary
  - url: https://openai.github.io/openai-agents-js/guides/multi-agent/
    title: "OpenAI Agents SDK — agents-as-tools and handoffs (combinable)"
    type: secondary
  - url: https://openai.github.io/openai-agents-js/guides/handoffs/
    title: "OpenAI Agents SDK — handoffs as runtime-mediated agent-to-agent delegation"
    type: secondary
  - url: https://docs.langchain.com/oss/python/langchain/multi-agent/index
    title: "LangChain multi-agent (subagents, handoffs, routers, skills, custom workflows; mixable)"
    type: secondary
  - url: https://docs.crewai.com/en/learn/hierarchical-process
    title: "CrewAI hierarchical process (manager-coordinated workflow)"
    type: secondary
  - url: https://microsoft.github.io/autogen/stable/user-guide/agentchat-user-guide/selector-group-chat.html
    title: "AutoGen SelectorGroupChat (horizontal collaboration with centralized turn-selection)"
    type: secondary
  - url: https://claude.com/blog/the-advisor-strategy
    title: "Anthropic advisor strategy (cost-tier inversion, single-request)"
    type: secondary
  - url: https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns
    title: "Microsoft Azure AI agent orchestration patterns"
    type: secondary
  - path: spore/docs/research/connections/canon-rebuild-arc-method-retrospective.md
    title: "Spore canon-rebuild arc method retrospective (operational case study)"
    type: primary
  - path: spore/tmp/multi-session-orchestrator-research-2026-04-25.md
    title: "Pre-bridge-note research synthesis (this document's evidence basis)"
    type: primary
disposition: propose new
research_subkind: bridge_note
concepts:
  - multi-session-orchestration
  - handoff-protocol
  - session-role-stratification
  - decision-gated-plan-structure
  - audit-then-propose
  - hook-driven-handoff-automation
  - orchestrator-executor-topology
  - structured-handoff-artifact
---

# Multi-Session Orchestrator-Handoff Pattern — Bridge Note

A working pattern for AI-assisted long-range work: how a single multi-day project (canon-rebuild, large refactor, research arc) sustains coherent execution across many separate Claude Code sessions when each session has a finite context budget and the work has no natural completion within that budget.

This note synthesizes (a) **operational validation** from the Spore canon-rebuild arc 2026-04-22 → 2026-04-25 (29 ADRs across five repos; 10 Agent-tool orchestrations on 2026-04-25 alone; multiple successful cross-session handoffs; zero rollbacks) and (b) **comparative analysis** of Hermes-agent, OpenClaw, Claude Code internals, and 2026 industry agent-orchestration patterns. The result is a structured pattern for both **within-session optimization** and **cross-session continuity**, plus a critical tooling opportunity (hook-driven handoff automation) identified for follow-on work.

This is the methodology-host bridge-note for the orchestrator-handoff pattern. It is the methodology counterpart to `spore.connection.canon-rebuild-arc-method-retrospective`, which documents the same arc as a Spore-canon-method retrospective.

---

## Artifact Profile

- **What's being documented**: a reusable orchestration pattern that emerged across the Spore canon-rebuild arc and is generalizable to long-range AI-assisted projects.
- **Evidence base**: see Evidence Ledger immediately below.
- **Scope**: orchestration discipline, not domain content. This pattern is about *how* sessions coordinate, not *what* the work produces.
- **Non-goals**: (a) within-session prompt engineering for a single task, (b) production deployment patterns for autonomous agents, (c) economic / cost optimization of agent fleets.
- **Disposition**: `propose new` — the *composition* is novel; individual elements are sourced from prior work and cited.

---

## Evidence Ledger

| Item | Value | Source |
|------|-------|--------|
| Canon-rebuild arc duration | 2026-04-22 → 2026-04-25 (4 days) | retrospective:597 |
| ADRs landed across the arc | 29 (chain `0044-0058`, `0059a`, `0061-0077`) | retrospective:597 |
| Sibling repos touched | 5 (Spore, IC, PM, koi-processor, darren-workflow) | retrospective + Spore CLAUDE.md |
| Agent-tool orchestrations on 2026-04-25 | 10 + 2 direct-edit palate-cleansers + 4 housekeeping commits | retrospective:583 |
| Tactical successor sessions on 2026-04-25 | Multiple (one strategic-tier orchestrator + tactical successors that produced the three afternoon/evening/next-session handoff artifacts; specific successor session IDs not all preserved in CLAUDE.md Session History) | Spore CLAUDE.md "Session History" + `tmp/handoff-prompt-2026-04-25-*.md` |
| Cross-session handoffs on 2026-04-25 | 3 (afternoon, evening, next-session pickups) | `tmp/handoff-prompt-2026-04-25-*.md` |
| Handoff artifact size range (measured) | 115–310 lines / 1,057–3,390 words / ~1,400–4,500 tokens estimated | `wc -l -w tmp/handoff-prompt-*.md` |
| Rollbacks across the arc | 0 | retrospective:22 |
| Sample session-atomic timings (Step 3 → Step 7.5) | spore:ADR-0072 sub-2-min; spore:ADR-0067 258s; spore:ADR-0066 307s; spore:ADR-0073 (F1) ~5 min; spore:ADR-0075 (F6) 8m21s; spore:ADR-0076 (F5) 122s | retrospective + Spore CLAUDE.md |

These are the figures the rest of the doc cites. Where claims reference a specific count or timing, they trace back to a row above.

---

## §1 Pattern definition

This pattern is a **single-orchestrator-at-a-time, artifact-mediated** orchestration mode. One human operator coordinates multiple AI sessions stratified by role; structured artifacts (handoff prompts, CLAUDE.md, decision-briefs) carry context across the boundaries between them.

### Three-tier role stratification

| Tier | Role | Context-budget profile | Primary artifact |
|------|------|------------------------|------------------|
| **Strategic** | Multi-day arc continuity; canonical narrative curation | Medium; replenished via /compact + handoff | `CLAUDE.md` (curated narrative) |
| **Tactical** | Within-day session pickup; child dispatch; ADR landing | High; compacted at ~70–80% utilization | `tmp/handoff-prompt-*.md` |
| **Execution** | Single-task focused; one ADR or one investigation | Self-contained per-dispatch | Decision-brief + close-out manifest |

A tier is a *responsibility*, not necessarily a distinct session ID; the strategic tier may live across days in one Claude Code session that periodically /compacts, while the tactical tier turns over multiple times within a day. The operator chooses when to span tiers within a single session and when to fork.

The **operator** moves between sessions, not within them. Sessions of the same tier across days are *successors* (linked via handoff artifact), not *parallel* peers.

### Operating doctrine, not industry taxonomy

The pattern is best framed as an *operating doctrine this pattern intentionally chooses*, not as a categorical taxonomy of how multi-agent systems must be organized. Public agent frameworks treat hybrids as normal:

- **OpenAI Agents SDK** distinguishes "agents-as-tools" (manager keeps control) from "handoffs" (specialist becomes active) and explicitly says the two patterns can be combined — e.g., a triage agent handing off to a specialist that then uses other agents as tools.
- **LangChain** lists subagents, handoffs, routers, skills, and custom workflows, and explicitly says the patterns can be mixed.
- **CrewAI** documents a hierarchical process with a manager agent — hierarchy itself is not pathological; only *unbounded nested* hierarchy is.
- **AutoGen `SelectorGroupChat`** is horizontal collaboration with *centralized turn-selection*, a clean counterexample to a strict vertical-vs-horizontal binary.

The choice this pattern makes — **single-orchestrator + artifact-mediated handoff + flat one-direction dispatch** — is well-suited to long-range coherent work where sustained narrative matters and where every state transition wants to be human-auditable. It is not the only legitimate choice. **Mixing modes is acceptable only when three boundary conditions are explicit**:

1. **Synthesis owner**: who integrates results into a single coherent narrative
2. **Operator-facing speaker**: who reports back to the human (exactly one session at any moment)
3. **Handoff mechanism**: runtime-mediated message passing vs artifact-mediated cross-session continuity (the two require different audit disciplines and should not be silently composed)

Without these three explicit, hybrids degrade into the failure modes catalogued in §5.

### Message-flow invariant

Within this pattern, message flow is one-direction at the dispatch layer:

```
operator ↔ orchestrator → child → orchestrator → operator
```

The operator and orchestrator have a bidirectional relationship (questions, ratification, status updates). The child is single-shot: it receives a comprehensive prompt and returns a result. **Children do not dispatch siblings. Orchestrators do not message peer orchestrators.** Handoff between orchestrator successors is artifact-mediated, not runtime-mediated.

This invariant avoids the **infinite handoff loop** identified in 2026 production surveys as the top failure mode for multi-agent systems: A→B→C→A patterns where each agent re-plans with partial context, accumulating drift.

---

## §2 Within-session optimization

These disciplines maximize per-session output before handoff is needed.

### 2.1 Decision-gated plan structure

A non-trivial unit of work (e.g., one ADR admission) is structured as Steps 0–7.5, with the **session-atomic window** containing only Steps 3–7.5:

| Step | Phase | Cost | Inside session-atomic window? |
|------|-------|------|-------------------------------|
| 0 | Audit current state | Cheap (reads only) | No |
| 0.5 | Audit existing artifacts for pre-bake / drift | Cheap (audit-then-propose) | No |
| 1 | Surface decision options (3–10 axes) | Cheap (synthesis) | No |
| 2 | Operator ratifies disposition | Cheap (single decision-brief turn) | No |
| 3 | Draft commit (atomic-bundle of allowlist files) | Mechanical | **Yes** |
| 4 | Status-flip commit (`status: draft` → `active`) | Mechanical | **Yes** |
| 5 | Push to origin | Mechanical | **Yes** |
| 6 | Validator + AC verification | Cheap (deterministic) | **Yes** |
| 7 | Cross-repo zero-change verification | Cheap (deterministic) | **Yes** |
| 7.5 | Close-out manifest | Brief synthesis | **Yes** |

**Primary value: rollback integrity and auditability.** The retrospective puts it directly: "Its practical outcome was not merely speed. It protected rollback integrity because the argument was settled before files started moving" (`retrospective:191`). When all argumentation is resolved before the commit window opens, the file changes inside the window are mechanical, narrowly-scoped, and reviewable as a unit. If something goes wrong, the rollback target is unambiguous.

**Secondary value: speed where it can be measured.** Pushing audit + decision-brief outside the window does shorten the ceremonial portion to just the mechanical operations. Measured timings on 2026-04-25: spore:ADR-0072 sub-2-min; spore:ADR-0067 258s; spore:ADR-0066 307s; spore:ADR-0076 (F5) 122s. The sub-minute and low-single-digit-minute tail is achievable for routine ADRs; F-series foundation-doc admissions sit higher (5–8 min) because the in-window artifact authoring is genuinely larger.

### 2.2 Audit-then-propose

A child agent dispatched to author a plan **must audit existing canon at Step 0.5 before proposing taxonomy or disposition**. The audit catches pre-baked misdirection in the plan's Context section.

**Operational validation (2026-04-25)**: the spore:ADR-0071 plan implicitly framed the koi-processor projection-script as drifted to flat-8 projections (per pm:ADR-0014's parking note). Step 0.5 audit revealed the actual drift was prose-level not code-level — the script implements Epistemic-primary intake correctly; canon prose overclaims its scope. A pre-baked plan would have invented a fix for nothing.

This discipline applies whether the agent is a fresh sub-agent dispatch or the operator's tactical session itself.

### 2.3 Comprehensive single-shot subagent prompt

Sub-agent dispatches use **comprehensive prompts (~500–700 lines)** containing full context, decision-brief structure, and explicit step boundaries. The child receives no parent conversation history.

This trades upfront prompt-authoring cost for elimination of multi-turn drift. Validated across the 10 Agent-tool orchestrations on 2026-04-25 with zero rollbacks. Aligns with Claude Code sub-agents documentation: *"the subagent does that work in its own context and returns only the summary."*

### 2.4 N-round review cap with known-ceiling acceptance

Plan review (e.g., `/review-plan` with Codex) is capped at **2 rounds** by default. After round 2, accept the known-ceiling state and proceed. Diminishing returns hit hard around round 3+; further iteration produces stylistic adjustments, not structural improvements (`retrospective:419-423`).

For mechanical-cleanup plans the ceiling is closer to 8 rounds (more lint-style picks possible). For semantic plans, 2 rounds is the right dose.

### 2.5 Parallel tool calls for independent work

Independent tool calls in a single turn are dispatched in parallel (one assistant message, multiple tool blocks). Empirically validated for Bash + Read combinations, multiple WebFetch URLs, and parallel Explore agents on independent repos. Not applicable when calls have dependencies.

### 2.6 Worktree isolation for parallel filesystem dispatch

Claude Code's `Agent` tool supports `isolation: "worktree"`, which creates a temporary git worktree copy for the child's filesystem (`AgentTool.tsx:90-100`, `:582-593`; `worktree.ts:902-951`). This **prevents simultaneous working-tree mutation races** when multiple children might write to the same checkout — it does **not** prevent logical merge conflicts when multiple agents touch overlapping files in their own worktrees.

If worktree mode is used for ≥3 truly-parallel child dispatches, the protocol requires:

- A designated **merge owner** (the dispatching orchestrator) who reintegrates worktrees in a defined order
- A **conflict-handling rule** (e.g., last-write-wins is unacceptable; conflicts must surface and be resolved with operator input)
- Per-child **close-out manifests** that enumerate the files each child touched, so the merge owner can verify scope before reintegration
- A **validation gate** (e.g., Spore validator 9/30 baseline) re-run after reintegration

The Spore canon-rebuild arc did not exercise worktree mode — sequential dispatch was sufficient. The primitive is in the toolkit; reintegration discipline must accompany its first real use.

---

## §3 Cross-session continuity

These disciplines preserve coherent execution across the session boundary.

### 3.1 Structured handoff artifact

A **handoff prompt (markdown, measured size 115–310 lines)** is the canonical cross-session continuity primitive. Format inherited from operational practice and Hermes structured compression (`hermes-agent/agent/context_compressor.py:7-18`, `:38-49`):

```markdown
# Handoff prompt — <YYYY-MM-DD afternoon|evening|next-session>

## Handoff metadata
- Source session UUID: <id>
- Parent session UUID (if known): <id>
- Source artifact path: <path-to-handoff-author's-tactical-state>
- Target tier: <Strategic | Tactical | Execution>
- Created at: <ISO timestamp>
- Source repo SHAs: <repo>:<sha> for each canon-bearing repo

## Status snapshot
- Spore HEAD: <SHA> (pushed | unpushed)
- IC HEAD: <SHA> (untouched | edited)
- ...

## Active task
[Single-sentence what the next session should pick up]

## Resolved questions
[Decisions already made — don't relitigate]

## Pending questions
[Operator-input still needed]

## Remaining work
[Concrete next steps; framed as observations, not instructions]

## Discipline reminders
[Project-specific rules: no `git add -A`, validator-baseline, etc.]

## Reference artifacts
[Relevant tmp/ files, plans, ADR drafts in flight]
```

**Measured local sizes** (five handoff artifacts spanning 2026-04-24 through 2026-04-26): 115–310 lines, 1,057–3,390 words, roughly 1,400–4,500 tokens estimated. Substantially cheaper than full transcript forwarding (per-session transcripts on multi-hour days run tens of thousands of tokens), but a multiplicative factor against full-transcript forwarding has not been measured locally and is not claimed here.

The compression is intentional: a fresh-session start with a structured summary outperforms session-resume-with-full-history when the next session's task is well-defined. (Session-resume is the right tool when the next task is *recovery from interruption*, not *next phase of work*.)

**Adoption from Hermes**: prepend a `[CONTEXT COMPACTION — REFERENCE ONLY]` framing header so the receiving session treats the content as background, not active instructions. Hermes' `context_compressor.py:38-49` is the verified source for this template; the rationale (treat compressed content as background data, not commands) is present in the code header.

**Adoption from Hermes — parent_session_id chains**: Hermes' state schema links compression-split sessions via `parent_session_id` (`hermes-agent/hermes_state.py:5-13`, `:41-70`). The "Handoff metadata" block above adopts this discipline at artifact-layer: every handoff carries the source session UUID, optional parent session UUID, source artifact path, target tier, created_at timestamp, and source repo SHAs. This makes lineage traversable without database support.

### 3.2 Step 0 handoff audit

A receiving session **does not consume the handoff as authoritative on first read**. Step 0 of any pickup is a verification pass:

- **Repo HEAD verification**: each canon-bearing repo's current HEAD matches what the handoff metadata recorded, or the divergence is explicit and explained
- **Dirty status check**: working tree is in expected state (clean, or has expected uncommitted changes named in the handoff)
- **Active task currency**: the named active task is still the right next move — operator may have changed direction since the handoff was written
- **Referenced artifact existence**: every `tmp/` file or plan path cited in the handoff actually exists at the cited path
- **Post-handoff operator changes**: the operator may have made commits or edits between handoff-write and session-start; check `git log` and recent file mtimes
- **Expiry check**: if the handoff is older than ~24h, treat it as suggestive rather than authoritative; verify everything

If verification fails, **stop and ask the operator** rather than proceed on a stale handoff. Step 0 audit is the cross-session counterpart to §2.2 audit-then-propose: just as the Step 0.5 audit catches pre-baked plan misdirection, the Step 0 audit catches pre-baked handoff misdirection.

### 3.3 CLAUDE.md as continuity narrative

The strategic-tier session curates a running narrative in `CLAUDE.md` ("Current Status" + "What's Done" + "What's Left" + "Session History"). This is updated at /end of each tactical session with that session's contributions.

Claude Code provides two distinct memory subsystems that the CLAUDE.md narrative composes with:

- **Session Memory** (`claude-code/src/services/SessionMemory/sessionMemory.ts:1-5`, `:76-82`; gated by `tengu_session_memory`): a per-conversation markdown file extracted by a background subagent. Scoped to a single conversation; survives `/compact` separately from transcript compaction.
- **Auto-memory / memdir** (`claude-code/src/memdir/paths.ts:21-77`, `:207-258`; gated by separate settings + `tengu_passport_quail` for extract mode): a cross-session memory store keyed by sanitized git root. Path: `<memoryBase>/projects/<sanitized-git-root>/memory/` with `MEMORY.md` as entrypoint.

The two layers compose: `CLAUDE.md` is the operator-curated project narrative; Session Memory captures per-conversation extraction; auto-memory/memdir holds durable cross-session facts.

### 3.4 Source-of-truth hierarchy

Multiple memory surfaces exist; they can diverge silently. The protocol defines which surface wins for which kind of state:

| State | Authoritative surface | Fallback / sync expectation |
|-------|-----------------------|-----------------------------|
| Strategic narrative (project status, what's done, queue) | `CLAUDE.md` | Updated at /end; auto-memory may extract reflections of it |
| Active task (next concrete move) | Latest `tmp/handoff-prompt-*.md` (when between sessions); current orchestrator session's working state (when in-session) | Step 0 audit verifies handoff against current repo state |
| Durable facts about user / project / conventions | Auto-memory / memdir `MEMORY.md` | Operator can edit directly; Claude Code may extract additions |
| Per-conversation context | Session Memory (Claude Code subsystem) | Session-ID scoped at `{projectDir}/{sessionId}/session-memory/`; survives compaction; not a project-level cross-session authority |
| Scratch / work-in-progress | `tmp/` in canon-bearing repo, or `~/.claude/plans/` | No durability guarantee; do not rely as canonical |

When two surfaces conflict, the operator-curated surface wins over the Claude-extracted surface; the most recent surface wins over older surfaces; the canon-bearing-repo surface wins over the operator-private surface.

### 3.5 Dirty-tree and explicit-staging discipline

Cross-session safety requires that working-tree state be **legible at handoff time**:

- Tracked changes outside an active ADR's allowlist surface as a Step-4 tripwire and pause for operator ratification rather than silent inclusion
- Staging is **always explicit**: never `git add -A` or `git add .`; always name files
- Untracked accumulation (manifests, screenshots, scratch) is acknowledged in the handoff if it could affect a successor's audit — otherwise the successor's Step 0 audit may flag false drift

The retrospective frames this as more than git hygiene: "if the staging discipline is loose, the intellectual method becomes unauditable" (`retrospective:417`).

### 3.6 Claude Code implementation notes

These are operational details for Claude Code specifically, not protocol pillars. They will likely change as Claude Code evolves; protocol shape does not depend on them.

- **ID-based addressing post-completion**: when a sub-agent dispatch completes, the agent's name (e.g., `f6-failure-modes`) is no longer addressable by `SendMessage`; only the agent's UUID persists via transcript-resume. Capture child UUIDs at Step 2 if continuation messages are anticipated. (Per `retrospective:587`, this is "an operational reminder for running ten-dispatch days cleanly," not a canon-method-layer discipline.)
- **Sandbox plan-file fallback**: when sandbox denies writes to `~/.claude/plans/`, child agents fall back to `tmp/decision-brief-*.md` per spore:ADR-0072 precedent. The plan content lands in the canon-bearing repo's tmp/ rather than operator-private state.

---

## §4 Compositional patterns (multi-session arc)

### 4.1 Cognitive-load-optimized child sequencing

When dispatching multiple children in sequence within one tactical session, **invention-heavy work goes first** while cache is warm; synthesis-heavy work goes later when the orchestrator has more substrate.

**Operational validation (2026-04-25)**: Tier B inverted the scoping plan's F3→F5→F6 default to **F6→F5→F3**. F6 (failure-modes — net-new taxonomy authoring) ran first; F3 (actor-governance — synthesis weaving four prior ADRs) ran last with maximally-warm cache. All three landed cleanly.

This is the multi-session analog of "easy parts first" — but optimized for substrate-availability, not cognitive ease. Later dispatches inherit richer substrate as earlier dispatches' results land.

### 4.2 Operator-mediated cross-session consultation

Two orchestrator sessions can consult each other indirectly: the operator pastes a question from session N into session N-1's terminal, gets a recommendation, pastes it back into session N.

**Operational validation (2026-04-25, single instance)**: session 3 asked "what should I do next?"; the operator pasted the question into session 2's terminal; session 2 gave Option A/B/C analysis; the operator carried session 2's recommendation back to session 3.

This is **observed once locally**, low-confidence as a generalizable pattern. Multi-agent review, advisor agents, debate, and manager/specialist consultation are common in industry frameworks; "the operator is the bus" applied to advice-seeking may be a narrower local technique. Treat it as a possibility, not a recommended discipline, until it repeats and clarifies.

When used: keep advice-seeking lightweight. The pattern is suited to tactical questions, not strategic decisions (those go to the strategic-tier session or back to the operator directly).

### 4.3 Selecting the right pattern

The §1 hybrid-boundary test (synthesis owner / operator-facing speaker / handoff mechanism) is the primary tool. As a heuristic:

- **Single-orchestrator + artifact-mediated** (this pattern) when: long-range coherent work; output is artifact-shaped (ADRs, code commits, doctrine); narrative continuity matters; coordination cost should be minimized.
- **Horizontal teams** (Claude Code agent teams; CrewAI hierarchical; AutoGen `SelectorGroupChat`) when: parallel exploration adds value; teammates need to challenge each other's conclusions; the output is the *synthesis* of multiple perspectives.
- **Hybrid** when: a complex task has both phases — e.g., parallel research → synthesis-by-orchestrator → artifact handoff to next session. Make the boundary explicit per the §1 test.

The anti-pattern is silent composition: spawning sub-agents inside an agent-team teammate, or letting peer orchestrators message each other directly, without naming who owns synthesis or who speaks to the operator.

---

## §5 Failure modes and anti-patterns

| Anti-pattern | Source | This pattern's mitigation |
|--------------|--------|----------------------------|
| **Infinite handoff loops** (A→B→C→A with replanning) | 2026 industry survey | One-direction flow: `operator ↔ orchestrator → child → orchestrator → operator`. Children never dispatch siblings. |
| **Unbounded nested hierarchy / manager-of-managers** | OpenClaw VISION.md (roadmap guardrail, `:106-117`); Claude Code teams ("no nested teams") | Three-tier role stratification has flat structure within tier. Hierarchy is bounded at 3 tiers. |
| **Mid-turn cache invalidation** | Hermes (frozen MEMORY.md at session start) | Memory writes apply to next session only. CLAUDE.md updates land at /end, not mid-session. |
| **Full conversation forwarding** (verbose, expensive) | 2026 industry survey | Structured handoff artifact (115–310 lines measured locally) replaces full transcript. |
| **Stale handoff / poisoned context / source drift** | (Anticipated; specific to this pattern) | §3.2 Step 0 handoff audit verifies HEADs, dirty status, task currency, artifact existence, post-handoff operator changes; expiry window for old handoffs. |
| **Memory surface divergence** | (Anticipated; multi-surface design) | §3.4 source-of-truth hierarchy names which surface wins per state-kind. |
| **Operator-as-bus bottleneck and fatigue** | (Anticipated; central role of operator) | Bounded session count per day; cross-session consultation kept lightweight; tooling proposals (§6) reduce manual handoff overhead. |
| **Letting team run unattended too long** | Claude Code teams docs | Single-task children with close-out manifest at Step 7.5 enforce check-in cadence. |
| **Same-file editing by parallel agents** (working-tree race) | Claude Code teams docs | Sequential dispatch by default; worktree isolation (§2.6) for genuine parallelism, with merge-owner discipline. |
| **Unmanaged hybridization** (vertical + horizontal in one session without explicit boundaries) | OpenAI / LangChain / CrewAI counter-evidence + cross-pattern interaction | §1 hybrid-boundary test must be answered (synthesis owner / operator-facing speaker / handoff mechanism). |
| **Pre-baked plan misdirection** | Operational validation (spore:ADR-0071, 2026-04-25) | Audit-then-propose at Step 0.5 (§2.2). |
| **Multi-turn iterative subagent prompts** | Operational validation across 2026-04-25 arc | Comprehensive single-shot prompts (§2.3). Multi-turn dialogues with sub-agents accumulate drift. |
| **Loose staging discipline making method unauditable** | retrospective:417 | §3.5 explicit-staging discipline; never `git add -A`; surface untracked drift in handoffs. |

---

## §6 Tooling proposals

### 6.1 PreCompact + SessionStart hook pair (highest-leverage; spike candidate)

**Problem**: on 2026-04-25, three cross-session handoffs each cost ~5–10 minutes of operator effort: assess context budget, author handoff prompt, start fresh session, paste handoff prompt.

**Proposed solution**: register two hooks in `~/.claude/settings.json`. The Claude Code hook config is keyed by event with arrays of matcher configurations (`claude-code/src/schemas/hooks.ts:194-213`):

```json
{
  "hooks": {
    "PreCompact": [
      {
        "matcher": "manual",
        "hooks": [
          { "type": "command", "command": "bash ~/.claude/hooks/handoff-snapshot.sh" }
        ]
      },
      {
        "matcher": "auto",
        "hooks": [
          { "type": "command", "command": "bash ~/.claude/hooks/handoff-snapshot.sh" }
        ]
      }
    ],
    "SessionStart": [
      {
        "matcher": "compact",
        "hooks": [
          { "type": "command", "command": "bash ~/.claude/hooks/handoff-restore.sh" }
        ]
      },
      {
        "matcher": "startup",
        "hooks": [
          { "type": "command", "command": "bash ~/.claude/hooks/handoff-restore.sh" }
        ]
      }
    ]
  }
}
```

**Hook semantics that constrain implementation** (`claude-code/src/utils/hooks/hooksConfigManager.ts:86-93`, `:136-143`):

- **PreCompact**: matchers are `manual` or `auto` (covering /compact and auto-compact paths). Exit 0 stdout is **appended as custom compact instructions** — *not* arbitrary status output. The snapshot script must therefore write the handoff *to a file silently* and either emit nothing on stdout or emit only content intended to influence the compaction summary.
- **SessionStart**: matchers are `startup`, `resume`, `clear`, `compact`. Exit 0 stdout is shown to Claude. The restore script can emit handoff content on stdout, where it will appear in the next session's context — but framing this as "initial system context" overstates it; it is content shown to Claude at session start, of comparable weight to other startup messages.

**Spike scope intentionally narrow**: the JSON example above registers only the `compact` and `startup` SessionStart matchers, not `resume` and `clear`. Rationale: a fresh-after-/compact session and a fresh `claude` invocation are the two cases where this pattern's handoff-restore is most useful. Resume picks up a specific prior session ID and already preserves full conversation context. Clear is operator-initiated context drop, where auto-injecting a stale handoff would fight the operator's intent. Add `resume` and/or `clear` matchers in a later spike if a use-case appears.

**`handoff-snapshot.sh`** (sketch, ~50 lines):
- Read project's `CLAUDE.md` "Current Status" section
- Snapshot `git status` + recent commits across canon-bearing repos
- List active plans in `~/.claude/plans/` matching project
- Write `~/.claude/handoffs/{project}/$(date +%s).md`
- Stay silent on stdout (or emit only content valid as compact instructions)

**`handoff-restore.sh`** (sketch, ~30 lines):
- Detect latest handoff file in `~/.claude/handoffs/{project}/`
- If recent (within ~24h) and matcher value indicates a fresh-after-compact / fresh-startup path, emit the handoff content to stdout
- Otherwise no-op (silent exit 0)

**Estimated effort**: 1–2 hours prototyping + testing across `manual`, `auto`, `compact`, and `startup` paths.
**Estimated savings**: 5–10 min × multiple handoffs/day. Pays back in 1 day if hooks fire reliably.
**Status**: **spike candidate**. Schema is verified against the local Claude Code source; runtime semantics are documented but not yet validated end-to-end. Move to "design ratified" only after a prototype has fired across at least the `manual` and `auto` PreCompact matchers and at least the `compact` and `startup` SessionStart matchers, with the handoff round-trip observed.

### 6.2 `/handoff-prompt` skill (darren-workflow)

A complementary skill that auto-generates a handoff prompt on demand (without /compact triggering), for cases where the operator wants to fork to a fresh session voluntarily before the context budget hits the limit.

Inputs: project name, target session role-tier, current task. Output: `tmp/handoff-prompt-<timestamp>.md` populated with structure from §3.1 (including the §3.1 metadata block).

### 6.3 `/end` skill extension

The existing `/end` skill in darren-workflow already handles session wrap. Extend with an optional `--with-handoff` flag that writes a handoff snapshot before final commit. Closes the gap between manual handoff authoring and automatic snapshot when /end is the explicit termination path rather than /compact.

### 6.4 Memory-flush turn before /compact

OpenClaw pattern (`openclaw-upstream/extensions/memory-core/src/flush-plan.ts:24-138`): insert a silent agent turn before transcript compaction asking "what durable facts need to land in memory before we summarize away history?"

Manual implementation currently: the operator prompts before `/compact` with "what should I write to CLAUDE.md or memory before compacting?" Then compacts.

A hook-automated version requires a hook primitive that asks the operator a question rather than executing a command. Not currently supported by the Claude Code hook system. **Defer** until the primitive exists.

---

## §7 Claims for learning field

- **C1** [target: fc.connection.multi-session-orchestrator-handoff] [concept: orchestrator-executor-topology]: Multi-session orchestrator-handoff is a *defensible operating doctrine* (single-orchestrator + artifact-mediated handoff + flat one-direction dispatch), not a categorical taxonomy of all multi-agent systems. Public frameworks (OpenAI Agents SDK, LangChain, CrewAI, AutoGen) treat hybrids as normal; this pattern's distinguishing property is that it picks a coherent operating mode and enforces three boundary conditions (synthesis owner, operator-facing speaker, handoff mechanism). *Evidence: §1 hybrid-boundary test; OpenAI / LangChain / CrewAI / AutoGen counter-evidence cited in §1; Spore canon-rebuild arc 2026-04-22 → 2026-04-25 validated this operating mode across 29 ADRs and 10 Agent-tool orchestrations on 2026-04-25.*

- **C2** [target: fc.connection.multi-session-orchestrator-handoff] [concept: decision-gated-plan-structure]: Decision-gated plan structure with audit-then-propose at Step 0.5 primarily protects rollback integrity and auditability — argument is settled before files start moving, so the file changes inside the session-atomic window are mechanical, narrowly-scoped, and reviewable as a unit. Speed is a measured secondary consequence, not the load-bearing claim. *Evidence: retrospective:191 ("not merely speed; it protected rollback integrity"); measured timings sub-2-min through 8m21s across 2026-04-25 ADRs; zero rollbacks across 29 ADRs in the arc.*

- **C3** [target: fc.connection.multi-session-orchestrator-handoff] [concept: session-role-stratification]: Three-tier role stratification (Strategic / Tactical / Execution) maps cleanly to context-budget tiers and curation-responsibility tiers. The strategic tier holds CLAUDE.md continuity; tactical tier holds within-day execution; execution tier produces close-out manifests. A tier is a responsibility, not necessarily a distinct session ID. *Evidence: Spore canon-rebuild arc 2026-04-25 — one strategic-tier orchestrator session plus multiple tactical successors across 24 hours validated the three-tier composition; tactical sessions turned over multiple times within the day (producing three handoff artifacts), while the strategic narrative persisted in CLAUDE.md.*

- **C4** [target: fc.connection.multi-session-orchestrator-handoff] [concept: structured-handoff-artifact]: Structured handoff artifacts (115–310 lines, measured) function as compressed context-transfer between sessions. They are substantially cheaper than full transcript forwarding and trade history-completeness for task-focus. The compression is intentional: a fresh-session start with a structured summary outperforms session-resume-with-full-history when the next session's task is well-defined. *Evidence: `wc -l -w tmp/handoff-prompt-*.md` measured 115–310 lines / 1,057–3,390 words across five handoff artifacts spanning 2026-04-24 through 2026-04-26; three of those handoffs landed cleanly on 2026-04-25 with successor sessions producing full ADRs without requiring re-transmission of context.*

- **C5** [target: fc.connection.multi-session-orchestrator-handoff] [concept: hook-driven-handoff-automation]: Claude Code's PreCompact + SessionStart hook pair is a plausible substrate for converting manual handoff discipline into structural automation; the schema and matcher values are verified against the local source, but the end-to-end round-trip has not been validated. Treat as **spike candidate** until a prototype fires across `manual`/`auto` PreCompact matchers and `compact`/`startup` SessionStart matchers and the handoff round-trip is observed. *Evidence: schema at `claude-code/src/schemas/hooks.ts:194-213`; event semantics at `hooksConfigManager.ts:86-93` and `:136-143`; manual baseline of 5–10 min per handoff measured on 2026-04-25.*

- **C6** [target: fc.connection.multi-session-orchestrator-handoff] [concept: orchestrator-executor-topology]: One-direction message flow at the dispatch layer — `operator ↔ orchestrator → child → orchestrator → operator` — and bounded tier-depth avoid the most-cited 2026 production failure mode for multi-agent systems (infinite handoff loops with replanning drift). The constraint is that children do not dispatch siblings, orchestrators do not message peer orchestrators, and tier-depth is fixed at 3. *Evidence: 2026 industry surveys identifying A→B→C→A loops as top failure mode; OpenClaw VISION.md:106-117 rejecting nested-hierarchy default; Spore canon-rebuild arc validated single-orchestrator-at-a-time across 10 dispatches on 2026-04-25 with zero rollbacks.*

- **C7** [target: fc.connection.multi-session-orchestrator-handoff] [concept: orchestrator-executor-topology]: Worktree isolation (Claude Code Agent tool's `isolation: "worktree"`) prevents simultaneous working-tree mutation races for parallel multi-task dispatch; it does not prevent logical merge conflicts. Adoption requires a designated merge owner, conflict-handling rule, per-child close-out manifests, and a validation gate after reintegration. *Evidence: `claude-code/src/utils/worktree.ts:902-951`; `AgentTool.tsx:90-100`, `:582-593`; primitive exists, reintegration discipline is the missing complement.*

- **C8** [target: fc.connection.multi-session-orchestrator-handoff] [concept: audit-then-propose]: Audit-then-propose discipline composes across role-tiers. A child agent's Step 0.5 audit catches plan Context-section drift; an orchestrator successor's Step 0 audit catches handoff drift; the strategic session's CLAUDE.md curation catches arc-level drift. Each tier's audit prevents pre-baked errors that would have propagated through the dispatch chain. *Evidence: spore:ADR-0071 on 2026-04-25 caught pm:ADR-0014 implicit drift framing at Step 0.5; spore:ADR-0064 Step 0.5 audit reclassified plan-default; recurring pattern across the canon-rebuild arc.*

- **C9** [target: fc.connection.multi-session-orchestrator-handoff] [concept: handoff-protocol]: Operator-mediated cross-session consultation (the operator carrying a question between two orchestrator sessions and back) was observed once on 2026-04-25 and is **low-confidence as a generalizable pattern**. Multi-agent review and advisor patterns are common in industry; this specific operator-as-bus consult-loop may be a narrower local technique. Re-evaluate after additional instances. *Evidence: single observation 2026-04-25 (session 3 → session 2 → session 3); not described in the surveyed sources.*

---

## §8 Open questions

1. **Hook prototype validation**: do PreCompact + SessionStart hooks fire reliably across `manual`/`auto` compaction and `startup`/`resume`/`clear`/`compact` session-start paths? Edge cases (signal-induced shutdown, context-overflow auto-compact mid-tool-call) need empirical validation.
2. **Worktree-mode threshold**: at what count of truly-parallel children does worktree overhead cost exceed sequential-dispatch coordination cost? Probably ≥3 but needs operational validation.
3. **Operator-mediated consultation formalization**: should §4.2 become a `/consult-other-session` skill, or is it intentionally lightweight and skill-fication would over-formalize?
4. **Session-fork primitive**: Claude Agent SDK provides `fork_session: True` for branching from a session's history. Does it have a place in this pattern — e.g., "fork the current orchestrator session, run an experiment, abandon if dead-end"? Or is fresh-session-with-handoff strictly better for our use cases?
5. **Memory-flush turn formalization**: should the manual "what should I write to memory before /compact?" become a hook-driven prompt-injection? Requires a hook primitive that asks the operator a question rather than executing a command.
6. **Multi-orchestrator parallelism**: 2026-04-25 validated *sequential* tactical sessions. Is there a use-case for *parallel* tactical sessions on different sub-arcs of one project? If yes, what's the coordination primitive — strategic-session arbitrates and two parallel tactical sessions proceed independently?

---

## §9 References

### Primary local repos
- `~/projects/claude-code/` — Claude Code CLI source (auto-memory + Session Memory subsystems, hooks, agent teams, sub-agents, worktree)
- `~/projects/hermes-agent/` — Hermes agent (memory architecture, structured compression with handoff framing, parent_session_id chains)
- `~/projects/openclaw-upstream/` — OpenClaw (memory-flush turn, three-tier compaction fallback, roadmap guardrail against nested-hierarchy default)

### Web documentation
- [Claude Code agent teams](https://code.claude.com/docs/en/agent-teams)
- [Claude Code sub-agents](https://code.claude.com/docs/en/sub-agents)
- [Claude Agent SDK sessions](https://code.claude.com/docs/en/agent-sdk/sessions)
- [OpenAI Agents SDK — multi-agent patterns](https://openai.github.io/openai-agents-js/guides/multi-agent/)
- [OpenAI Agents SDK — handoffs](https://openai.github.io/openai-agents-js/guides/handoffs/)
- [LangChain multi-agent](https://docs.langchain.com/oss/python/langchain/multi-agent/index)
- [CrewAI hierarchical process](https://docs.crewai.com/en/learn/hierarchical-process)
- [AutoGen SelectorGroupChat](https://microsoft.github.io/autogen/stable/user-guide/agentchat-user-guide/selector-group-chat.html)
- [Anthropic advisor strategy](https://claude.com/blog/the-advisor-strategy)
- [Microsoft Azure AI agent design patterns](https://learn.microsoft.com/en-us/azure/architecture/ai-ml/guide/ai-agent-design-patterns)
- 2026 industry orchestration surveys — see research memo for full list

### Internal artifacts
- `spore/docs/research/connections/canon-rebuild-arc-method-retrospective.md` — Spore-side method retrospective for the same arc this note bridges
- `spore/tmp/multi-session-orchestrator-research-2026-04-25.md` — pre-bridge-note research synthesis with full per-source citations and dispositions
- `spore/tmp/codex-review-multi-session-orchestrator-handoff-20260424-203926.md` — third-party Codex review that produced the 2026-04-25 revision pass
- `spore/tmp/handoff-prompt-2026-04-{24,25,26}-*.md` — measured handoff artifacts referenced in C4
- `spore/CLAUDE.md` — operational session-history table for the canon-rebuild arc
- ADRs of note: spore:ADR-0072 (sandbox-plan-file fallback precedent), spore:ADR-0071 (canon-prose-drift discovery), spore:ADR-0073–0077 (foundation-doc admissions validating template-adaptability), pm:ADR-0014–0017 (cross-repo alignment), ic:ADR-0018–0020 (cross-repo alignment)

### Flowcoding canon
- `fc.canon-decision.flowcoding-identity-and-scope` (FC-ADR-0001) — flowcoding-as-methodology-host
- `fc.canon-decision.methodology-migration-convention` (FC-ADR-0002) — attrition-based migration
- `fc.methods.governance-architecture` — canon-bearing governance framework this note operates within
- `fc.connection.claude-code-flow-coding` — sister bridge note covering Claude Code's broader subsystems

---

**Status**: draft. Two Codex third-party reviews complete (2026-04-25); both revision passes applied. Final review verdict: no critical blockers; structurally sound for `status: active` from review perspective. Hook proposal explicitly held as spike candidate pending prototype validation. Move to `status: active` after operator ratification, with C5's conditional preserved as a known-pending item until the prototype lands.
