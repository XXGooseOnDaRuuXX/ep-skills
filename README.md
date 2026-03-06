# EP Skills Library

Reusable, tool-agnostic agent skills for any EP agent.

## What This Is

A portable library of skill content (markdown) that any agent can pull at dispatch time. Skills here are NOT tied to specific tools or EP infrastructure. They're frameworks, patterns, and reference material that make agents better at their jobs.

## What Goes Here vs EP Registry

| Location | Content | Examples |
|----------|---------|----------|
| **EP Registry `skills/`** | Tool-specific instructions (L2/L3) | linear, hubspot, v0, stripe |
| **EP Registry `knowledge/`** | EP-specific reference docs | cloudflare, ep-cron |
| **EP Registry `prompts/`** | Workflow triggers with arguments | triage, morning-brief |
| **This repo** | Reusable, project-agnostic skills | copywriting, frontend, dark-theme |

## How Agents Use This

### ep-code (GLM-5)
```
ep_code dispatch skills=["frontend","copywriting"] ticket=ECO-440
```
ep-code does a sparse checkout of the requested skill folders and injects them into its system prompt alongside CLAUDE.md.

### Claude Code / Any MCP Agent
EP Registry's `load_skill` tool can be extended to serve content from this repo, or agents can read directly via GitHub raw URLs.

### Manual
```bash
curl -sL https://raw.githubusercontent.com/ena-pragma/ep-skills/main/frontend/SKILL.md
```

## Structure

Each skill is a folder with a `SKILL.md` and optional reference files:

```
ep-skills/
├── frontend/
│   └── SKILL.md
├── copywriting/
│   └── SKILL.md
├── dark-theme/
│   └── SKILL.md
└── README.md
```

## Adding Skills

1. Create a folder with your skill name (kebab-case)
2. Write a `SKILL.md` following the standard format
3. Add reference files as needed (lookup tables, examples, etc.)
4. PR it. Skills are reviewed like code.

## Skill Format

```markdown
---
name: skill-name
description: One-line description
when_to_use: When an agent should load this skill
---

# Skill Name

## Quick Reference
[The 80% an agent needs immediately]

## Patterns
[Detailed frameworks, examples, anti-patterns]

## Gotchas
[Common mistakes, edge cases]
```
