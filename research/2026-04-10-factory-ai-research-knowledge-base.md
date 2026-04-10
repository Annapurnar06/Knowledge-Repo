## Factory AI Research Knowledge Base — Documentation Site

A clean, fast reference site for a founder researching Factory AI's platform to inform decisions about building frontier vertical AI systems.

### Tech Stack
**Astro Starlight** — purpose-built for docs, minimal JS, fast builds, Markdown-native, built-in search and dark mode.

### Site Structure

```
/project/workspace/
├── astro.config.mjs
├── package.json
└── src/
    └── content/docs/
        ├── index.md                    # Overview + executive summary
        ├── droids/
        │   ├── core-types.md           # Core Droid types & capabilities
        │   ├── custom-droids.md        # Custom Droid definitions
        │   └── multi-droid-workflows.md
        ├── missions/
        │   ├── orchestration.md        # Task decomposition & milestones
        │   ├── multi-day-autonomy.md   # Retries, sessions, 40-day pursuit
        │   └── scoping-approval.md
        ├── droid-computers/
        │   ├── cloud-and-local.md      # Cloud, BYO, local model support
        │   ├── persistence.md          # Storage, SSH, checkpointing
        │   └── isolation-security.md
        ├── architecture/
        │   ├── orchestration-design.md # Control/data planes, state
        │   ├── observability.md        # Harnesses, traces, replay
        │   └── execution-envs.md       # Sandboxes, Docker, workspaces
        ├── platform-evolution/
        │   ├── timeline.md             # Key releases timeline
        │   ├── single-to-multi-agent.md
        │   └── benchmarks.md
        ├── features/
        │   ├── memory.md               # Context compression, org memory
        │   ├── ux-patterns.md          # IDE, web, CLI, Slack
        │   └── agent-patterns.md       # Engineering, product, reliability
        ├── integrations/
        │   ├── models-vendors.md       # OpenAI, Anthropic, Google, Ollama
        │   ├── tool-integrations.md    # GitHub, Jira, Slack, Sentry, etc.
        │   └── enterprise-apis.md
        └── developer-experience/
            ├── task-delegation.md      # Specs, auto-run, automation
            ├── enterprise.md           # SSO, compliance, analytics
            └── ci-cd.md               # Missions & Droids in CI/CD
```

### Content Approach
Each page will contain:
1. **Research questions** as section headings
2. **Synthesized answers** sourced from the provided URLs (fetched and distilled)
3. **Source links** preserved as references for deeper reading
4. **Founder-relevant takeaways** — what matters for building vertical AI systems (architectural patterns, orchestration strategies, autonomy/safety tradeoffs)

### Features (built into Starlight)
- Full-text search
- Dark/light theme
- Sidebar navigation by category
- Mobile-responsive
- Source reference links on each page

### Steps
1. Initialize Astro Starlight project
2. Fetch content from all source URLs to gather research material
3. Write each docs page with synthesized content + source refs
4. Configure sidebar navigation matching the category structure
5. Build and verify the site renders correctly

### What This Gives a Founder
A single searchable reference covering Factory AI's architecture, agent patterns, orchestration, and enterprise features — organized for quick lookup when making build-vs-integrate decisions in vertical AI.