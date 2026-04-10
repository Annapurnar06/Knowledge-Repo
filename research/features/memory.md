---
title: Memory & Context Management
description: How Factory handles context compression, organizational memory, and multi-layer context.
---

Context is the primary bottleneck for autonomous coding agents. A model that can't see the relevant code, conventions, and organizational knowledge will produce plausible but wrong output. Factory's memory architecture addresses this through four distinct layers, each serving a different time horizon and scope.

## The Four Context Layers

### Layer 1: AGENTS.md — Project Conventions

Every repository can include an `AGENTS.md` file (or `.factory/` configuration) that captures project-level conventions: build commands, coding patterns, architecture decisions, testing requirements, and style guides. This file is ingested at session start, giving the agent immediate access to the tribal knowledge that would otherwise take a new engineer weeks to absorb.

This is the most impactful context layer per token — a well-written AGENTS.md eliminates entire classes of errors by telling the agent how the project actually works, not how a generic project might work.

### Layer 2: Dynamic Code Context

Factory doesn't dump the entire codebase into the context window. Instead, it performs **automatic relevant file search** based on the task at hand, using lazy loading to pull in only what's needed. Files are not duplicated across context — if a file is already loaded, it's referenced rather than re-inserted.

This approach is critical for large monorepos. In one demo, Factory showed **43% context capacity used** despite operating in a large monorepo — evidence that selective retrieval dramatically outperforms naive "stuff everything in" approaches.

### Layer 3: Tool Integrations

Enterprise context is scattered across tools. A bug report lives in Jira, the error trace is in Sentry, the discussion is in Slack, the spec is in Notion, the deployment config is in the CI/CD pipeline, and the performance data is in Datadog. Factory integrates with all of these, pulling relevant context from:

- **Slack** — thread context, decisions, requirements
- **Notion / Google Docs** — specifications, documentation
- **Jira / Linear** — ticket details, acceptance criteria, priority
- **Sentry / Datadog** — error traces, performance metrics, alert context
- **GitHub / GitLab** — PR history, review comments, CI results

This means the agent operates with the same information surface a human engineer would have — not just the code, but the full context around why the code needs to change.

### Layer 4: Organizational Memory

The most sophisticated layer. Factory maintains two types of persistent memory:

**User Memory (Private)**
- Environment setup and preferences
- Work history and interaction patterns
- Personal coding style and tool preferences
- Persists across sessions, visible only to the individual user

**Org Memory (Shared)**
- Style guides and coding conventions
- Security requirements and compliance rules
- Architectural decisions and patterns
- Shared across all users in the organization

Memory works by **recording stable facts** — not ephemeral conversation context, but durable knowledge about the user, the team, and the codebase that remains true across sessions.

## Synthetic Insights

At indexing time, Factory generates **"synthetic insights"** — pre-computed observations about code structure, patterns, and relationships. These insights are stored and retrieved alongside raw code context, meaning the agent starts with analytical context, not just raw files. This is analogous to a senior engineer who doesn't just read code but maintains a mental model of how the system fits together.

## Founder Takeaway

The four-layer memory architecture is the most defensible part of Factory's system. Raw model capability is a commodity — everyone has access to the same foundation models. But the ability to efficiently retrieve and compose the right context for each task is a compounding advantage. For vertical AI builders, invest heavily in context engineering: it determines whether your agent produces generic output or output that actually fits the target environment.

## Sources
- [Factory AI Guide — Sid Bharath](https://sidbharath.com/blog/factory-ai-guide/)
- [ZenML — Enterprise Autonomous Software Engineering with AI Droids](https://www.zenml.io/llmops-database/enterprise-autonomous-software-engineering-with-ai-droids)
