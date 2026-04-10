---
title: Enterprise APIs & Gateways
description: How Factory incorporates external APIs, LLM gateways, and enterprise tooling.
---

Factory's enterprise layer provides programmatic control over every aspect of the platform — from LLM routing and model governance to session management, analytics, and compute orchestration. This is the layer that transforms Factory from a developer tool into enterprise infrastructure.

## LLM Gateway Patterns

### Direct Configuration
LLM gateways are configured via `settings.json` or environment variables:

```bash
ANTHROPIC_BASE_URL=https://gateway.corp.example.com/anthropic
OPENAI_BASE_URL=https://gateway.corp.example.com/openai
```

This routes all model traffic through corporate infrastructure for logging, rate limiting, cost attribution, and compliance monitoring.

### Org-Level Gateway Policy
Administrators can enforce a **mandatory gateway policy** requiring all LLM traffic — regardless of provider or user configuration — to pass through specified gateway endpoints. This is the compliance hard boundary: no direct-to-provider calls, no shadow AI.

## BYOK Control

Organizations control how users bring their own API keys:

| Control | Description |
|---------|-------------|
| **Allow/block user-supplied keys** | Toggle whether users can use personal API keys |
| **Restrict providers** | Limit which providers users can configure |
| **Restrict endpoints** | Enforce that BYOK traffic routes through approved gateways |

This gives security teams confidence that all LLM interactions — even user-initiated ones — are governed and auditable.

## MCP Server Governance

**Model Context Protocol** servers are governed hierarchically:

- **Org allowlist/blocklist** — admins control which MCP servers are permitted across the organization
- **Project-level config** — teams add project-specific MCP servers within org policy
- **User-level opt-in** — individuals can enable personal MCP servers, subject to org and project constraints

This prevents unvetted tool access while allowing teams to extend agent capabilities through approved channels.

## Hierarchical Droids & Commands

Factory's configuration hierarchy enables layered control over agent behavior:

### Organization Level
Admins publish **blessed workflows** — Droids, Skills, and commands that encode security-reviewed, compliance-approved patterns. These are the organization's standard operating procedures, expressed as agent configuration.

### Project Level
Teams add **specialized logic** — project-specific Droids, commands, and hooks that build on org-level foundations. A team might add a custom Droid for their specific migration pattern or a hook that enforces their service's testing requirements.

### User Level
Individuals add **personal customizations** — workflow shortcuts, preferred models, and personal automation. These must still respect org and project policies — a user can't re-enable a model the org has banned or bypass a mandatory hook.

## Sessions API

Programmatic session management for building Factory into larger automation systems:

- **Create sessions** — spin up agent sessions programmatically with specific configurations
- **Manage sessions** — list, resume, and terminate sessions via API
- **Add messages** — inject context or instructions into running sessions
- **Retrieve results** — pull agent outputs, diffs, and artifacts programmatically

This enables patterns like: "When a Jira ticket moves to In Progress, create a Factory session, assign the ticket context, and let the agent start working."

## Analytics API

Organization-level visibility into agent utilization and impact:

| Metric Category | Examples |
|----------------|----------|
| **Usage metrics** | Sessions created, duration, completion rate |
| **Token consumption** | Per-user, per-project, per-model token usage |
| **Tool usage** | Which tools agents use most, failure rates |
| **Productivity analytics** | PRs created, code volume, task completion time |

This data feeds into ROI calculations and helps organizations identify where agents deliver the most value — and where they need improvement.

## Computers API

Manage the execution environments where agents operate:

- **Create computers** — provision isolated execution environments with specific configurations
- **Manage lifecycle** — start, stop, snapshot, and destroy compute instances
- **Monitor execution** — track resource usage, running processes, and agent activity
- **Configure resources** — set CPU, memory, and storage limits per environment

The Computers API enables fine-grained control over the security and resource boundaries of agent execution — critical for enterprises that need to ensure agents operate within approved infrastructure.

## Founder Takeaway

The enterprise API layer is what separates a developer tool from enterprise infrastructure. For vertical AI builders, the lesson is that programmatic control, hierarchical governance, and auditability aren't features — they're prerequisites for enterprise adoption. Build your API surface early, design for hierarchical policy enforcement, and assume that every action your agent takes will need to be logged, attributed, and auditable.

## Sources
- [Factory Docs — Models, LLM Gateways, and Integrations](https://docs.factory.ai/enterprise/models-llm-gateways-and-integrations)
- [Factory Documentation — llms.txt](https://docs.factory.ai/llms.txt)
