---
title: "Observability, Traces & Replay"
description: Telemetry, structured tracing, replay debugging, and continuous evaluation strategies for autonomous agent systems.
---

If you can't trace, replay, and evaluate agent behavior, you're running experiments in production. Observability is the foundation of trustworthy autonomous systems.

## Telemetry Architecture

Factory uses OpenTelemetry for telemetry export to OTLP-compatible collectors. This means agent telemetry integrates with existing enterprise observability stacks (Datadog, Grafana, Splunk, etc.) without custom instrumentation.

Every agent action generates structured telemetry — not just logs, but correlated traces that capture the full causal chain from user request to final output.

## End-to-End Tracing

Each task gets a correlation ID that propagates across all agent actions: model calls, tool invocations, file operations, API requests, and policy decisions. This enables:

- **Full request reconstruction**: Trace any outcome back through every decision and action that produced it.
- **Cross-agent correlation**: When a supervisor delegates to specialists, the trace follows the full delegation chain.
- **Latency attribution**: Identify exactly where time is spent — model inference, tool execution, approval gates, queue wait time.

## Structured Logging

Logs capture structured fields beyond free-text messages:

- **Model version**: Which model produced each response.
- **Prompt version**: Which system prompt variant was active.
- **Tool calls**: Full input/output for every tool invocation.
- **Policy decisions**: Which policies were evaluated and their outcomes (allow, deny, escalate).
- **Token counts**: Input and output tokens per model call for cost attribution.

## Key Metrics

The metrics that matter for agent operations:

- **Success rate**: Percentage of tasks completed without human intervention.
- **Escalation rate**: How often agents escalate to humans — and why.
- **Tool failure rate**: Broken tools are the leading cause of agent failures.
- **Latency**: End-to-end task completion time, broken down by phase.
- **Cost**: Token spend per task, per agent type, per organization.

## Action Logging

Every action is logged — commands executed, tool calls made, model interactions completed, files read and written. This creates a complete audit trail that satisfies both debugging needs and compliance requirements.

The logging is append-only and tamper-evident, ensuring that post-hoc analysis reflects what actually happened, not a reconstructed narrative.

## Replay Capability

Stored traces allow replaying exact inputs and tool outputs for debugging. When an agent produces an unexpected result, you can:

1. Pull the full trace for that task.
2. Replay the exact sequence of model inputs and tool outputs.
3. Identify the specific decision point where behavior diverged from expectations.
4. Test fixes against the same inputs before deploying.

Replay eliminates the "works on my machine" problem for agent debugging — you're testing against the exact conditions that produced the failure.

## Continuous Evaluation

Agent quality requires ongoing measurement, not just pre-deployment testing:

- **Golden datasets**: Curated input-output pairs that define expected behavior. Run on every change.
- **Regression runs**: Automated evaluation on prompt, tool, or model changes before deployment.
- **Shadow mode**: New agent versions process real traffic in parallel with production, with outputs compared but not served.
- **Canary releases**: Gradual rollout of agent changes to a subset of traffic with automatic rollback on metric degradation.

## Version Everything

Reproducibility requires versioning all components that affect agent behavior:

- Prompts (system prompts, templates, few-shot examples)
- Tools (schemas, implementations, configurations)
- Policies (approval rules, budget limits, scope constraints)
- Retrieval configs (embedding models, chunk sizes, ranking parameters)
- Model routing rules (which models serve which task types)

Without versioning, you can't attribute behavior changes to specific component changes.

## Enterprise Telemetry

For organizations running agents at scale:

- **Usage-based billing**: Transparent token tracking tied to teams, projects, and task types.
- **Code churn metrics**: Measuring the stability and quality of agent-generated code over time.
- **PR metrics**: Cycle time, review rounds, merge rate for agent-created pull requests.

## The Semantic Observability Challenge

Traditional observability answers "what happened." Agent observability must also answer "was the user satisfied?" This is the semantic observability problem — understanding user intent and satisfaction beyond simple thumbs up/down signals.

Solving this requires correlating task outcomes with downstream signals: Was the PR merged? Did the fix resolve the incident? Did the documentation answer the question? These lagging indicators close the feedback loop that simple telemetry cannot.

## Founder Takeaway

Observability is the foundation of trustworthy autonomous systems. If you can't trace, replay, and evaluate agent behavior, you're running experiments in production. Invest in structured tracing, replay infrastructure, and continuous evaluation before scaling agent autonomy.

## Sources

- [Orchestrating AI Agents — HatchWorks](https://hatchworks.com/blog/ai-agents/orchestrating-ai-agents/)
- [Telemetry Export — Factory Docs](https://docs.factory.ai/enterprise/telemetry-export)
- [Enterprise Autonomous Software Engineering with AI Droids — ZenML LLMOps Database](https://www.zenml.io/llmops-database/enterprise-autonomous-software-engineering-with-ai-droids)
