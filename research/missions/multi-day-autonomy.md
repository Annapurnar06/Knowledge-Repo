---
title: Multi-Day Autonomy
description: Mechanisms enabling Factory Missions to sustain autonomous execution over hours, days, and weeks — fresh sessions, token economics, skill-based learning, and failure recovery.
---

Multi-day autonomous execution is not achieved by running a single session for longer. Missions sustains autonomy through architectural choices that avoid context window degradation, manage token economics efficiently, and recover from failures structurally.

## Duration Distribution

The data on mission runtimes reveals a power-law distribution:

- **Median mission** runs approximately 2 hours
- **65%** run longer than 1 hour
- **37%** exceed 4 hours
- **14%** run beyond 24 hours
- **Longest recorded mission** ran for 16 days, with some reported up to 40 days

This is not a system designed for quick fixes. The architecture is optimized for sustained, multi-day engineering work.

## Fresh Sessions Prevent Context Degradation

The key mechanism for long-running reliability is that each feature gets a fresh worker session. Rather than accumulating context until the window fills and quality degrades, Missions resets context for each discrete unit of work. The orchestrator maintains high-level state; workers operate with clean context focused on their specific task.

This is the architectural answer to the context window problem. Instead of fighting degradation, eliminate it by design.

## Token Economics

Missions exhibits distinctive token patterns compared to normal Droid sessions:

- **Message rate:** ~3 messages per minute (vs. 6 for normal sessions)
- **Message density:** ~19K tokens per message (vs. 11K for normal)
- **Total tokens:** 12x more tokens consumed than a normal session
- **Burn rate:** Roughly equivalent at ~45K tokens per minute

Missions doesn't burn tokens faster — it sustains the same burn rate over a much longer period. The lower message frequency with higher per-message density reflects deeper, more deliberate reasoning per step.

## Skill-Based Learning

The orchestrator identifies reusable patterns across tasks and workers refine a skill library over time. This means a mission that encounters a recurring pattern (e.g., a specific test setup, a migration pattern, a deployment configuration) can encode that knowledge for subsequent features and milestones. The system gets more efficient as it works, rather than re-discovering solutions.

## Beyond Code

Missions is not limited to code generation. The system handles research papers, ML model training pipelines, and other complex multi-step workflows. The decomposition and validation pattern generalizes beyond software engineering to any domain where work can be broken into verifiable stages.

## Failure Recovery

Validation is not just a quality check — it is the failure recovery mechanism. When the validation phase catches errors, the orchestrator creates follow-up repair tasks rather than attempting to patch within the same context. This means failures are handled structurally: identify the problem, create a fresh session scoped to the fix, validate again.

This avoids the compounding-error problem where an agent tries to fix its own mistakes within a degraded context, often making things worse.

## Founder Takeaway

Multi-day autonomy is achieved **through fresh sessions and iteration, not by maintaining a single long context**. This is a counterintuitive architectural choice — it seems wasteful to discard context — but it is the pragmatic path to reliability at scale. The system trades context continuity for context quality, and uses structured orchestration to maintain coherence across the boundary. This pattern is likely generalizable to any domain requiring long-running autonomous agents.

## Sources
- [Introducing Missions — Factory AI](https://factory.ai/news/missions)
- [Factory AI Analysis — 36Kr](https://eu.36kr.com/en/p/3704948816556163)
