---
doc_id: fc.technique.typed-loop-design
doc_kind: pattern
status: draft
depends_on:
  - fc.spec-driven-development
related_claims:
  - "orn:koi-net.claim:011ea8c837e943b3a0d74101ac38ff1e"
  - "orn:koi-net.claim:e5839075ee3c94aaaa31fc5b0da5b00c"
derived_from:
  - fc.connection.claude-code
concepts:
  - agentic-control-loop
---

# Typed Loop Design

Build the agentic control loop as an explicit, typed state machine — with typed transitions between iterations and side-effect-aware concurrency across tool calls.

## Context

You are writing the inner control loop of an agentic system: a function that takes user input, calls a model, dispatches tools the model requests, folds the results back in, and repeats until terminal. The loop is the place where correctness, recovery, and performance live — most of the hard bugs in agentic systems concentrate here.

## Problem

Control loops written ad-hoc become untestable and fragile. State scattered across closure variables, transition logic expressed through nested conditionals, and tool calls dispatched sequentially "to be safe" — these patterns compound into loops whose recovery paths nobody can reason about and whose performance degrades as tool counts grow.

## Forces

- **Testability vs. prose.** Transition logic written as if-else branches is hard to assert in tests; typed state transitions make behavior inspectable without parsing message contents.
- **Concurrency vs. consistency.** Running tool calls serially is simple and safe; running them in parallel is fast but can introduce races for state-mutating operations.
- **Recovery vs. surface-level catch.** Try/catch around the whole loop hides failure modes; each recovery stage (compaction, model fallback, retry) wants its own explicit place.
- **Abort vs. cleanup.** User interrupts must leave message invariants intact (no orphaned `tool_use` without `tool_result`), which is trivial to get wrong.

## Pattern

**State machine shape.** Destructure mutable state at the start of each iteration. Transitions between iterations are typed `continue` objects (e.g., `{ reason: 'tool_use' }`, `{ reason: 'stop_hook_blocking' }`). Terminal states are a discriminated union. This gives recovery paths names and makes the loop testable at the transition level, not the text-matching level.

**Partition tools by side-effect class.** Read-only tools (file reads, lookups, searches) execute concurrently up to a bounded ceiling — typically 10 — because order does not affect correctness. State-mutating tools (writes, deletes, shell commands) execute serially. The loop itself does the partitioning; the tool implementations do not need to know about each other.

**Compose recovery stages, don't nest them.** When the model returns an error (context too long, output truncated, oversized media), withhold the error from downstream callers while the loop tries successive recovery stages: context collapse, reactive compaction, fallback model. Only surface the error if all stages are exhausted.

**Abort as loop-wide control flow.** A shared abort controller signal, checked at key points — before streaming, during tool dispatch, inside each recovery stage — and synthetic `tool_result` blocks for any in-flight tools when the signal fires. This keeps message invariants intact on interrupt without threading cancellation tokens through every function.

## Evidence

Claude Code implements all four of the above in a single large async-generator loop:

- Typed state transitions: `src/query/transitions.ts:8-37` defines `Terminal` and `Continue` discriminated unions; `src/query.ts:265-279` destructures per-iteration mutable state.
- Partitioned tool concurrency: `src/services/tools/toolOrchestration.ts:91-116` partitions calls into concurrent read-only batches and serial state-mutating runs.
- Composed recovery: `src/query.ts:1065-1175` chains collapse-drain → reactive-compact → surface. `src/query.ts:788-816` withholds intermediate errors from SDK callers during retry.
- Abort with synthetic results: `src/query.ts:1015-1051` handles abort by generating synthetic `tool_result` blocks for in-flight tools and transitioning to `aborted_*` states.

## See Also

- [Bridge note: Claude Code → Flow Coding](../research/connections/claude-code-flow-coding.md) (§Agentic Loop, claims C1–C2)
- [Spec-Driven Development](../foundations/spec-driven-development.md)
