---
title: "Cloud Computers, BYO Machines & Local Models"
description: Factory AI's Droid Computers offer managed cloud instances, bring-your-own hardware registration, and fully local model execution for air-gapped deployments.
---

Droid Computers are persistent, long-lived compute environments purpose-built for Droids. Unlike ephemeral CI runners or short-lived containers, these machines maintain state across sessions — giving Droids a stable home where installed packages, cloned repos, and running services survive between tasks.

## Cloud Computers

Factory's managed Cloud Computers are provisioned directly from the Factory app. The lifecycle is fully managed: create, wake, sleep, checkpoint, and restore — all from the desktop app or API. Key characteristics:

- **Persistent storage** that survives sleep/wake cycles
- **SSH access** for direct inspection and debugging
- **Instant resume** — Droids pick up exactly where they left off, no re-provisioning delay
- **Checkpoint/restore** — snapshot machine state at any point and roll back if needed

Cloud Computers are the fastest path to getting Droids running. No infrastructure to manage, no provisioning scripts to maintain.

## BYO Machine

For teams that need Droids running on their own hardware, `droid computer register` turns any machine into a Droid Computer. This covers:

- **Developer workstations** — run Droids alongside your local development environment
- **On-prem servers** — keep compute within your physical infrastructure boundary
- **GPU rigs** — dedicate specialized hardware for model inference or compute-heavy tasks

Registration is a single CLI command. Once registered, the machine appears in the desktop app alongside Cloud Computers with the same management interface.

## Local Model Support

Register a machine with a GPU and Droids can run entirely on local models. The BYOK (Bring Your Own Key) system connects to Ollama, vLLM, or any OpenAI-compatible endpoint running on your infrastructure. The critical property: **no data leaves your network**.

This enables deployments in regulated industries where data residency is non-negotiable:

- **Financial services** — trading systems, risk models, proprietary strategies stay on-premises
- **Healthcare** — patient data and clinical systems remain within compliance boundaries
- **Government** — classified or sensitive workloads in air-gapped environments

## Unified Management

The Factory Desktop app provides a single pane of glass across all computer types. Status, logs, and configuration for Cloud Computers and BYO Machines live in one interface — no context-switching between dashboards.

## Founder Takeaway

The Cloud/BYO/Local trifecta covers the full spectrum of enterprise deployment needs. Cloud Computers optimize for fast iteration and zero-ops convenience. BYO Machines address data sovereignty and infrastructure control requirements. Local model support goes further — enabling fully air-gapped deployments where no data crosses the network boundary. This graduated model means Factory can land in security-conscious enterprises without requiring customers to compromise on their deployment constraints.

## Sources
- [Factory Desktop — Factory AI](https://factory.ai/news/factory-desktop)
- [Droid Computers — Factory Docs](https://docs.factory.ai/cli/features/droid-computers)
