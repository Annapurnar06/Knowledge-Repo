---
title: Missions & Droids in CI/CD
description: How Factory's agents integrate with CI/CD pipelines for automated code review, testing, documentation, and deployment workflows.
---

Factory's headless execution mode (Droid Exec) and GitHub App integration enable agents to run as part of automated CI/CD pipelines, bringing autonomous capabilities to continuous integration and delivery workflows.

## Droid Exec in Pipelines

Droid Exec runs non-interactive agent commands suitable for automation:

```bash
droid exec --auto high "review the changes and fix any lint errors"
```

### GitHub Actions Integration

Factory provides ready-to-use GitHub Actions workflows for:
- **Automated code review** on pull requests
- **Automated documentation** updates when code changes
- **Lint fix automation** across codebases
- **Import organization** across files
- **Error message improvement** across entire codebases

### Pipeline Patterns

| Pattern | Description |
|---------|-------------|
| **PR Review** | Factory GitHub App provides context-aware code reviews on every PR |
| **Doc Sync** | Documentation automatically updated when code changes |
| **Lint Fix** | ESLint violations fixed automatically via Droid Exec |
| **Migration** | Large-scale refactoring executed as Missions with CI integration |

## GitHub App

The Factory GitHub App enables:
- Automated pull request reviews with context-aware analysis
- Integration with existing GitHub workflows
- Runs alongside human reviewers, not replacing them

## CI/CD Identity & Security

- Use **separate identities and credentials** for CI compared to developers
- Same hierarchical policies apply: models, tools, MCP servers constrained by org/project policy
- Droid Shield scans for secrets in all automated commits
- All actions logged for audit trail

## Scaling Across Teams

Org-level droids and commands enable standardized CI/CD workflows:
- Org admins publish security-reviewed automation workflows
- Teams extend with project-specific logic
- Shared skills and plugins checked into repos are available to all team members
- Enterprise plugin registry for centralized distribution of approved extensions

## Founder Takeaway

CI/CD integration is where autonomous agents move from "developer tool" to "organizational infrastructure." The pattern of headless execution + GitHub App integration + hierarchical policy control enables teams to deploy agent-powered automation that scales across repositories and teams while maintaining security boundaries. For vertical AI systems, this demonstrates how to embed autonomous agents into existing operational workflows rather than requiring process changes.

## Sources
- [Droid Exec Documentation](https://docs.factory.ai/cli/droid-exec/overview)
- [GitHub Actions Guide](https://docs.factory.ai/guides/droid-exec/github-actions)
- [Automated Code Review](https://docs.factory.ai/guides/droid-exec/code-review)
