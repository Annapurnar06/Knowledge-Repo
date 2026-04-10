---
title: Core Droid Types & Capabilities
description: Factory AI's specialized autonomous agents — Code, Knowledge, Reliability, Product, and Tutorial Droids — and what each is built to do.
---

Factory's Droids are specialized AI agents designed for distinct parts of the software development lifecycle. Unlike general-purpose coding assistants, each Droid carries optimized system prompts, specialized tools, and an appropriate model for its domain.

## The Five Core Droid Types

### Code Droid
The primary engineering agent. Handles feature development, refactoring, bug fixes, and implementation work. This is the Droid developers interact with most for actual coding tasks. It can build entire production-ready features from a ticket or specification, executing multi-file changes autonomously.

### Knowledge Droid
Research and documentation specialist. Operates as a deep research-style system that searches codebases, documentation, and the internet to answer complex questions. Produces documentation, writes specs, and helps teams understand legacy systems. Particularly valuable for onboarding engineers to unfamiliar codebases.

### Reliability Droid
The on-call specialist. Triages production alerts, performs root cause analysis (RCA), troubleshoots incidents, compiles evidence, and documents the resolution. Focused on SRE-style incident response. Integrates with observability tools like Sentry, Datadog, and PagerDuty.

### Product Droid
Ticket and PM work automation. Manages backlogs, prioritizes tickets, handles assignment, and transforms Slack threads into coherent product specs. Extends beyond developers to serve product managers and team leads.

### Tutorial Droid
Onboarding assistant for Factory itself. Helps new users learn the platform, understand features, and get productive quickly.

## Key Design Principles

**Delegation over collaboration.** Droids are built for autonomous task completion — you assign work and come back to results, rather than pair-programming interactively.

**Context-rich execution.** Each Droid has access not just to code, but to enterprise tools (Slack, Jira, Notion, Sentry, etc.) providing the same context a human engineer would need.

**Validated outputs.** Code is one of the few domains where outputs can be rigorously validated via execution and tests, making autonomous agents particularly viable here.

## Founder Takeaway

The Droid architecture demonstrates a key pattern for vertical AI: **specialization beats generalization**. Rather than one agent trying to do everything, Factory decomposes the SDLC into domains and assigns purpose-built agents to each. This is the Supervisor + Specialists pattern described in production orchestration literature — it reduces hallucinations, enables stricter tool access per agent, and supports clearer ownership of agent behavior.

## Sources
- [Factory AI Guide — Sid Bharath](https://sidbharath.com/blog/factory-ai-guide/)
- [Factory Droids — Developer Tech](https://developer-tech.com/news/factory-droids-ai-agents-tackle-entire-development-lifecycle/)
- [ZenML — Enterprise Autonomous Software Engineering](https://www.zenml.io/llmops-database/enterprise-autonomous-software-engineering-with-ai-droids)
