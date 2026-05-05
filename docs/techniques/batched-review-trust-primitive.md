---
doc_id: fc.technique.batched-review-trust-primitive
doc_kind: pattern
status: draft
depends_on:
  - fc.spec-driven-development
related_claims:
  - "orn:koi-net.claim:b26d2853804b360997ed9cea6b50b983"
  - "orn:koi-net.claim:ee6d0f52e228bbc978a98ee45ac5ad76"
derived_from:
  - fc.connection.claude-code
concepts:
  - plan-mode
  - batched-review
---

# Batched Review as Trust Primitive

Make human approval of agentic action a single, explicit event that gates a batch of proposed work — not a per-tool interruption and not a set-and-forget blanket permission.

## Context

The practitioner needs to retain oversight over what the AI does with their systems, but per-action approvals shatter flow state and blanket approvals are too risky. Between these extremes is the review surface: the agent explores, summarizes its intended work, and the practitioner approves or rejects it as a unit. Flow Coding's core cycle — *spec → structured review → execute* — is an instance of this primitive at task-planning time.

## Problem

How do you give the agent enough autonomy to be useful without giving up structural oversight of what it changes?

## Forces

- **Flow vs. control.** Frequent approval prompts break concentration; infrequent approvals mean decisions get made without the practitioner's input.
- **Scope of grant.** A permission grant must be scoped — per-session, per-project, per-user — and session scope is usually the right default: trust must be reconstructed explicitly.
- **Batch legibility.** The review must present *what will be done* in a form the practitioner can read and edit, not just a binary approve/deny.
- **Multi-agent oversight.** When teammates are proposing work in parallel, a single leader must still be the approver — oversight cannot shard across the team.

## Pattern

**Mode transition as the approval gate.** Enter a mode (plan, explore, draft — pick a name) where the agent is restricted to read-only action. It accumulates a plan. Exit is a single explicit practitioner act: approve, reject, or edit-then-approve. Write access reopens only on approval.

**Semantic permissions, not enumerated commands.** When the agent requests permission as part of the plan, let it describe the capability class ("run tests", "install dependencies") rather than enumerating every command. The approval step converts semantic descriptions into session-scoped rules.

**Session-scoped grants by default.** Permissions granted at approval time live for the session, not forever. This is not a limitation — it is governance: trust does not leak across sessions, and each new working session starts from an explicit act.

**Leader-gated approval in multi-agent teams.** Teammates cannot approve their own plans. When a teammate finishes exploring, it serializes a plan-approval request (with a unique request ID) to the leader's mailbox and waits. The leader — the practitioner, or the lead agent the practitioner has designated — decides.

**Editable plan artifact.** The plan is a file on disk with a stable path. The practitioner can edit it before approving. The approval event should record whether the plan was modified (so later audit can distinguish "approved as proposed" from "approved after edits").

## Evidence

- Mode transition gate: `src/types/permissions.ts:16-22` registers `plan` as an external permission mode; `src/tools/ExitPlanModeTool/ExitPlanModeV2Tool.ts:357-403` restores the pre-plan mode on exit.
- Semantic permissions: `src/tools/ExitPlanModeTool/ExitPlanModeV2Tool.ts:64-87` accepts `allowedPrompts` — LLM-written semantic action classes — and converts them to session rules.
- Session-scoped grants: `src/components/permissions/ExitPlanModePermissionRequest/ExitPlanModePermissionRequest.tsx:56-76` writes grants to the `session` destination, not user/project/policy settings.
- Multi-agent gating: `src/tools/ExitPlanModeTool/ExitPlanModeV2Tool.ts:264-313` — teammates with `planModeRequired=true` serialize `plan_approval_request` messages to the leader's mailbox with unique request IDs.
- Editable plan: `src/tools/ExitPlanModeTool/ExitPlanModeV2Tool.ts:251-261` writes the plan to a stable `getPlanFilePath(agentId)`; `planWasEdited` output signals modification.

## See Also

- [Bridge note: Claude Code → Flow Coding](../research/connections/claude-code-flow-coding.md) (§Plan Mode + Practitioner Trust Surface, claims C3–C4)
- [Spec-Driven Development](../foundations/spec-driven-development.md) — the Flow Coding instance of this primitive at task-planning time.
- [The "Nothing to Save" Exit Clause](nothing-to-save-exit-clause.md) — complementary technique on the prompt side of the review surface: gives the agent permission to fail-closed when there's nothing to surface.
