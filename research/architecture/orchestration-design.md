---
title: Orchestration Design Patterns
description: Architectural patterns for production agent orchestration — supervisor routing, state management, deterministic control flow, and model routing strategies.
---

Production agent orchestration is a systems design problem, not a prompting problem. The patterns below reflect how Factory structures multi-agent coordination at scale.

## Supervisor + Specialists

Factory uses a Supervisor + Specialists pattern. A single supervisor agent receives inbound tasks and routes them to specialist agents, each with narrower instructions, constrained tool access, and domain-specific system prompts. This avoids the failure mode of monolithic agents that try to do everything — specialists are easier to test, evaluate, and improve independently.

The supervisor handles task decomposition, delegation, result aggregation, and error recovery. Specialists focus on execution within well-defined boundaries.

## Control Plane vs Data Plane

Orchestration separates cleanly into two layers:

- **Control plane**: Manages state, routing decisions, policy enforcement, budget tracking, and approval gates. This is where orchestration logic lives — which agent handles what, under what constraints, with what fallbacks.
- **Data plane**: Handles actual tool execution and side effects — running commands, calling APIs, reading files, writing code. The data plane is stateless from the orchestration perspective; all durable state lives in the control plane.

This separation means you can change routing logic without touching tool implementations, and vice versa.

## State Management

Agent systems require three distinct categories of state:

- **Session context** (short-lived): Current conversation, working memory, intermediate reasoning. Discarded after task completion.
- **Task state** (durable workflow checkpoints): Where a multi-step task stands — which steps completed, which failed, what's pending. Survives agent restarts and enables resumption.
- **System state** (policies and permissions): Organization-level configuration — which tools are enabled, what approval policies apply, budget limits, model preferences. Changes infrequently.

Conflating these categories is a common source of bugs. Session context that should be ephemeral gets persisted; policy state that should be durable gets lost between sessions.

## Deterministic State Machine Orchestration

Rather than letting LLMs free-form decide what to do next, production orchestration uses explicit states and transitions. The LLM decides *within* constrained options at bounded decision points — it picks which branch to take, not whether branches exist.

This means workflows are:
- **Predictable**: You can enumerate all possible states and transitions.
- **Auditable**: Every state transition is logged with the decision rationale.
- **Recoverable**: Failed states have explicit retry or fallback paths.
- **Testable**: You can unit test individual state transitions without running the full agent.

## Two-Phase Actions

For irreversible or expensive operations, Factory applies a Plan → Validate → Execute pattern:

1. **Plan**: The agent proposes an action with full details (what it will do, why, expected outcome).
2. **Validate**: The plan is checked against policies, budget limits, and optionally human approval.
3. **Execute**: Only after validation passes does the action run.

This prevents the class of failures where an agent confidently executes a destructive action before anyone can review it.

## Event-Driven Orchestration

Long-running tasks use event-driven architecture — queues, workers, and timeouts replace synchronous request-response patterns. This enables:

- **Parallel execution**: Multiple independent subtasks run concurrently.
- **Resilience**: Worker failures don't crash the entire workflow; tasks get retried or rerouted.
- **Timeouts**: Stuck tasks are detected and escalated rather than silently hanging.
- **Backpressure**: The system degrades gracefully under load rather than cascading failures.

## Model Routing + Fallbacks

Not every task needs the most capable (and expensive) model. Requests are routed based on:

- **Task type**: Simple code formatting vs complex architectural reasoning.
- **Complexity**: Estimated token requirements and reasoning depth.
- **Risk tier**: High-risk operations get more capable models with additional validation.
- **Latency SLO**: Interactive tasks route to faster models; batch tasks can use slower, more capable ones.
- **Budget**: Per-task and per-organization cost constraints shape model selection.

Fallback chains ensure that if the primary model is unavailable or fails, the request degrades to an alternative rather than failing entirely.

## Anti-Patterns

Patterns that consistently fail in production agent systems:

- **Unbounded autonomy**: Letting agents decide their own scope without policy constraints. Leads to runaway costs and unexpected side effects.
- **Hidden state in conversations**: Using conversation history as the sole state store. Fragile, non-inspectable, and lost on context window overflow.
- **Mega-prompts**: Stuffing all instructions into a single enormous system prompt instead of decomposing into specialized agents.
- **No policy boundaries**: Trusting the model to self-regulate without explicit guardrails on tool access, budget, and scope.
- **No observability**: Running agents without structured logging, tracing, or metrics. You can't improve what you can't measure.

## Founder Takeaway

Production orchestration is a systems design problem, not a prompting problem. The key primitives are deterministic state, tool contracts, policy enforcement, and observability. Get the control plane right and the LLM becomes a decision engine within well-defined boundaries — not an unpredictable autonomous actor.

## Sources

- [Orchestrating AI Agents — HatchWorks](https://hatchworks.com/blog/ai-agents/orchestrating-ai-agents/)
