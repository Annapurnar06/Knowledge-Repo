---
title: Enterprise Features
description: SSO, compliance, analytics, ROI tracking, and organizational controls for enterprise adoption of Factory AI.
---

Factory's enterprise layer adds organizational governance, identity management, compliance controls, and usage analytics on top of the core agent capabilities.

## Identity & Access

- **SSO/SAML** authentication with enterprise identity providers
- **Directory Sync/SCIM** provisioning for automated user lifecycle management
- **Domain Management** for controlling organizational access
- **RBAC** (Role-Based Access Control) with org, project, and user-level permissions

## Hierarchical Settings

Settings cascade through three levels, with extension-only semantics:

| Level | Controls |
|-------|----------|
| **Org** | Authoritative list of allowed models, tool policies, MCP server allowlists, BYOK restrictions |
| **Project** | Additional models, project defaults, specialized droids/commands |
| **User** | Personal defaults within the allowed set, personal droids/commands |

Org-level bans cannot be overridden at any lower level. Upgrades and policy changes flow consistently across all environments.

## Compliance & Audit

- **SOC 2 Type II**, **ISO 27001**, and **ISO 42001** certifications
- Every action logged with full audit trail
- OpenTelemetry telemetry export to your OTLP-compatible collector
- Enterprise control change history with paginated revision tracking
- Data classification, retention policies, encryption in transit and at rest

## Usage & Analytics

- **Token consumption** tracking with usage-based pricing and transparent billing
- **Per-user credits limits** with global defaults and individual overrides
- **Productivity metrics**: PRs created/merged, code churn, delivery timeline compression
- **Tool usage analytics** for understanding adoption patterns
- **ROI tracking**: Most compelling metric — reducing 4-month migration to 3.5 days

## Deployment Options

| Mode | Description |
|------|-------------|
| **Cloud-managed** | Factory manages infrastructure |
| **Hybrid** | LLM traffic terminates inside your network (Azure OpenAI, Bedrock, Vertex, self-hosted models) |
| **Fully airgapped** | No data leaves your network; deployed at major financial, healthcare, and government institutions |

## LLM Safety Controls

- Commands classified by risk level
- Droid Shield scans for secrets before anything reaches a model
- Droid Shield Plus (Palo Alto Prisma AIRS) for prompt injection and sensitive data protection
- Hooks for custom security integration at execution boundaries
- Allow/deny policies for tools and models

## Founder Takeaway

Enterprise adoption of autonomous AI requires **trust infrastructure**: identity management, hierarchical policy enforcement, comprehensive audit trails, and deployment flexibility. Factory's layered controls — org → project → user — provide a model for how to make agent systems governable at scale. The compliance certifications (SOC 2, ISO 27001/42001) signal maturity that regulated industries require.

## Sources
- [Enterprise with Droids — Factory Docs](https://docs.factory.ai/enterprise/index)
- [Models, LLM Gateways & Integrations](https://docs.factory.ai/enterprise/models-llm-gateways-and-integrations)
- [Compliance, Audit & Monitoring](https://docs.factory.ai/enterprise/compliance-audit-and-monitoring)
