---
doc_id: fc.connection.claude-code
doc_kind: research
status: draft
depends_on:
  - fc.project-vision
  - fc.spec-driven-development
  - fc.flow-state-practice
relates_to:
  - spore.connection.claude-code-membrane-control
sources:
  - path: projects/claude-code/src/assistant/
    title: "Claude Code agentic loop (query engine, turn orchestration)"
    type: primary
    commit: 071d5ecee8392fb0054e5de09a541002c56a015f
  - path: projects/claude-code/src/tools/ExitPlanModeTool/
    title: "Plan mode + practitioner trust surface"
    type: primary
  - path: projects/claude-code/src/utils/hooks.ts
    title: "Hook execution model"
    type: primary
  - path: projects/claude-code/src/skills/loadSkillsDir.ts
    title: "Skills architecture (loader + frontmatter contract)"
    type: primary
  - path: projects/claude-code/src/utils/claudemd.ts
    title: "CLAUDE.md hierarchy (memory + context)"
    type: primary
  - path: projects/claude-code/src/utils/swarm/backends/types.ts
    title: "Multi-agent coordination (team executors + mailboxes)"
    type: primary
  - path: projects/claude-code/docs/architecture.md
    title: "Claude Code architecture docs"
    type: primary
  - path: projects/claude-code/docs/subsystems.md
    title: "Claude Code subsystems docs"
    type: primary
  - path: projects/claude-code/agent.md
    title: "Claude Code agent configuration"
    type: primary
  - path: projects/claude-code/Skill.md
    title: "Claude Code repository skill"
    type: primary
disposition: support existing term
research_subkind: bridge_note
concepts:
  - agentic-control-loop
  - plan-mode
  - hooks-and-skills
  - context-architecture
  - multi-agent-coordination
---

# Claude Code — Flow Coding Bridge Note

Claude Code is Anthropic's production CLI for agentic coding. Its source is direct evidence for the patterns Coding Commons intends to govern: spec-driven development, flow state preservation, batched review, and coordinated autonomy across human and AI practitioners. This note maps Claude Code's architecture to Flow Coding's foundation docs, cites implementation specifics at a pinned commit, and extracts 5–8 claims for the Flow Coding learning field.

Complementary to `spore.connection.claude-code-membrane-control`, which analyzes the permission enforcement machinery narrowly. This note takes the practitioner-facing lens: what does Claude Code's design reveal about best practices for human–AI collaborative coding?

## Artifact Profile

- **What:** Terminal-native AI coding CLI, ~1,900 TypeScript files / ~512k LOC, React + Ink for the terminal UI, Bun runtime, Zod validation, GrowthBook feature gates. Evidence: `Skill.md` (Tech Stack table).
- **Study slice:** 5 subsystems investigated in parallel — Agentic Loop, Plan Mode + Trust Surface, Hooks + Skills, Memory + Context, Multi-Agent Coordination. Commit pin `071d5ec` on local HEAD; working tree had one modified file (`web/components/ui/Skeleton.tsx`, UI rendering, out of scope).
- **Evidence reliability:** All primary citations are from HEAD-clean files at the pinned commit. Line numbers come from agent investigation and reflect the pinned tree; citations are file + location (range or function), not prose assertions.
- **Non-duplication guard:** Claims whose primary evidence is under `src/hooks/toolPermission/` or `src/utils/permissions/` are owned by the Spore bridge note and excluded here. The permission *experience* (plan mode, session-scoped grants) is in scope; permission *enforcement code* is not.

---

## Subsystem: Agentic Loop

Claude Code's turn orchestration is an explicit, typed state machine over mutable per-iteration state. The loop is structured as an async generator that yields messages while simultaneously dispatching tools — streaming-first compute/IO overlap — with recovery stages composed before termination.

Every iteration destructures mutable state, and transitions use typed `continue` objects (e.g., `{ reason: 'tool_use' }`). This makes recovery paths inspectable without parsing message content.

- **Core turn loop:** `src/query.ts:241-1727` (`queryLoop`, State type at `src/query.ts:265-279`).
- **Typed transitions:** `src/query/transitions.ts:8-37` (`Terminal` and `Continue` discriminated unions).
- **Streaming tool execution:** `src/services/tools/toolOrchestration.ts:19-82` (`runTools`).
- **Partitioned concurrency:** `src/services/tools/toolOrchestration.ts:91-116` (read-only tools concurrent up to `MAX_TOOL_USE_CONCURRENCY`; state-mutating tools serial).
- **Compose-before-retry error recovery:** `src/query.ts:1065-1175` (collapse-drain → reactive-compact → surface). Recoverable errors are *withheld* from SDK callers during retry (`src/query.ts:788-816`).
- **Abort as loop-wide control flow:** `src/query.ts:1015-1051` (on abort signal: stop streaming, synthesize tool_result blocks for in-flight tools, transition to `aborted_*`).

