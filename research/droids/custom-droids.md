---
title: Custom Droids (Subagents)
description: How to define custom Droids with their own prompts, models, and tool policies for specialized task delegation.
---

Custom Droids are reusable subagents defined as Markdown files. Each carries its own system prompt, model preference, and tooling policy so the primary assistant can delegate focused tasks — like code review, security checks, or research — without re-typing instructions.

## How They Work

Custom Droids live as `.md` files in either:
- **Project scope:** `<repo>/.factory/droids/` — shared with teammates, version-controlled
- **Personal scope:** `~/.factory/droids/` — follows you across workspaces

The CLI scans these folders, validates each definition, and exposes them as `subagent_type` targets for the Task tool. Project definitions override personal ones when names match.

## Configuration Format

Each Droid file is Markdown with YAML frontmatter:

```yaml
---
name: code-reviewer
description: Focused reviewer that checks diffs for correctness risks
model: inherit
tools: read-only
---

You are the team's senior reviewer. Examine the diff and:
- Flag correctness, security, and migration risks
- List targeted follow-up tasks if changes are required
- Confirm tests or manual validation needed before merge
```

### Key Metadata Fields

| Field | Notes |
|-------|-------|
| `name` | Required. Lowercase letters, digits, `-`, `_`. Drives the subagent_type value. |
| `description` | Optional. Shown in UI. Keep under 500 chars. |
| `model` | `inherit` (use parent session model) or specify a model ID. |
| `reasoningEffort` | Optional. `low`, `medium`, or `high` for compatible models. |
| `tools` | Omit for all tools, use a category (`read-only`), or specify an array like `["Read", "Grep", "Glob"]`. |

### Tool Categories

| Category | Tools | Purpose |
|----------|-------|---------|
| `read-only` | Read, LS, Grep, Glob | Safe analysis and file exploration |
| `edit` | Create, Edit, ApplyPatch | Code generation and modifications |
| `execute` | Execute | Shell command execution |
| `web` | WebSearch, FetchUrl | Internet research and content |
| `mcp` | Dynamically populated | Model Context Protocol tools |

## Why Use Custom Droids

- **Faster delegation** — encode complex checklists once, reuse with a single tool call
- **Stricter safety** — limit agents to read-only, edit-only, or curated tool sets
- **Context isolation** — each subagent runs with a fresh context window, avoiding prompt bloat
- **Repeatable reviews** — capture team-specific review, testing, or release gates as versionable code

## Hierarchical Control (Enterprise)

Custom Droids follow the same hierarchical settings as the rest of Factory:

- **Org-level**: Admins publish "blessed" Droids encoding security-reviewed workflows
- **Project-level**: Teams add specialized Droids in `.factory/droids/` with project-specific logic
- **User-level**: Personal Droids in `~/.factory/droids/` for individual workflows (must still respect org/project policies)

## Founder Takeaway

Custom Droids are the mechanism by which teams encode tribal knowledge as code. This pattern — defining agent behavior declaratively in version-controlled files — is a powerful primitive for scaling AI-assisted workflows. It enables a "Supervisor + Specialists" architecture where the primary agent delegates to purpose-built helpers, each with constrained tools and focused prompts.

## Sources
- [Custom Droids Documentation](https://docs.factory.ai/cli/configuration/custom-droids)
- [Factory AI Guide — Sid Bharath](https://sidbharath.com/blog/factory-ai-guide/)
