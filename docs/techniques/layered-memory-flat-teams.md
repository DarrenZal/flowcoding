---
doc_id: fc.technique.layered-memory-flat-teams
doc_kind: pattern
status: draft
depends_on:
  - fc.project-vision
related_claims:
  - "orn:koi-net.claim:cca0855f140f80fbb18194aa2f1082a7"
  - "orn:koi-net.claim:9e99431048a930d07d9451dc96836e7e"
derived_from:
  - fc.connection.claude-code
concepts:
  - context-architecture
  - multi-agent-coordination
---

# Layered Memory, Flat Teams

Two orthogonal coordination primitives: a **hierarchical memory file** that separates team conventions from personal customization, and **flat-roster agent teams** whose members cannot spawn further teammates.

## Context

Long-running practitioner sessions and multi-agent work both fail in the same way: coordination state is either over-centralized (one file holds everything, edits collide) or under-specified (state scattered across processes, consensus impossible). The answer in both cases is a constrained structure — layered ownership for memory, flat hierarchy for teams.

## Problem

How should persistent project state and active multi-agent coordination be structured so they scale without becoming chaotic?

## Forces

- **Team conventions vs. personal preferences.** Both want to influence the AI's behavior, but they have different owners and different durability.
- **Shared oversight vs. recursive delegation.** If any teammate can spawn teammates, approval paths deepen unpredictably; if only leaders can spawn, the practitioner always knows where oversight lives.
- **Deterministic routing vs. registry lookups.** Leaders need to address teammates without calling a discovery service — reachability should be computable from names.
- **Crash resilience.** Messages between agents should survive a teammate restart; a mailbox on disk outlives the process that reads it.

## Pattern

**Layered memory file (per-project AI context).** Four layers, loaded in reverse priority:

1. **Managed** — shipped with the tool (system-level defaults).
2. **User** — `~/.claude/CLAUDE.md` — the practitioner's personal preferences across all projects.
3. **Project** — `./CLAUDE.md` at repo root — team conventions, **checked in**.
4. **Local** — `./CLAUDE.local.md` or equivalent — practitioner's personal overrides for this project, **gitignored**.

Later layers override earlier ones. The project layer enforces collective discipline; the local and user layers allow individual customization without merge conflicts. Discovery walks upward from the current directory; toggles exist to disable specific layers.

**Flat-roster teams with deterministic identity.** When teams are allowed:

- Agent identity is a composite `name@team` — deterministic, computable, sanitized (no `@` in names).
- Only the **leader** can spawn teammates. Teammates cannot spawn further teammates. The roster is flat by construction; approval paths are shallow and auditable.
- Teammates communicate via **file-based mailboxes** — messages persist on disk, survive crashes, decouple sender from receiver.
- Shared resources (task lists, state files) are serialized by **file-based locks with backoff retry**; high-water marks prevent ID reuse after reset.

**Backend polymorphism for execution modes.** A single `TeammateExecutor` interface is implemented by multiple backends (tmux panes, iTerm2 windows, in-process async). Coordination code does not branch on backend; the messaging abstraction is identical.

## Evidence

- Layered memory file hierarchy: `src/utils/claudemd.ts:1-26` documents the four-layer load order with reverse-priority override; `src/utils/claudemd.ts:790-935` implements `getMemoryFiles()` with per-layer enable toggles.
- Deterministic composite identity: `src/utils/agentId.ts:38-40` (`formatAgentId`).
- Flat-roster enforcement: `src/tools/AgentTool/AgentTool.tsx:273` rejects teammate-spawns-teammate with the error "Teammates cannot spawn other teammates — the team roster is flat."
- File-based mailboxes: `src/tools/SendMessageTool/SendMessageTool.ts:149-189` writes structured messages with sender identity, color, summary, timestamp.
- File-based locking: `src/utils/tasks.ts:94-107,147-188` implements lock + backoff retry + high-water mark.
- Backend polymorphism: `src/utils/swarm/backends/types.ts:279-300` defines the `TeammateExecutor` interface shared by tmux, iTerm2, in-process backends.

## See Also

- [Bridge note: Claude Code → Flow Coding](../research/connections/claude-code-flow-coding.md) (§Memory + Context, §Multi-Agent Coordination, claims C7–C8)
- [Project Vision](../project-vision.md)