**Coding Commons derivation.** The agentic loop is not a "call the model, parse the response" procedure — it is a state machine whose safety properties depend on explicit transition types, composable recovery stages, and concurrency-aware tool dispatch. Flow Coding practitioners building their own loops should externalize these invariants (typed states, partitioned concurrency) rather than encode them in ad-hoc branching.

---

## Subsystem: Plan Mode + Practitioner Trust Surface

Plan mode is Claude Code's concrete implementation of *batched review*: a two-phase workflow (exploration → approval) that temporarily downgrades trust, batches all read-only exploration under a single approval gate, and then opens write access on a single explicit approval event. It is the closest working analogue to the Flow Coding concept of *spec → structured review → execute*.

- **Mode transition state:** `src/types/permissions.ts:16-22` (plan added to `EXTERNAL_PERMISSION_MODES`); `src/tools/ExitPlanModeTool/ExitPlanModeV2Tool.ts:357-403` (exit restores `prePlanMode`).
- **Semantic permission grants from plans:** `src/tools/ExitPlanModeTool/ExitPlanModeV2Tool.ts:64-87` (`allowedPrompts` — LLM outputs semantic action classes like "run tests", "install dependencies", converted at approval time to session-scoped rules).
- **Approval UI contract:** `src/components/permissions/ExitPlanModePermissionRequest/ExitPlanModePermissionRequest.tsx:56-76` (session-scoped `PermissionUpdate` destination — grants do not persist across sessions); `src/components/permissions/EnterPlanModePermissionRequest/EnterPlanModePermissionRequest.tsx:52-59`.
- **Multi-agent plan gating:** `src/tools/ExitPlanModeTool/ExitPlanModeV2Tool.ts:264-313` (teammates with `planModeRequired=true` cannot exit plan mode locally; exit serializes a `plan_approval_request` to the leader's mailbox with a unique request ID); `src/utils/teammate.ts:149-150`.
- **Plan persistence + editability:** `src/tools/ExitPlanModeTool/ExitPlanModeV2Tool.ts:251-261` (plan written to disk via `getPlanFilePath(agentId)`; `planWasEdited` output signals user modification).
- **Channel gating:** `src/tools/EnterPlanModeTool/EnterPlanModeTool.ts:56-67` (plan mode disabled when `--channels` is active because the approval dialog requires terminal visibility).

**Coding Commons derivation.** Practitioners benefit from a *review surface* where the LLM articulates intent and requested permissions in one structured object (plan + `allowedPrompts`), and the practitioner approves/rejects atomically. Session-scoping of those grants is not a limitation — it is a governance choice: trust does not leak across sessions, so each new working session reconstructs trust from an explicit act.

---

## Subsystem: Hooks + Skills

Hooks and skills are the two ports through which Claude Code is extended without modifying its core. Hooks are deterministic, event-driven, invoked by lifecycle events and permitted to modify behavior (including blocking via exit code 2 or rewriting tool input); skills are markdown-native, reusable AI capabilities loaded from layered sources (bundled, project, user, plugin, MCP) with context-budgeted discovery.

- **Hook event schema:** `src/types/hooks.ts:22-163` (all hook event types); `src/types/hooks.ts:73-107` (`PreToolUse`/`PostToolUse` with `hookSpecificOutput` unions — PreToolUse can return `permissionDecision: allow|deny|ask` and `updatedInput`).
- **Hook execution generator:** `src/utils/hooks.ts:1952-2100` (`executeHooks`); `src/utils/hooks/hooksConfigManager.ts:270-365` (matcher-based routing across user/project/policy/plugin sources).
- **Hook execution types:** `src/utils/hooks.ts:150-165` (command / prompt / agent / callback); `src/utils/hooks/execPromptHook.ts:21-80` (LLM-evaluated hooks via Haiku with custom JSON schema). Exit codes are Unix-conventional: 0 success, 2 block.
- **Skill frontmatter contract:** `src/skills/loadSkillsDir.ts:185-265` (`parseSkillFrontmatterFields` — `name`, `description`, `whenToUse`, `allowed-tools`, `model`, `hooks`, `paths`, `context`).
- **Skill tool + context-budgeted listing:** `src/tools/SkillTool/SkillTool.ts:332-333` (tool definition); `src/tools/SkillTool/prompt.ts:21-29` (`SKILL_BUDGET_CONTEXT_PERCENT` caps listing at ~1% of context window; bundled skills prioritized, never truncated).
- **Path-scoped + forked execution:** `src/tools/SkillTool/SkillTool.ts:122-250` (`executeForkedSkill` — isolated token budget); `src/skills/loadSkillsDir.ts:159-178` (`parseSkillPaths` — activate only when model touches matching files).

**Coding Commons derivation.** Governance does not have to be a human-in-the-loop event. Hooks are *governance baked into the loop* — deterministic rules that observe or reshape the AI's behavior at well-defined event points. Skills are *governance as reuse* — the knowledge of "when and how to do X" shared as a markdown artifact. Together they describe a lightweight governance stack that practitioners can adopt incrementally.

---

## Subsystem: Memory + Context

Claude Code's memory and context system is organized around two orthogonal axes: **hierarchy** (managed → user → project → local override semantics for CLAUDE.md) and **stageability** (per-turn context assembly is memoized and separately invalidated from system prompt sections, which are themselves separately invalidated on compact).

- **CLAUDE.md hierarchy:** `src/utils/claudemd.ts:1-26` (four layers; reverse-priority load order; upward directory walk with worktree dedup; `getMemoryFiles()` at `src/utils/claudemd.ts:790-935` respects `projectSettings`/`localSettings`/`userSettings` toggles; `CLAUDE_CODE_DISABLE_CLAUDE_MDS` env var hard-disables auto-discovery).
- **Per-turn context assembly:** `src/context.ts:155-189` (`getUserContext`/`getSystemContext` memoized; atomically invalidated on `/compact` or `/clear`).
- **Session memory compaction:** `src/services/compact/sessionMemoryCompact.ts:44-631` (token/message-count minimums; tool-use/tool-result pairs preserved atomically; thinking blocks sharing `message.id` kept together).
- **Microcompact based on cache age:** `src/services/compact/microCompact.ts:253-293` (if >5 min since last assistant message, server cache has expired — clear old tool results proactively).
- **Query-driven lazy memory loading:** `src/memdir/findRelevantMemories.ts:39-75` (Sonnet selects up to 5 relevant memories per query; excludes recently-used tools to reduce reference-doc noise).
- **Auto-memory + index:** `src/memdir/memdir.ts:419-507` (`loadMemoryPrompt` builds MEMORY.md index + behavioral instructions).

**Coding Commons derivation.** The hierarchy is itself a governance artifact. Project-level CLAUDE.md is checked in and enforces team conventions; user and local layers are private to the practitioner. Flow Coding's spec-driven practice benefits from an equivalent: per-project governance that the AI sees every turn and an auto-memory that accumulates as the practitioner works.

---

## Subsystem: Multi-Agent Coordination

Claude Code ships a production multi-agent coordination stack: deterministic identity, file-based mailboxes, pluggable execution backends, and a strict "flat roster" rule that prevents teammates from spawning teammates — a deliberate governance constraint.

- **Deterministic composite identity:** `src/utils/agentId.ts:38-40` (`formatAgentId` — composite `agentName@teamName`; names sanitized to exclude `@`).
- **Pluggable execution backends:** `src/utils/swarm/backends/types.ts:279-300` (`TeammateExecutor` interface implemented by tmux / iTerm2 / in-process backends; shared mailbox-based messaging).
- **Team creation:** `src/tools/TeamCreateTool/TeamCreateTool.ts:128-200` (`TeamFile` + `leadAgentId` from `TEAM_LEAD_NAME`; shared task list directory initialized).
- **Unified task list resolution:** `src/utils/tasks.ts:199-210` (explicit env var → in-process teammate context → process-based teammate context → leader team name → session ID).
- **File-based concurrency control:** `src/utils/tasks.ts:94-107,147-188` (file locks with backoff retry, up to 30 retries at 5–100 ms; high-water-mark prevents ID reuse after reset).
- **Mailbox messaging:** `src/tools/SendMessageTool/SendMessageTool.ts:149-189` (structured messages with sender identity, color, summary, timestamp).
- **Flat-roster constraint:** `src/tools/AgentTool/AgentTool.tsx:273` (teammates attempting to spawn other teammates are rejected: "Teammates cannot spawn other teammates — the team roster is flat.").
- **Fork subagents for cache-identical prefixes:** `src/tools/AgentTool/forkSubagent.ts:32-170` (when the `FORK_SUBAGENT` feature is on, omitting `subagent_type` spawns a fork that inherits parent's full context and uses identical placeholder tool-result text to maximize prompt-cache reuse).

**Coding Commons derivation.** A commons for agentic coding needs primitives for coordinating multiple agents without recursion: deterministic identities so leaders can route, mailboxes so messages survive crashes, and a flat hierarchy so approval paths are shallow and auditable. The Claude Code implementation demonstrates that "production multi-agent" can be simple — a few hundred lines of mailbox + executor + identity — if the hierarchy constraint is enforced.

---

## Claims

1. **[CLAIM] Agentic loops should be implemented as explicit, typed state machines with typed `continue` transitions.** Recovery paths become inspectable without parsing message contents, and tests can assert state transitions directly.
   Evidence: `src/query/transitions.ts:8-37`; `src/query.ts:265-279`.

2. **[CLAIM] Tool dispatch in a loop should partition calls by side-effect class — read-only tools run concurrently up to a bounded ceiling; state-mutating tools run serially.** This preserves consistency without requiring per-tool synchronization logic.
   Evidence: `src/services/tools/toolOrchestration.ts:91-116`.

3. **[CLAIM] Batched review is the right primitive for human trust over agentic action: a mode transition that batches exploration under one approval, with approval-time permission grants that are session-scoped, not persisted.** This is Flow Coding's *spec → review → execute* cycle implemented in a production CLI.
   Evidence: `src/tools/ExitPlanModeTool/ExitPlanModeV2Tool.ts:147-493`; supporting: `src/components/permissions/ExitPlanModePermissionRequest/ExitPlanModePermissionRequest.tsx:56-76`.

4. **[CLAIM] Multi-agent plan approval should be leader-gated via mailbox-forwarded requests with unique request IDs.** Teammates cannot exit plan mode locally; they serialize a `plan_approval_request` to the leader. This preserves single-point oversight in parallel agent scenarios without requiring shared terminal access.
   Evidence: `src/tools/ExitPlanModeTool/ExitPlanModeV2Tool.ts:264-313`; supporting: `src/utils/teammate.ts:149-150`.

5. **[CLAIM] Hooks are the practical mechanism for *governance baked into the loop* — PreToolUse hooks can return a permission decision and even rewrite tool input before execution.** This lets teams enforce policy (e.g., "block `Bash(rm *)`") declaratively without touching tool code.
   Evidence: `src/types/hooks.ts:73-107`; supporting: `src/utils/hooks.ts:1952-2100`.

6. **[CLAIM] Skills are a viable unit of reuse for AI capabilities — markdown frontmatter + body, loaded from layered sources, context-budgeted at discovery time so the listing fits within a small fraction of the context window.** Flow Coding can adopt this format directly for sharing domain knowledge across practitioners.
   Evidence: `src/skills/loadSkillsDir.ts:185-265`; supporting: `src/tools/SkillTool/prompt.ts:21-29`.

7. **[CLAIM] A hierarchical memory file (CLAUDE.md at managed/user/project/local layers with reverse-priority override) separates *team conventions* from *personal customization*.** The project layer is checked in; the local layer is gitignored. Flow Coding projects should adopt this exact separation.
   Evidence: `src/utils/claudemd.ts:1-26`; supporting: `src/utils/claudemd.ts:790-935`.

8. **[CLAIM] Agent teams should be flat by construction — teammates cannot spawn teammates.** Combined with deterministic composite identity (`name@team`) and file-based mailboxes, this gives a minimal, auditable multi-agent stack.
   Evidence: `src/tools/AgentTool/AgentTool.tsx:273`; supporting: `src/utils/agentId.ts:38-40`.

---

## Disposition

**support existing term.** The evidence strengthens — rather than revises or extends — Flow Coding's existing foundations (`fc.spec-driven-development`, `fc.flow-state-practice`). Plan mode, semantic approvals, hooks, and skills are production realizations of patterns the foundations already name. No new term is introduced here; this note is a reference-implementation witness and a source of actionable claims for the learning field.

## Unresolved Tensions

- **Auto mode vs. plan mode:** Claude Code's `auto` mode (ML-classifier per-action approval) is feature-gated to Anthropic-internal users (`feature('TRANSCRIPT_CLASSIFIER')`). External practitioners never see it as a configuration option. Whether Flow Coding practitioners should eventually adopt a classifier-assisted auto mode — and how to govern it safely in a non-org context — is an open question. Evidence: supporting-only (auto mode gating lives in barred paths).
- **Forked subagents share parent context for cache reuse, but strip message history in in-process teammates:** `src/tools/AgentTool/forkSubagent.ts:32-170` vs. `src/utils/swarm/backends/InProcessBackend.ts:122`. Two different "parent context inheritance" models live side-by-side; a commons would need to pick one default.
- **Hook namespacing for skills:** Per-skill hooks are supported via frontmatter (`src/skills/loadSkillsDir.ts:136-153`), but no namespace-isolation evidence was found. Conflicts between a global hook and a skill's hook at the same event are not clearly resolved. This is a gap worth watching as Flow Coding grows its own skill/hook ecosystem.

## Coverage Gaps

All five subsystems produced ≥1 HEAD-clean primary file citation; no `[UNANALYZED]` sections. Some specifics — e.g., exact token-budget calculation in `src/bootstrap/state.ts` and `src/query/tokenBudget.ts`; the cache-lifecycle of session memory; stop-hook interaction with subagent mode — were noted as gaps by investigating agents but did not block subsystem-level evidence or claim generation.
