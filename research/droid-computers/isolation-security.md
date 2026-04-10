---
title: "Isolation & Security"
description: Factory AI's defense-in-depth security model — risk-classified commands, Droid Shield secret scanning, sandboxed execution, and enterprise compliance certifications.
---

Autonomous agents executing code on real infrastructure demand a security model that goes beyond access control. Factory implements defense in depth: multiple independent layers that each reduce the blast radius of a failure in any other layer.

## Command Risk Classification

Every command a Droid executes is classified by risk level. Low-risk operations (reading files, listing directories) proceed automatically. Higher-risk operations (modifying system files, network access, destructive commands) require explicit approval before execution. This classification prevents a misguided Droid from causing damage without blocking routine productive work.

## Droid Shield

Droid Shield provides automatic secret detection, preventing accidental credential exposure in git commits and pushes. Before any code leaves the machine, Shield scans for:

- API keys and tokens
- Database connection strings
- Private keys and certificates
- Other credential patterns

This catches the class of security incidents where an agent inadvertently commits secrets to a repository — a risk that increases when code generation is automated.

### Droid Shield Plus (Enterprise)

Shield Plus adds AI-powered scanning from Palo Alto Networks Prisma AIRS. Beyond pattern-matching for secrets, it detects:

- **Prompt injection attempts** — adversarial inputs designed to manipulate agent behavior
- **Sensitive data exposure** — PII, financial data, or proprietary information in code or logs
- **Advanced secret patterns** — credentials that don't match known formats but exhibit secret-like entropy

## Hooks

Factory supports custom hooks at key execution points, enabling teams to inject their own security logic into the Droid workflow. This allows integration with existing security tooling — static analysis, policy engines, compliance scanners — without modifying Factory's core behavior.

## Execution Sandboxing

Droid Computers run in sandboxed execution environments. Each computer is isolated from other computers and from Factory's control plane. Data is encrypted in transit and at rest. The same security model applies whether the Droid is accessed via CLI, desktop app, or web interface — there is no "less secure" access path.

## Observability and Audit

All Droid actions are logged with OpenTelemetry telemetry, producing a complete audit trail of what each agent did, when, and why. This serves both operational debugging and compliance requirements — auditors can trace any change back to the specific Droid action that produced it.

## Enterprise Controls

For organizations with strict governance requirements, Factory provides:

- **Org-level model policies** — control which LLM providers and models Droids can use
- **Tool restrictions** — limit which tools and integrations are available to Droids
- **SSO/SCIM** — centralized identity management and automated user provisioning
- **RBAC** — role-based access control for Droid and computer management
- **Audit logging** — comprehensive records for compliance and forensics

## Compliance Certifications

Factory holds **SOC 2 Type II**, **ISO 27001**, and **ISO 42001** certifications. ISO 42001 is particularly notable — it covers AI management systems specifically, addressing the governance challenges unique to deploying autonomous agents in enterprise environments.

## Founder Takeaway

The security model is designed for defense in depth. Risk classification gates dangerous operations. Secret scanning prevents credential leaks. Execution sandboxing limits blast radius. Audit logging ensures accountability. These form concentric layers of protection — a failure in any single layer is caught by the others. The addition of Prisma AIRS-powered scanning for prompt injection addresses a threat category unique to AI agents, showing awareness that the attack surface for autonomous systems differs from traditional software.

## Sources
- [Factory Desktop — Factory AI](https://factory.ai/news/factory-desktop)
- [Security — Factory Docs](https://docs.factory.ai/cli/account/security)
- [LLM Safety & Agent Controls — Factory Docs](https://docs.factory.ai/enterprise/llm-safety-and-agent-controls)
