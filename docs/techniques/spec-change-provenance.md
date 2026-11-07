---
doc_id: fc.technique.spec-change-provenance
doc_kind: pattern
status: draft
depends_on:
  - fc.spec-driven-development
relates_to:
  - fc.technique.batched-review-trust-primitive
concepts:
  - provenance
  - multi-agent-coordination
  - verification-discipline
---

# Spec Change Provenance

Record what prompted each change to a spec, and tie each approval to the exact bytes approved, so credit and review survive when many people and agents change the spec.

## Context

In multi-agent spec-driven development, many hands change one spec: the practitioner, their agents, review sessions, collaborators and their agents, and the results of running the work. Version control records who committed each change. It does not record who had the idea, what prompted it, or which version a person approved. When agents draft most of the text, commit authorship credits the agent that typed, and the real sources disappear.

## Problem

How do you keep track of where each spec change came from, and what was approved, when agents draft most changes from inputs that arrive through many channels?

## Forces

- **Ideas and authorship come apart.** An agent can write every change while the ideas come from people, reviews, test results and other teams.
- **Recording is cheap at the time and expensive later.** One line per commit costs seconds. Rebuilding the same record from logs a day later costs searches and still misses things.
- **Sources are often private.** Ideas arrive in direct messages, private reviews and session logs. Citing one in a shared repository shows its content to every reader.
- **Influence can be recorded, priority cannot.** "This change was prompted by that finding" is a fact about the project. "This person had the idea first" usually cannot be checked.
- **Approvals drift.** An approval given for one version gets cited for a later version that changed after review.

## Pattern

**A `Prompted-by:` line on every spec commit.** Name the review finding, message, test result or decision behind the change, as a link or an id. When the source is private, name it without its content, for example "a private message, not cited". A change with several sources gets several lines.

**Approvals tied to bytes.** An approval records the sha256 of the exact text approved. A commit that cites an approval names it, and a check compares the committed bytes with the approved ones. Anything added after the approval needs its own.

**Credit per finding.** Give each review finding an id, and have the commit that applies it name the id. Each reviewing person or agent session then gets credit for each finding it contributed, instead of a thank-you line in a header.

**Generate the credit table.** Build the record of who contributed what from what already exists: commit trailers, review verdicts, message ids, and the agent harness's own logs of the model, effort setting and prompts behind each request. A table written by hand afterwards is a reconstruction.

**Credit is a record, not a score.** Keep it as something people read and argue over. Once credit is summed into scores that pay out, contributors start working to be credited.

## Evidence

From one round of a spec co-written by two practitioners and their agents, in November 2026. The round's records are in a private repository and are not shared here.

- Afterwards, reconstructing where 18 changes came from took six searches of one session's logs, plus messages and commits, for changes made within about a day.
- The agent drafted all nine proposed changes and had the idea for seven of them. Of the other two, one came from the collaborator's side and one mostly from the practitioner. Commit authorship showed none of this.
- A commit cited one approval for nine proposals. The review behind it covered eight. The ninth was added afterwards and approved separately in chat.
- Four reviews shaped the draft before it was first shared. Nothing linked their findings to commits, so their contributions could not be attributed later.
- One idea, keeping observations apart from interpretations, reached the spec twice from two different sources. Crediting influence records both. Crediting priority would have to pick one.

This is one episode. The pattern needs a second before it moves past draft.

## See Also

- [Spec-Driven Development](../foundations/spec-driven-development.md). It notes that AI may move from reviewing specs to co-authoring them, and the evidence here suggests that has started.
- [Batched Review as Trust Primitive](batched-review-trust-primitive.md). Its approval event should record whether the plan was edited, and tying approvals to bytes extends that.
- [CRediT](https://credit.niso.org/), the contributor-roles taxonomy that research publishing uses to separate who had the idea from who wrote it.
