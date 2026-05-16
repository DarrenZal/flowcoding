---
doc_id: fc.methods.agentic-workflow-portability
doc_kind: methods
status: draft
depends_on:
  - fc.project-vision
  - fc.methods.governance-architecture
  - fc.connection.claude-code
relates_to:
  - ic.connection.agentic-workflow-portability
sources:
  - path: darren-workflow/docs/agentic-coding-system-translation-primitives.md
    title: "Agentic Coding System Translation Primitives"
    type: primary
  - path: darren-workflow/docs/codex-claude-parity.md
    title: "Codex / Claude Parity"
    type: primary
  - url: https://github.com/openai/skills/tree/main/skills/.curated/migrate-to-codex
    title: "OpenAI migrate-to-codex skill"
    type: primary
---

# Agentic Workflow Portability Method

This method governs how to translate a working agentic coding workflow from one runtime to another without silently losing capability, safety, memory, or human ergonomics.

It is not a product-specific migration recipe. Product-specific tools such as OpenAI's `migrate-to-codex` skill are useful auditors and converters, but the durable method is primitive-level: identify what the workflow actually does, choose the smallest portable primitive that preserves that behavior, then verify the translated workflow in the target runtime.

## When To Use

Use this method when:

- a Claude Code slash command should also work in Codex;
- a Codex skill should be usable by Claude Code or another agent runtime;
- MCP, plugin, hook, or subagent configuration needs parity across runtimes;
- a team wants to publish agentic coding practice without binding it to one vendor's UI;
- a workflow failed because an agent lacked a capability the sibling agent already had.

Do not use this method for ordinary code portability. This is about the agentic practice layer: instructions, skills, commands, hooks, tool connectors, permissions, memory, and invocation surfaces.

## Translation Procedure

### 1. Inventory the source workflow

List every source surface the workflow depends on:

- instructions (`AGENTS.md`, `CLAUDE.md`, project docs)
- skills or prompt files
- slash commands
- deterministic scripts
- hooks
- MCP servers or app connectors
- subagents
- plugin packaging
- permissions and approval assumptions
- memory or retrieval surfaces
- human invocation path

For Claude Code to Codex work, run the official migrator in audit mode before writing anything:

```bash
MIGRATE_TO_CODEX='python3 ~/.codex/skills/migrate-to-codex/scripts/migrate-to-codex.py'

$MIGRATE_TO_CODEX --source ~/.claude --scan-only
$MIGRATE_TO_CODEX --source ~/.claude --target ~/.codex --doctor
$MIGRATE_TO_CODEX --source ~/.claude --target ~/.codex --plan
$MIGRATE_TO_CODEX --source ~/.claude --target ~/.codex --dry-run
```

### 2. Classify by primitive, not product surface

Map each source surface to the behavior it carries:

| Behavior | Likely primitive |
| --- | --- |
| Standing norms | Instructions |
| Repeatable AI workflow | Skill |
| Deterministic/authenticated action | Script or CLI behind a skill |
| External typed capability | MCP server or app connector |
| Lifecycle enforcement | Hook |
| Scoped worker role | Subagent/custom agent |
| Installable distribution | Plugin |
| Trust boundary | Permission/sandbox policy |
| Continuity across sessions | Memory/governance surface |

The important move is to avoid surface mirroring by reflex. A slash command is often better translated into a skill plus script. A plugin may need to split into a skill, MCP server, and hook. A hook may need to degrade into a Stop-time verification script if the target runtime lacks the same event.

### 3. Choose the conversion operator

Use one of these operators:

| Operator | Meaning |
| --- | --- |
| Lift | Move product-specific workflow text into a portable skill. |
| Wrap | Put deterministic behavior behind a script or CLI, then invoke it from the skill. |
| Mirror | Copy config when the two runtimes share the same concept closely enough. |
| Degrade explicitly | Preserve partial behavior and name what no longer holds. |
| Split | Separate bundled behavior into its real primitives. |
| Package | Bundle stable shared primitives for installation. |
| Audit | Use a converter to expose gaps without trusting it as proof. |

### 4. Preserve the safety contract

Before calling a translation complete, check:

- Does the target runtime enforce the same permission boundary?
- Are tool restrictions actual policy or only prompt guidance?
- Do hooks run on the same lifecycle event?
- Are secrets passed without being printed?
- Does the human approve the same class of actions?
- Is memory provenance preserved?

If any answer changes, document the degradation in the translated artifact.

### 5. Verify with runtime evidence

Run the translated workflow in the target runtime. Minimum verification:

- syntax/schema validation for generated skills/config;
- dry-run for destructive or external actions;
- one end-to-end invocation for the main workflow path;
- explicit note of any behavior that could not be verified.

## Current Practice in `darren-workflow`

The 2026-05-15 parity pass produced this policy:

- `skills/<name>/SKILL.md` is the portable workflow source of truth.
- `scripts/<domain>/...` carries deterministic or authenticated behavior.
- MCP config is mirrored into Codex with a purpose-built script that avoids printing secrets.
- Claude slash commands remain Claude-specific ergonomics unless lifted into shared skills.
- Hooks are runtime-specific until their event model is validated on both sides.
- Plugins are distribution units, not the canonical source of the workflow.

That policy is the current working example of this method.

## Anti-Patterns

- **Surface-copying:** assuming a Claude command should become a Codex command just because the names match.
- **Permission collapse:** converting `allowed-tools` or equivalent source policy into prose guidance without naming the loss.
- **Hook optimism:** assuming lifecycle events are equivalent across runtimes.
- **Plugin enclosure:** leaving useful workflow knowledge trapped inside a plugin bundle instead of lifting the reusable method into a skill/script/MCP substrate.
- **Memory amnesia:** copying workflow text while dropping the memory, provenance, or retrieval surfaces that made it reliable.

## Relationship to Intelligence Commons

The IC bridge note `ic.connection.agentic-workflow-portability` owns the intelligence-primitives framing: agentic control, capability routing, and memory governance. This Flow Coding method owns the practitioner procedure: how an operator actually audits, translates, and verifies workflows between agentic coding systems.
