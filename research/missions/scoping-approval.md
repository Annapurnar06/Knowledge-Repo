---
title: Scoping & Approval Flow
description: How Factory Missions moves from user input to autonomous execution — conversational planning, approval gates, runtime controls, and enterprise security mechanisms.
---

Autonomous execution without controls is a non-starter for enterprise adoption. Missions addresses this with a structured flow from scoping through approval to monitored execution, layered with security mechanisms at every stage.

## Initiating a Mission

Missions start with the `/enter-mission` command in any Droid session. This transitions from interactive coding assistance into the mission planning phase — a deliberate context switch that signals the system to engage its planning and decomposition capabilities.

## Conversational Planning

The planning phase is interactive and conversational. The Droid asks clarifying questions, probes constraints, surfaces assumptions, and iterates on the plan with the user. This is not a one-shot prompt — it is a collaborative scoping exercise designed to surface ambiguity before autonomous execution begins.

The user approves the final plan, then the Droid enters Mission Control. This explicit approval gate ensures autonomous work only proceeds with a validated scope.

## User as Project Manager

During execution, the user's role shifts to project manager. You monitor progress across milestones, unblock workers when they encounter issues, and redirect priorities as the mission evolves. This is a fundamentally different interaction model from pair programming — you are supervising autonomous work rather than collaborating on individual lines of code.

## Configuration Continuity

MCP integrations, skills, hooks, and custom Droids all carry over into missions. The environment a team has configured for their Droid workflows persists through the transition to autonomous execution. No reconfiguration required.

## Execution Controls

Missions provides multiple layers of execution control:

- **Execution environment:** Runs locally or in isolated cloud containers
- **Source of truth:** Git serves as the canonical record — all changes are committed and reviewable
- **Risk classification:** Every command is classified by risk level before execution
- **Secret scanning:** Droid Shield scans all actions for credential exposure
- **Custom hooks:** Teams can integrate their own security checks into the execution pipeline
- **Audit logging:** All actions are logged with full traceability
- **Telemetry:** OpenTelemetry integration for observability

## Deployment Models

Missions supports deployment configurations across the security spectrum:

- **Cloud-managed** — Factory-hosted infrastructure
- **Hybrid** — Customer-managed model endpoints via Azure OpenAI, Amazon Bedrock, or Google Vertex AI
- **Fully airgapped** — Complete on-premises deployment with no external connectivity

## Enterprise Controls

For organization-scale adoption, Missions includes:

- **Org-level policy controls** for governing agent behavior across teams
- **SSO and SCIM** for identity management
- **Role-based access control (RBAC)** for granular permissions
- **Comprehensive audit logging** for compliance
- **SOC 2 Type II**, **ISO 27001**, and **ISO 42001** certifications

## Founder Takeaway

The approval and controls layer is not overhead — it is **the enabling mechanism for enterprise adoption**. Autonomous systems that lack safety mechanisms cannot be trusted with production codebases. The pattern here is instructive: conversational scoping reduces errors before execution starts, explicit approval gates create accountability, and layered runtime controls (risk classification, secret scanning, audit logs) make the system auditable. These are the mechanisms that make autonomous systems trustworthy enough for organizations to actually deploy them.

## Sources
- [Introducing Missions — Factory AI](https://factory.ai/news/missions)
