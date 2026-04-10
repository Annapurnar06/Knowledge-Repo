---
title: Agent Patterns for Engineering & Product
description: How Factory's agent patterns serve engineering, product, reliability, and organizational verticals.
---

Factory's agent architecture extends well beyond code generation. The platform provides patterns for reliability engineering, product management, knowledge work, and organizational automation — reflecting the reality that software delivery involves far more than writing code.

## Beyond Code: Specialized Agent Patterns

### Reliability Droid
On-call incident response automation. Integrates with **Sentry** and **PagerDuty** to:
- Triage production alerts automatically
- Perform root cause analysis across logs, traces, and code
- Compile evidence and generate incident reports
- Propose and implement fixes with appropriate review gates

This transforms incident response from a purely reactive human workflow into a partially automated pipeline where the agent handles the investigative grunt work.

### Product Droid
Ticket and project management automation. Connects to **Jira** and **Linear** to:
- Manage backlogs and prioritize tickets
- Handle assignment and status updates
- Transform Slack threads into coherent product specs
- Bridge the gap between product requirements and engineering tasks

### Knowledge Droid
Research and documentation specialist:
- Deep codebase exploration and analysis
- Documentation generation and maintenance
- Cross-referencing code with external docs, specs, and tickets
- Onboarding knowledge compilation for new team members

## Headless Execution: Droid Exec

**Droid Exec** runs agents in headless mode for CI/CD pipelines and automation:

```bash
droid exec --auto high "run tests, commit, push"
```

This enables agents to operate as pipeline steps — triggered by events (PR opened, test failed, deploy completed) rather than human interaction. Key use cases:
- Automated test execution and fix loops
- Post-deploy verification
- Scheduled maintenance tasks
- CI-triggered code review

## Custom Slash Commands

Teams define custom slash commands for routine automation — reusable shortcuts that encode multi-step workflows into a single invocation. These live in project configuration and are shared across the team.

## Skills Framework

Factory's **Skills** are reusable capability modules that agents can invoke:

| Skill | Purpose |
|-------|---------|
| **Data Analyst** | Statistical analysis, data exploration, visualization |
| **Browser Automation** | Web scraping, testing, interaction with web UIs |
| **Data Querying** | Database access and query construction |
| **Frontend UI** | Component building, styling, layout |
| **Internal Tools** | Admin panels, dashboards, internal utilities |
| **Product Management** | Spec writing, ticket management, prioritization |
| **Service Integration** | API connections, webhook setup, third-party services |
| **Vibe Coding** | Rapid prototyping with minimal specification |

Skills allow agents to acquire domain-specific capabilities on demand without bloating the base prompt.

## Hooks

**Hooks** are event-driven automations that trigger on specific actions:

- **Code validation** — run linters, type checks, or custom validators after edits
- **Documentation sync** — update docs when code changes
- **Git workflows** — auto-format commits, enforce branch naming, update changelogs
- **Testing automation** — run relevant tests after code modifications
- **Notifications** — alert channels on completion, failure, or review-needed events

## Plugins

**Plugins** are shareable packages that bundle Droids, Skills, Hooks, and configuration into distributable units. This enables teams to publish and consume standardized agent workflows across the organization.

## Extending to Non-Engineers

The Desktop app and accessible interfaces extend Factory's agent patterns to roles beyond engineering:

- **Designers** — generate component implementations from design specs
- **Product Managers** — manage backlogs, write specs, track progress
- **Data Scientists** — explore data, build pipelines, generate visualizations
- **Account Executives** — pull product metrics, generate customer-facing reports

## Founder Takeaway

The agent patterns reveal Factory's real product strategy: it's not a coding tool — it's a workflow automation platform that happens to start with code. For vertical AI builders, this is the template: start with the highest-value, most-automatable task in your domain, then expand outward into adjacent workflows. The Skills and Hooks architecture provides the extensibility mechanism — build the core agents, then let users and teams compose domain-specific capabilities on top.

## Sources
- [Factory AI Guide — Sid Bharath](https://sidbharath.com/blog/factory-ai-guide/)
- [Factory Documentation — llms.txt](https://docs.factory.ai/llms.txt)
