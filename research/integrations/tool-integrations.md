---
title: Tool & Platform Integrations
description: Factory's integrations with GitHub, Jira, Slack, Sentry, Linear, PagerDuty, and other developer tools.
---

Autonomous agents are only as useful as the context and actions available to them. Factory's integration layer connects agents to the full surface area of modern software development — source control, project management, communication, observability, documentation, and CI/CD.

## Source Control

### GitHub
Deep integration as the primary source control platform:
- Repository access for code context and file operations
- Pull request creation, review, and management
- **GitHub App** for automated PR reviews — Droids review incoming PRs against configurable criteria
- **Droid Exec in GitHub Actions** — agents run as CI/CD pipeline steps
- Issue tracking and project board integration

### GitLab
Full support for cloud and self-hosted GitLab instances:
- Repository access and merge request management
- **Droid Exec in GitLab CI** — headless agent execution in pipelines
- Self-hosted deployments for air-gapped enterprise environments

## Project Management

### Jira
- Ticket creation, updates, and status management
- Priority and assignment handling
- Sprint and backlog operations
- Bi-directional sync between agent actions and ticket state

### Linear
- Issue management with priority and assignment
- Project and cycle tracking
- Streamlined integration for engineering-centric teams
- Automatic status updates as agents complete work

## Communication

### Slack
- **@factory mention** — invoke Factory directly in Slack channels for questions and commands
- Thread context extraction — agents can read and synthesize Slack discussions
- Notification delivery — agents report completion, failures, and review requests to channels
- Spec generation from Slack threads — transform discussions into structured requirements

## Observability

### Sentry
- Error trace ingestion for incident triage
- Stack trace analysis and root cause identification
- Integration with the Reliability Droid for automated incident response

### Datadog
- Performance metrics and monitoring data
- Alert context for reliability workflows
- Infrastructure and application health signals

## Documentation

### Notion
- Page reading and content extraction for spec and context gathering
- Documentation updates driven by code changes

### Google Docs
- Document access for requirements, specs, and planning materials
- Content extraction for agent context

## MCP (Model Context Protocol)

Factory supports **MCP** for extensible tool access — a standardized protocol for connecting agents to external tools and data sources:

- **MCP server management** with organizational allowlist/blocklist controls
- Org admins control which MCP servers are permitted
- Project-level MCP configuration for team-specific tools
- User-level opt-in for personal MCP servers (within org policy)

MCP enables teams to extend Factory's capabilities to internal tools, databases, and APIs without building custom integrations from scratch.

## IDE Integrations

| IDE | Integration Type |
|-----|-----------------|
| **VS Code** | Extension with inline agent, chat panel, and deep editor integration |
| **JetBrains** | Plugin for IntelliJ-based IDEs (IntelliJ, WebStorm, PyCharm, etc.) |
| **Zed** | Extension for the Zed editor |

## Founder Takeaway

The integration layer is where vertical AI systems win or lose in the enterprise. Raw coding ability is table stakes — what differentiates Factory is that its agents operate with the same information surface as a human engineer: code, tickets, conversations, error traces, and documentation. For vertical AI builders, prioritize integrations ruthlessly. Every tool your users already rely on that you don't connect to is a gap in your agent's context — and context gaps produce bad outputs.

## Sources
- [Factory AI Guide — Sid Bharath](https://sidbharath.com/blog/factory-ai-guide/)
- [Factory Droids — Developer Tech](https://developer-tech.com/news/factory-droids-ai-agents-tackle-entire-development-lifecycle/)
- [Factory Docs — Models, LLM Gateways, and Integrations](https://docs.factory.ai/enterprise/models-llm-gateways-and-integrations)
