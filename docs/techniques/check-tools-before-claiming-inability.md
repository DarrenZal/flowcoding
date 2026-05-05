---
doc_id: fc.technique.check-tools-before-claiming-inability
doc_kind: pattern
status: draft
depends_on:
  - fc.flow-state-practice
derived_from:
  - fc.connection.practitioner-discipline-2026-05-05
relates_to:
  - fc.technique.governance-without-core-changes
concepts:
  - tool-discovery
  - capability-routing
  - default-deflection
---

# Check Tools Before Claiming Inability

Before saying "I do not have access to X" or "I cannot reach Y," exhaust the tool discovery surfaces — tool search, the available-skills list, deferred MCP tools, and existing project scripts. In a tool-rich harness, "no access" claims default-deflect work that is actually doable.

## Context

A practitioner runs an AI agent inside a harness that exposes a large, layered set of capabilities: a base set of always-loaded tools, a deferred set discoverable via search, a set of skills that auto-trigger on specific phrases or files, MCP servers that index external systems, and project-local scripts. The agent does not see all of this at once — most of the surface is loaded on demand. When a task touches an external system (calendar, email, vault, third-party API), the agent has a choice: claim "no access" from a default mental model, or check what the harness actually exposes.

## Problem

How does the agent decide whether a capability exists in the current harness, when most of the harness is not visible by default?

## Forces

- **Default deflection vs. tool discovery.** "I do not have access" is a fast, cheap reply. It is also wrong often enough that the cheapness is a liability — the operator now has to surface the gap, which costs more than tool discovery would have.
- **Discovery cost vs. flow.** Stopping mid-task to search tools breaks the agent's current motion. The cost has to stay low (~10 seconds, single tool call) to be worth doing routinely.
- **Pattern-matched mental model vs. current capability.** Agents trained on past harnesses pattern-match to "AI does not have access to your private email" — true in many contexts, false in this one. The pattern-match runs faster than the reality check.
- **Speculative claims propagate.** A wrong "no access" claim does not just block the current task — if it lands in a memory rule or a written reply, it can recur across sessions.

## Pattern

**Treat "I do not have access" as a discovery flag, not a closure.** When the iterator notices itself about to write any of the following:

- "I do not have access to your X"
- "I cannot reach Y"
- "We cannot see Z without [manual step]"
- "You would need to manually..."

...stop. The phrase is the trigger to run discovery, not the conclusion.

**Discovery checklist, in order of cost:**

1. **Tool search** for keywords matching the resource (e.g., `proton`, `calendar`, `gmail`, `notion`, `vault`).
2. **Available-skills list** in the current session — any skill that touches the resource.
3. **Deferred MCP server tools** — many are not in the initial tool list; load via tool search.
4. **Broad-coverage MCPs** — single tools often index multiple sources via sensors (e.g., a calendar tool covering both Proton-derived and Gmail-derived ICS invites).
5. **Existing project scripts** — sometimes a Python helper or skill script already wraps the resource.

**Restated phrasing.** Instead of *"I do not have access to your Proton calendar to check organizers,"* the right output is something like *"let me check what tools surface this — [tool search / scan available skills] — yes, the calendar tool indexes Proton-derived ICS, querying now."* The discovery move is visible in the output, so the operator can correct the discovery if it missed something.

**The 10-second budget.** Discovery has to stay fast or the rule fails. The checklist is ordered by speed: a tool search is one call; an MCP probe is two; a script search is a `find`. If all four miss, the inability claim is now well-grounded — and the discovery itself becomes evidence the operator can reuse next session.

## Evidence

**Trace 1 — IndigenomicsAI session 2026-05-05.** The iterator wrote "I do not have access to your Proton calendar" while drafting a meeting-organizer recommendation. The operator pushed back: *"you should have access to my proton emails! check your skills."* The deferred tool `mcp__personal-koi__calendar_events_by_date` was in the available-tools list the whole session, and indexes both Proton- and Gmail-derived ICS invites. The original claim was a pattern-match to a default ("AIs do not see private email") rather than verification of current capability.

The cost difference: ~10 seconds of tool search vs. the operator-side cost of detecting the gap and surfacing it explicitly, plus the compounding cost if the wrong claim had propagated into a written reply or a memory rule.

Source memory rule: `feedback_check_tools_before_claiming_inability.md` in the IndigenomicsAI project memory, discovered 2026-05-05.

## Generalization

The trigger generalizes beyond access claims. Anywhere the agent is tempted to assert a structural inability based on a default mental model — "I cannot run X kind of job," "I cannot persist state across sessions," "I cannot coordinate with another agent" — the same discovery move applies. The harness is usually richer than the surface tool list suggests, and the cost of checking is always lower than the cost of being wrong.

## See Also

- [Governance Without Core Changes](./governance-without-core-changes.md) — the hooks-and-skills architecture that makes harnesses extensible by definition; "what tools exist here" is intrinsically dynamic in such systems.
- [Flow State Practice](../foundations/flow-state-practice.md) — discovery is a phase-shift move, not a flow break, when budgeted correctly.

## Open Questions

- **When does the rule stop applying?** After a confirmed null discovery, the inability claim becomes legitimate. The claim should then be cached so the discovery is not re-run on every adjacent question.
- **Promotion to global discipline.** The pattern surfaced in a project context but is harness-level, not project-level. If it fires again across projects, it likely belongs in the global practitioner-config layer, not per-project.
- **Discovery noise.** Aggressive tool search across long sessions could pollute the context window. The 10-second budget is a soft cap; a hard ceiling on discovery rounds may be needed for very long sessions.
