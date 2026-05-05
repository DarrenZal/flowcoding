---
doc_id: fc.spec-driven-development
doc_kind: foundation
status: active
depends_on:
  - fc.project-vision
relates_to:
  - fc.connection.claude-code
---

# Spec-Driven Development

The practice of externalizing development intent into a governed specification before execution begins. The spec resolves unknowns, states constraints, defines acceptance criteria, and creates the structural conditions for confident, sustained flow.

## Core Claim

A specification is not documentation. It is a **pre-flight** — a structured artifact that transforms ambiguous intent into executable direction. The transition from vibe coding to flow coding passes through the spec: without it, mid-execution uncertainty breaks the flow state repeatedly. With it, direction is held by an artifact rather than working memory, and the practitioner can commit fully to the build.

The spec is also a **coordination artifact**: it is the medium through which the human practitioner and the AI system reach alignment on what is being built, why, and under what constraints — before either invests effort in building. This alignment work is not overhead. It is the craft.

## Spore Derivation

A spec is a constitutional artifact that gives a development task:

- **Explicit intent** (Goal, non-goals, what success looks like)
- **Documented constraints** (what the system must not do, what boundaries apply)
- **Visible authority flow** (what must be decided before execution vs. during)
- **Shared context** for human and AI agents working inside the task

The plan-review cycle — spec → structured review → resolve → execute — is an instance of **discourse-as-governance** at the personal-project scale. The questions gate (are there unresolved unknowns?) and the must-fix gate (are there blockers?) are structured discourse over the spec before execution begins. The spec itself is the proposal; the review is adversarial argument; the resolved spec is the decision artifact.

## IC Derivation

Spec-driven development is an **Agentic Control** primitive at the task level. The spec is the control plane for a development session:

- **Query planning:** The spec routes work — what to build, in what order, with what dependencies
- **Safety guards:** Non-goals and constraints define the scope boundary; the AI operates within them
- **Context management:** The spec surfaces what the AI needs to know before execution, preventing mid-session context reconstruction
- **Evaluation:** Acceptance criteria are the eval contract — structured claims about what the completed work must satisfy

This maps directly to IC's framing of agentic control: authorization (what the agent is permitted to do), context management (what the agent needs to know), and evaluation (how outputs are assessed). The spec formalizes all three before a line of code is written.

## The Minimum Viable Spec

A spec that can gate execution contains six sections:

| Section | Function |
|---|---|
| **Goal** | The single outcome this task achieves. One sentence. |
| **Non-goals** | What this task explicitly does not do. Scope boundary. |
| **Constraints** | What the solution must not violate. Architectural, operational, or ethical bounds. |
| **Assumptions** | What is taken as true without verification. Makes implicit dependencies explicit. |
| **Acceptance Criteria** | Testable conditions that define done. The eval contract. See [Specific Observation → Specific Claim](../techniques/specific-observation-to-specific-claim.md) for the verification-discipline technique that prevents AC-overclaim ("verified by direct observation" hand-waves). |
| **Rollback** | How to undo the change if it fails. Reversibility is a first-class concern. |

The minimum viable spec is not a long document. A well-formed spec for a bounded task may be eight to twenty lines. The discipline is in the thinking, not the word count.

## The Plan-Review Loop

The reference implementation is the plan-review loop developed in the darren-workflow project:

```
/new-plan <title>    →  create spec from governed skeleton (all six sections present, lint-green)
fill in the plan     →  resolve goal, constraints, acceptance criteria
/review-plan         →  structured review: lint gate + questions gate + must-fix gate
resolve findings     →  edit spec, re-run review
repeat until         →  NEXT: Execute (zero questions, zero blockers)
```

The review cycle is adversarial by design. The reviewer (human, Claude, or Codex) identifies unresolved unknowns and blockers. The planner resolves or consciously defers them. The loop terminates when the spec is clean — not when it feels done. This distinction matters: feeling done is a cognitive state; being clean is a structural one.

**Key production observation:** Multi-round review consistently improves execution outcomes. Plans that pass the questions gate on the first round tend to surface mid-execution unknowns that break flow. Plans that require two or three review rounds tend to execute cleanly. The review cost is lower than the recovery cost.

## Relationship to Flow State

Flow state requires two conditions: clear proximal goals and feedback. The spec provides the first condition directly — every acceptance criterion is a clear proximal goal. The AI's implementation attempts provide the feedback. Together they create the attentional absorption that characterizes flow.

The deeper mechanism: flow state requires working memory to be fully committed to the current action. When direction is ambiguous, working memory splits — part executes, part holds open questions, part anticipates direction changes. The spec resolves open questions before execution, allowing full working memory commitment. This is why spec-driven development is not adjacent to flow coding. It is constitutive of it.

## Tensions and Unresolved Questions

**Speccing cost vs. task size:** The plan-review loop as currently implemented is calibrated for significant development tasks (hours to days). For small tasks (minutes), the overhead exceeds the benefit. The right trigger for formal spec-driven development vs. lightweight intention-setting is not yet codified.

**Spec vs. exploration:** Some development work is genuinely exploratory — the goal cannot be stated until some building has occurred. Spec-driven development is not the right frame for pure exploration. The boundary between exploration (where vibe coding is appropriate) and implementation (where spec-driven development is appropriate) is a practice judgment, not a rule.

**AI as spec co-author:** In current practice, the spec is primarily human-authored with AI review. As AI capabilities increase, the spec co-authoring dynamic may shift — AI systems may propose specs based on brief intent statements. The governance implications of AI-authored specs (who bears authority, how is provenance attributed) are not yet resolved.
