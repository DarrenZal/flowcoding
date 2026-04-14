---
doc_id: fc.technique.governance-without-core-changes
doc_kind: pattern
status: draft
depends_on:
  - fc.spec-driven-development
related_claims:
  - "orn:koi-net.claim:0cdf2614e248ad3ab383335221c945d8"
  - "orn:koi-net.claim:1d1dd8795980c806c7f0482b37f4e631"
derived_from:
  - fc.connection.claude-code
concepts:
  - hooks-and-skills
  - governance-injection
  - capability-reuse
---

# Governance Without Core Changes

Extend and govern an agentic system through two ports — event-driven hooks that shape behavior at lifecycle points, and markdown-native skills that package reusable capabilities — without modifying the core loop.

## Context

An agentic system becomes useful when it is extended: teams want policy enforcement ("never run `rm -rf` without review"), workflow injection ("after every code edit, run the formatter"), and shared expertise ("when processing a meeting note, follow these five steps"). If all of this requires editing the core, the system becomes a monolith and extensions become forks.

## Problem

How do you let teams govern and extend an agentic system without forking its core?

## Forces

- **Deterministic vs. AI-mediated.** Some rules ("block this command") must fire deterministically; others ("when should I refactor") are judgment calls the AI should make. The extension surface should support both.
- **Declarative vs. procedural.** Policy rules are best expressed declaratively; capability packages (multi-step procedures) need prose and examples. One port for each.
- **Shareable vs. local.** Both hooks and skills should load from multiple sources — user config, project config, plugins — so teams can share and individuals can override.
- **Budget-aware discovery.** The AI must know what skills exist to use them; listing too many eats the context window and poisons the prompt cache.

## Pattern

**Hooks: event-driven, deterministic, lifecycle-scoped.** Hooks are shell commands or small prompts that fire at specific lifecycle points (`PreToolUse`, `PostToolUse`, `UserPromptSubmit`, `SessionStart`, `Stop`). They receive structured JSON input and return structured JSON output. Exit codes carry meaning: 0 = success, 2 = block. `PreToolUse` hooks can return a permission decision and even rewrite tool input before execution — this is the injection point for governance: "block `Bash(rm *)`", "require review before `Write(.env)`", "wrap all bash commands in a tracing prefix".

**Skills: markdown-native, context-budgeted, multi-source.** Skills are markdown files with YAML frontmatter (`name`, `description`, `whenToUse`, `allowed-tools`, `hooks`, `paths`, `context`). They load from layered sources: bundled (shipped with the tool), project (checked into the repo), user (in the practitioner's config), plugin, and MCP. The system injects a skill listing into each prompt — context-budgeted to a small fraction of the window, with bundled skills prioritized so discovery stays affordable.

**Path-scoped activation and isolated execution.** Skills declare `paths: [globs]` to auto-activate only on matching files, keeping discovery noise low. Skills can run in a forked subagent (`context: fork`) with their own token budget — useful when the skill's work is long or doesn't need to pollute the parent conversation.

**Let skills carry their own hooks.** Skills can declare hooks in their frontmatter. This gives a composable governance unit: a single skill bundle can ship a procedure plus the safety checks that should run when it executes.

## Evidence

- Hook event schema with permission override: `src/types/hooks.ts:73-107` — `PreToolUse` / `PostToolUse` `hookSpecificOutput` unions include `permissionDecision: allow | deny | ask` and `updatedInput`.
- Hook execution: `src/utils/hooks.ts:1952-2100` is the main `executeHooks` generator; `src/utils/hooks/hooksConfigManager.ts:270-365` routes via matchers across settings/plugin sources; `src/utils/hooks/execPromptHook.ts:21-80` evaluates prompt-type hooks via Haiku.
- Skill frontmatter contract: `src/skills/loadSkillsDir.ts:185-265` parses `name`, `description`, `whenToUse`, `allowed-tools`, `model`, `hooks`, `paths`, `context`.
- Context-budgeted discovery: `src/tools/SkillTool/prompt.ts:21-29` caps the skill listing at ~1% of the context window; bundled skills prioritized and never truncated.
- Path-scoped + forked execution: `src/skills/loadSkillsDir.ts:159-178` (`parseSkillPaths`); `src/tools/SkillTool/SkillTool.ts:122-250` (`executeForkedSkill`).

## See Also

- [Bridge note: Claude Code → Flow Coding](../research/connections/claude-code-flow-coding.md) (§Hooks + Skills, claims C5–C6)
- [Spec-Driven Development](../foundations/spec-driven-development.md)
