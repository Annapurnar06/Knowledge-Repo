---
title: Benchmarks & Evaluations
description: How Factory and the industry measure agent capabilities.
---

Benchmarking autonomous coding agents is harder than benchmarking models. The output isn't a classification or a score — it's working software, and "working" has many dimensions. Factory's approach to evaluation reveals the gap between academic benchmarks and production reality.

## SWE-Bench: Rise and Limitations

Factory initially gained recognition on **SWE-Bench**, the most prominent benchmark for autonomous software engineering. However, Factory stopped running it. The reasons are instructive:

- **Python-only**: SWE-Bench tasks are exclusively Python, which doesn't reflect real enterprise codebases (TypeScript, Java, Go, Rust, mixed stacks)
- **Narrow task scope**: Focused on bug fixes in open-source repos — not feature development, refactoring, migration, or multi-service work
- **Doesn't match enterprise reality**: Real engineering tasks involve cross-repo dependencies, CI/CD pipelines, internal APIs, and organizational context that SWE-Bench can't capture

The broader lesson: **public benchmarks measure what's easy to measure, not what matters.**

## Factory's Internal Evaluation Strategy

Factory uses a multi-layered approach to evaluation:

### Task-Based Evals
Real-world engineering tasks drawn from actual customer usage patterns. These include feature implementation, bug triage, code migration, and incident response — measured against human-validated expected outputs.

### Behavioral Specification with Rubrics
Rather than binary pass/fail, Factory evaluates agent behavior against rubrics: Did it explore the codebase before editing? Did it run tests? Did it handle edge cases? This captures quality of process, not just quality of output.

### Prompt Optimization
Systematic testing of prompt variations, tool configurations, and orchestration strategies to find optimal setups per task type. This is where dealing with model-specific biases becomes critical.

## Model Biases and Adaptation

A concrete example of evaluation-driven engineering: **Sonnet 3.7 developed a preference for CLI tools** due to its training on Claude Code interactions. This RL-induced bias meant the model would reach for shell commands even when Factory's native tools (Read, Edit, Grep) were faster and more reliable. Factory chose to work around these biases through prompt engineering and tool design rather than fine-tuning — preserving the ability to swap models without retraining.

Factory has deliberately **chosen not to fine-tune models**, maintaining vendor-agnostic flexibility. This is a strategic bet that orchestration and tooling matter more than model-specific optimization.

## Terminal-Bench and Public Metrics

Factory maintains leadership on **Terminal-Bench**, a benchmark focused on terminal-based agent capabilities — better aligned with Factory's CLI-first architecture.

## Production Metrics That Matter

The metrics Factory and its customers actually track:

| Metric | What It Measures |
|--------|-----------------|
| **Token usage** | Cost efficiency per task |
| **PRs created/merged** | Output volume and acceptance rate |
| **Code churn** | Quality signal — high churn means agents are rewriting their own work |
| **Delivery timeline compression** | The business metric — how much faster does work get done? |

The most compelling data point: **a 4-month migration reduced to 3.5 days.** This isn't a benchmark score — it's a 34x compression of real engineering work on a real codebase.

## Founder Takeaway

For vertical AI builders, the evaluation lesson is stark: don't optimize for public benchmarks. Build task-based evals that mirror your actual customer workflows, measure behavioral quality (not just output correctness), and track the business metrics your customers care about. The company that wins is not the one with the highest SWE-Bench score — it's the one whose agents reliably deliver value on production tasks.

## Sources
- [ZenML — Enterprise Autonomous Software Engineering with AI Droids](https://www.zenml.io/llmops-database/enterprise-autonomous-software-engineering-with-ai-droids)
- [Factory AI](https://factory.ai)
