---
title: Task Decomposition & Milestones
description: How Factory's Missions orchestrator decomposes complex projects into milestones, features, and validation checkpoints for reliable multi-day autonomous execution.
---

Missions is Factory's system for pursuing engineering goals autonomously over multi-day horizons. Rather than executing a single long-running prompt, the orchestrator decomposes work into structured units with built-in validation — the same pattern a senior engineer uses when breaking down a large project.

## Decomposition Hierarchy

### Milestones
The orchestrator breaks a project into milestones, each representing a meaningful checkpoint — a point where the system can verify progress before continuing. Milestones execute serially, ensuring each builds on validated prior work.

### Features
Within each milestone, work is subdivided into features. Each feature gets a fresh worker session with clean context, preventing the degradation that comes from accumulating too much state. Features within a milestone can run in parallel when there are no dependencies between them.

### Validation Phases
Every milestone ends with a validation phase. The orchestrator reviews completed work, runs the test suite, checks for regressions, and verifies integration across features. This is not optional — it is structurally embedded in the execution flow.

## Serial Execution with Targeted Parallelization

Milestones proceed sequentially. Features within a milestone can parallelize where safe to do so. This gives the orchestrator the reliability of sequential checkpointing while still exploiting parallelism for independent work items.

## Native Computer Use for Validation

Missions goes beyond running tests. The system uses native computer use to launch applications, navigate user flows, check rendering, and flag visual issues. This closes the gap between "tests pass" and "the feature actually works as intended."

## Multi-Model Orchestration

Missions leverages different models for different cognitive tasks:

- **Opus 4.6** for high-level planning and decomposition
- **Sonnet 4.6** for implementation work within features
- **GPT-5.3-Codex** for validation and review
- **Kimi K2.5** for research and information gathering

This reflects a practical insight: no single model is best at everything. Planning requires strong reasoning, implementation needs fast code generation, and validation benefits from a different perspective than the model that wrote the code.

## Real-World Execution Examples

Missions has completed substantial projects autonomously:

- **COBOL to Java Spring Boot migration** — 33.8 hours of autonomous execution
- **Rust HTTP tool from scratch** — 22.3 hours, building a complete tool in an unfamiliar language
- **Memory leak investigation** — 24.2 hours of deep debugging and root cause analysis
- **Tauri + React application** — 30 hours, full-stack desktop app development

These are not toy demos. They represent the kind of multi-day engineering work that would typically occupy a developer for a week or more.

## Founder Takeaway

The core insight is that **decomposition plus validation checkpoints is fundamentally more reliable than a single long-running session**. This mirrors how experienced engineering teams operate: break work into reviewable units, validate at each stage, and only proceed when the foundation is solid. The orchestrator pattern makes autonomous multi-day execution tractable by converting one hard problem (run perfectly for days) into many easier problems (run correctly for one feature, then verify).

## Sources
- [Introducing Missions — Factory AI](https://factory.ai/news/missions)
- [Missions Documentation — Factory Docs](https://docs.factory.ai/cli/features/missions)
