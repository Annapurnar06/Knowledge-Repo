---
title: Multi-Droid Workflows & Handoffs
description: Patterns for sub-agent handoffs, multi-Droid collaboration, and the Supervisor + Specialists model in Factory.
---

Multi-Droid workflows enable the primary assistant to orchestrate multiple specialized agents within a single session. This follows the Supervisor + Specialists pattern — one agent routes tasks to specialists with narrower instructions, tools, and constraints.

## Delegation Model

Factory's multi-agent architecture works through the **Task tool**:

1. The primary assistant (Supervisor) analyzes the user's request
2. It invokes the Task tool with a specific `subagent_type` (a custom Droid)
3. The subagent executes with its own context window, tools, and model
4. Results flow back to the primary assistant for synthesis

Each subagent runs with **fresh context** — this is a deliberate design choice to avoid prompt bloat and context window degradation over long sessions.

## Patterns in Practice

### Parallel Execution
Multiple Droids can run in parallel for independent tasks:
- One Droid analyzes code for security issues
- Another generates documentation
- A third reviews test coverage

### Sequential Handoffs
For dependent tasks, Droids execute sequentially:
1. Knowledge Droid researches the codebase and generates a spec
2. Code Droid implements based on the spec
3. A custom reviewer Droid validates the output

### Live Progress
The Task tool streams live progress, showing tool calls, results, and TodoWrite updates in real time as subagents execute. This gives visibility into what each Droid is doing without requiring interactive supervision.

## Model Mixing

Different Droids can use different models based on their task requirements:
- **Cheaper/faster models** for simple analysis, summarization, and routing
- **Larger/more capable models** for complex reasoning, code review, and multi-step analysis
- **`inherit`** to match the parent session's model when flexibility isn't needed

This model-mixing capability is a structural advantage — systems locked to one model family are constrained by that family's weakest capability.

## Context Isolation vs. Context Sharing

A key architectural decision in Factory's multi-agent system:

- **Each subagent gets a fresh context window** — no accumulated state from the parent session
- **The parent passes relevant context** in the task prompt — what the subagent needs to know
- **Git serves as the coordination mechanism** — for Missions, agents coordinate through shared repositories rather than shared memory

This design prevents the "mega-prompt" anti-pattern where a single context tries to hold everything, leading to degraded attention and increased hallucination.

## Founder Takeaway

The multi-Droid architecture addresses a fundamental tension in AI agent systems: **single agents hit limits** (context window, attention degradation, tool overload), but **multi-agent coordination is hard** (conflicts, duplication, drift). Factory's approach — centralized Supervisor with constrained Specialists, coordinated through git — represents a practical middle ground. The key insight is that **coordination matters more than raw agent capability**.

## Sources
- [Custom Droids Documentation](https://docs.factory.ai/cli/configuration/custom-droids)
- [Orchestrating AI Agents — HatchWorks](https://hatchworks.com/blog/ai-agents/orchestrating-ai-agents/)
- [Factory AI Guide — Sid Bharath](https://sidbharath.com/blog/factory-ai-guide/)
