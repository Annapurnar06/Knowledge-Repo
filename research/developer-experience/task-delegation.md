---
title: Task Design & Delegation Patterns
description: How specs, auto-run modes, and automation patterns enhance the developer experience when working with autonomous agents.
---

Factory's developer experience is built around a core principle: **delegation over collaboration**. Instead of pair-programming interactively, developers assign work to agents and review results. The platform provides structured patterns to make this delegation effective.

## Specification Mode

Before building, developers can switch to Spec mode (Shift-Tab) to plan:

1. Describe what you want built
2. Droid generates a complete specification: acceptance criteria, implementation plan, technical details, file changes, testing strategy
3. Review and iterate — hit Escape to correct, approve when satisfied
4. Choose autonomy level for execution

Approved specs are saved as markdown files in `.factory/docs/`, creating a decision record that new team members and future sessions can reference.

## Autonomy Levels

Three levels of delegation control:

| Level | Behavior |
|-------|----------|
| **Low** (Manual) | Allow file edits, approve every other change |
| **Medium** (Safe) | Droid handles reversible changes automatically, asks for risky ones |
| **High** (Full) | Full autonomy, Droid handles everything |

The recommendation is to start with low autonomy and increase as trust builds.

## Droid Exec (Headless Mode)

For automation and CI/CD, Droid Exec runs non-interactive commands:

```bash
droid exec --auto high "run tests, commit all changes, and push to main"
```

Use cases include CI pipelines, cron jobs, pre-commit hooks, and batch scripts. The agent follows instructions and exits when complete.

## Custom Slash Commands

Reusable commands for routine actions:
- Static markdown templates (content injected into conversation)
- Executable scripts (run in your environment)
- Examples: `/run-test`, `/security-review`, `/migrate-service`

## AGENTS.md

The primary context file that documents project conventions for agents:
- Build commands, test commands, code style
- Project layout and architecture
- Development patterns, security requirements, git workflow
- Multiple files supported: `/AGENTS.md` (repo-level), `/packages/api/AGENTS.md` (service-level)

## Founder Takeaway

The delegation patterns here — specification mode, graduated autonomy, headless execution — represent a UX framework for human-AI task handoff. The key insight is that **the planning phase creates most of the value**. Structured specs reduce ambiguity, autonomy levels manage risk, and headless mode enables fully automated workflows. This pattern applies broadly to any vertical AI system where you need humans to direct agents effectively.

## Sources
- [Factory AI Guide — Sid Bharath](https://sidbharath.com/blog/factory-ai-guide/)
- [Droid Exec Documentation](https://docs.factory.ai/cli/droid-exec/overview)
