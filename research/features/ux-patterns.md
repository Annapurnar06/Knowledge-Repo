---
title: UX Patterns Across Interfaces
description: How Factory's developer experience works across CLI, IDE, web, desktop, and mobile.
---

Factory's multi-surface approach to developer experience reflects a core conviction: autonomous agents shouldn't be locked to a single interface. Different tasks, contexts, and users demand different interaction patterns. The UX architecture spans five surfaces, each optimized for distinct workflows.

## Interfaces

### CLI (Primary)
The terminal is Factory's native surface. The CLI offers the lowest latency, deepest tool integration, and most direct control over agent behavior. Power users and automated workflows (CI/CD, scripts) operate here. The CLI supports the full feature set — sessions, Droids, Missions, tool configuration, and model selection.

### IDE Extensions
Factory ships extensions for **VS Code**, **JetBrains**, and **Zed**. These embed agent capabilities directly into the editor, enabling inline code generation, contextual chat, and agent-assisted refactoring without leaving the development environment.

### Web App
A chat-based interface accessible via browser. Connect a repository and interact with Droids through a conversational UI. Lower barrier to entry than the CLI, suitable for non-terminal-native users and quick interactions.

### Desktop App
Native **macOS and Windows** application with multi-session management. The desktop app adds capabilities that the CLI can't easily provide:

- **Multi-session sidebar** — manage and switch between concurrent agent sessions
- **VS Code integration** — deep linking between the desktop app and the editor
- **AI-native visualization** — Mermaid diagrams, charts, and tables rendered inline
- **Multi-agent sessions** — coordinate multiple Droids from a single interface

### Mobile
Full Factory experience on phone and tablet. Useful for monitoring agent progress, reviewing PRs, and triaging tasks while away from a workstation.

## Interaction Modes

### Default Mode
Standard conversational interaction. The agent responds to prompts and executes approved actions.

### Automatic Mode
The agent proceeds without waiting for approval on each step. Configurable with three **autonomy levels**:

| Level | Behavior |
|-------|----------|
| **Manual approval** | Every action requires explicit user confirmation |
| **Allow safe commands** | Read-only and low-risk actions proceed automatically; destructive actions require approval |
| **Allow all** | Full autonomy — the agent executes all actions without pausing |

### Planning Mode (Shift-Tab)
Activates **specification mode** — the agent plans before building. It produces a detailed implementation plan, saves it to `.factory/docs/`, and waits for approval before writing any code. This is critical for complex features where the cost of a wrong approach is high.

## Saved Specifications

Plans generated in specification mode are stored as markdown files in `.factory/docs/`. These serve as living documentation of architectural decisions and implementation strategies, version-controlled alongside the code they describe.

## Enterprise Adoption Data

The multi-surface approach has measurable impact:

- Users with **CLI + Desktop** are **2x faster** than CLI-only users
- Desktop users run **4.6x more sessions** than CLI-only users

This isn't just convenience — the desktop app's multi-session management and visualization capabilities fundamentally change how developers interact with autonomous agents, enabling concurrent delegation rather than sequential task assignment.

## Founder Takeaway

The UX lesson for vertical AI builders: don't ship a single interface and assume it fits all users. The power users who drive adoption need low-level control (CLI), but the broader organization needs accessible surfaces (desktop, web, mobile) to reach critical mass. The 4.6x session multiplier from the desktop app proves that reducing interaction friction directly increases agent utilization — and utilization is the metric that drives ROI.

## Sources
- [Factory AI Guide — Sid Bharath](https://sidbharath.com/blog/factory-ai-guide/)
- [Factory Desktop — Factory News](https://factory.ai/news/factory-desktop)
