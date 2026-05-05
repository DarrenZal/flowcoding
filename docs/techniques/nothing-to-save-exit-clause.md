---
doc_id: fc.technique.nothing-to-save-exit-clause
doc_kind: pattern
status: draft
depends_on:
  - fc.spec-driven-development
relates_to:
  - fc.technique.batched-review-trust-primitive
concepts:
  - prompt-craft
  - agentic-control-loop
  - output-production-bias
---

# The "Nothing to Save" Exit Clause

Any prompt that asks an agent — subagent, `claude -p` subprocess, or self — to inspect work and surface findings must include an explicit, named exit for "nothing meaningful was found." Without it, agents invent findings to justify their existence.

## Context

A practitioner runs an inspection-class task: review a plan, audit a code change, retrospect on a session, harvest open questions, score a draft. The task is dispatched to an agent with a prompt that names the inspection goal and the desired output shape. Sometimes the inspection finds nothing actionable. The prompt must make that outcome legible — both to the agent (so it does not pad) and to the calling code (so it can detect and skip the no-op cleanly).

## Problem

How do you give an inspection-class prompt the structural permission to return zero findings, and how do you make zero-findings detectable downstream?

The asymmetry: prompt-following agents are trained to produce the requested output shape. When the requested shape is "a list of findings," the agent will produce a list of findings even when none are warranted. The prompt's implicit contract — "fill the format" — overrides the implicit contract "be honest about absence." Without an explicit exit, output-production bias wins.

## Forces

- **Output-production bias vs. honest absence.** Without permission to produce nothing, agents pad: trivial issues surface as substantive, style preferences become "improvements," the input gets repeated back as findings, reports stretch to look thorough.
- **Detectability vs. natural language.** A free-form "no issues found" message is hard for calling code to detect reliably. A fixed string is cheap to grep and route on.
- **Structured-output constraints.** When the output must be JSON, "say nothing" is not legal — the schema demands a value. The exit must be representable inside the schema.
- **Soft refusal vs. hard exit.** The exit must be unambiguous. "I think the work is fine?" leaves the calling code uncertain; "Nothing to save." does not.

## Pattern

**Free-form prompts: name the exact exit string.**

```
[normal prompt body]

If nothing meaningful was found, output exactly: "Nothing to save."
Do not invent findings. Do not pad. A clean session producing zero saves is a valid outcome.
```

The verbatim string is the contract. The calling code can match on it cheaply.

**Structured-output prompts: provide a legal empty form.**

```
[prompt body]

If review found nothing actionable, output: {"saves": []}. Empty is valid; do not invent.
```

The empty array is a first-class member of the schema, not a degenerate case. The agent has a legal way to say "no signal."

**Two effects of the explicit exit:**

1. **Permission to fail-closed.** Zero output becomes a valid answer rather than a failure mode. The agent stops treating "be useful" as "produce findings."
2. **Cheap downstream routing.** A short fixed string (or a known-empty array) lets the calling code skip post-processing without parsing message contents.

**Anti-patterns to refuse:**

- Adding "be thorough" or "find everything you can" alongside the exit clause — these undermine the exit's permission.
- Asking for a minimum number of findings ("at least three issues") — this guarantees padding when fewer than three exist.
- Implicit exits ("if there are issues, list them") that don't name what to output when there are not.

## Evidence

**Trace 1 — Hermes-agent inspection prompts.** The pattern is borrowed verbatim from hermes-agent's review prompts at `run_agent.py:3025-3058`. The exit clause: *"If nothing is worth saving, just say 'Nothing to save.' and stop."* The follow-on comment at `run_agent.py:3060` explicitly notes that this clause is what enables the lightweight non-blocking review pattern. Without it, every review run would produce save-actions even when nothing changed — flooding the audit trail with noise.

**Trace 2 — Naming the failure mode, IndigenomicsAI session 2026-05-05.** During a hermes-agent investigation, the absence of the exit clause was identified as a structural cause of inspection-pad-out behavior in the practitioner's own audit passes. The pattern was named and codified for adoption in `/review-plan` skill prompts, subagent dispatches, and `claude -p --output-format json` invocations.

Source memory rule: `feedback_review_prompts_explicit_nothing_to_save_exit.md` in the IndigenomicsAI project memory, discovered 2026-05-05.

## Surfaces this rule applies to

- Plan-review prompts (Codex-side and self-side)
- Subagent dispatches asking for "open questions" / "missing tests" / "design issues"
- Session-harvest prompts (e.g., `Stop`-hook patterns)
- Any `claude -p --output-format json` invocation that may legitimately find nothing
- Audit passes whose "found nothing" outcome is informative rather than failure

## See Also

- [Batched Review as Trust Primitive](./batched-review-trust-primitive.md) — review surfaces the exit clause is a structural piece of.
- [Spec-Driven Development](../foundations/spec-driven-development.md) — the plan-review loop is the canonical inspection-class task.

## Open Questions

- **Generalization to non-review tasks.** The clause is well-defined for review/audit/harvest. Whether and how it applies to mixed tasks (build-and-review, propose-and-critique) is unsettled.
- **Multi-axis exits.** Some inspections have multiple output channels (e.g., open questions and must-fixes). The exit may need to be per-channel rather than one global "nothing to save."
- **Anti-gaming.** A sufficiently capable model may emit the exit string even when findings exist, if the prompt makes the exit too easy. The cost-asymmetry between false-empty and false-positive is task-specific.
