---
title: "Persistent Storage, SSH & Checkpointing"
description: How Droid Computers maintain state across sessions — persistent storage, SSH access, checkpoint/restore, and programmatic management via API.
---

Droids don't start from zero. When a Droid Computer wakes up, it picks up where it left off: installed packages, cloned repos, credentials, running services — all intact. This persistence is what separates a practical autonomous agent from a demo.

## Persistent Storage

Droid Computers maintain a persistent filesystem across sessions. Everything a Droid installs, configures, or creates survives sleep/wake cycles. This means:

- Package installations don't repeat every session
- Repository clones and their git history persist
- Configuration files and credentials remain in place
- Build artifacts and caches carry forward

The cumulative effect is significant. A Droid working on a large codebase doesn't spend the first minutes of every session re-cloning and re-installing — it starts productive immediately.

## SSH Access

Every Droid Computer exposes SSH access for direct human inspection and debugging. When you need to understand what a Droid did, verify its environment, or troubleshoot a stuck task, you can drop into the machine directly. This bridges the gap between autonomous execution and human oversight — the Droid works independently, but the operator can always look under the hood.

## Checkpoint and Restore

Save the state of a Droid Computer at any point and restore it later. This enables:

- **Safe experimentation** — checkpoint before a risky operation, restore if it goes wrong
- **Reproducible environments** — snapshot a known-good state and share it across team members
- **Rollback** — revert to a previous state without rebuilding from scratch

Checkpointing captures the full machine state, not just the filesystem — making restores genuinely faithful to the original.

## Programmatic Management via API

The Droid Computers API exposes the full lifecycle programmatically:

- **Create and delete** computers on demand
- **Update** configuration and resource allocation
- **Restart** to clear transient issues
- **Refresh credentials** — re-configures git credentials for SCM integrations, rotating access tokens without rebuilding the machine
- **Get metrics** — CPU, memory, and disk usage monitoring for capacity planning and cost management

This API makes it possible to integrate Droid Computer management into existing infrastructure automation, CI/CD pipelines, or custom orchestration systems.

## Founder Takeaway

Persistence is what makes autonomous agents practical for real work. Without it, every session starts from scratch — re-cloning repos, re-installing dependencies, re-configuring credentials. That overhead destroys the economics of automation. Droid Computers solve this by treating the agent's environment as a first-class, long-lived resource rather than a disposable container. The checkpoint/restore capability adds another dimension: the ability to treat machine state as versioned infrastructure.

## Sources
- [Factory Desktop — Factory AI](https://factory.ai/news/factory-desktop)
- [Factory LLMs.txt — Factory Docs](https://docs.factory.ai/llms.txt)
