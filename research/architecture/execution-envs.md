---
title: "Execution Environments & Sandboxes"
description: Isolation models, risk classification, sandboxed execution, and tool contracts for safe autonomous agent operations.
---

Safe autonomy requires layered execution controls. Agents must operate with enough freedom to be useful and enough constraint to be trustworthy.

## Risk Classification

Droid commands run locally by default. Each command is classified by risk level before execution:

- **Low risk**: Read-only operations — listing files, reading content, checking status. Execute immediately without approval.
- **Medium risk**: Operations with reversible side effects — creating files, installing packages, making commits. May execute with notification.
- **High risk**: Operations with potentially irreversible consequences — deleting files, pushing to remote, modifying system configuration. Require explicit approval.

Risk classification is the first gate in preventing unintended side effects. The classification considers the command itself, the target (production vs development), and the organizational policy.

## Execution Modes

### Local Execution
Pair-programming mode with direct access to the local development environment. The Droid operates on the developer's machine, sees the same files, and uses the same tools. Best for interactive development where the developer wants visibility and control.

### Remote Execution
Hand off tasks to remote Droids for independent completion. The developer assigns work and returns to results. The Droid operates asynchronously, reporting back through pull requests and status updates.

### Cloud Containers
Isolated execution environments for Missions (multi-step autonomous workflows). Each container gets a clean environment with the necessary dependencies, isolated from both the developer's machine and other running tasks.

## Git as Source of Truth

All coordination happens through version control. Git is the single source of truth for code state, and all agent operations ultimately produce git artifacts (commits, branches, pull requests). This means:

- **Auditability**: Every change has a commit with attribution and context.
- **Rollback**: Any agent-produced change can be reverted through standard git operations.
- **Collaboration**: Multiple agents and humans coordinate through the same branching and review workflows.

## Git Worktree Pattern

Each agent operates in an isolated git worktree to prevent conflicts. When multiple Droids work on the same repository simultaneously, each gets its own worktree — a separate working directory backed by the same repository. This prevents:

- File conflicts between concurrent agents.
- Partial state visibility (one agent seeing another's uncommitted changes).
- Lock contention on the working directory.

## Tool Contracts

Every tool an agent can invoke has a strict contract:

- **Input/output schemas**: Typed, validated parameters and return values. No free-form string passing.
- **Idempotency controls**: Tools declare whether they're idempotent. Non-idempotent tools get additional safeguards against duplicate execution.
- **Authorization checks**: Tool invocation is gated by the agent's permission scope. A code review Droid cannot deploy to production.
- **Rate limits**: Per-tool, per-agent, and per-organization rate limits prevent runaway execution.

Tool contracts convert the unbounded action space of "anything the agent could do" into a well-defined, testable, auditable set of capabilities.

## BYOM (Bring Your Own Machine)

Organizations can register any machine as an execution environment. This enables running Droids on specialized hardware, in specific network zones, or on machines with access to proprietary tools and data that cannot leave the organization's infrastructure.

## Air-Gapped Deployments

For regulated industries (finance, healthcare, defense), Factory supports fully isolated network execution. Air-gapped deployments ensure:

- No data leaves the organization's network boundary.
- Model inference runs on-premises or in the organization's cloud account.
- All telemetry stays internal.
- Compliance with data residency and sovereignty requirements.

## Founder Takeaway

Safe autonomy requires layered execution controls — risk classification, approval gates, sandboxed environments, and tool contracts form the boundary between agent judgment and real-world side effects. The goal is not to prevent agents from acting, but to ensure every action passes through appropriate validation for its risk level.

## Sources

- [Droid Computers — Factory Docs](https://docs.factory.ai/cli/features/droid-computers)
- [LLM Safety and Agent Controls — Factory Docs](https://docs.factory.ai/enterprise/llm-safety-and-agent-controls)
- [Orchestrating AI Agents — HatchWorks](https://hatchworks.com/blog/ai-agents/orchestrating-ai-agents/)
