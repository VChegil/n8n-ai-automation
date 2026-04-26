# n8n AI Automations

A growing collection of production-ready n8n workflows that solve real business problems with AI.

Each workflow demonstrates a different automation pattern — webhook triggers, API integration, structured prompt engineering, multi-output delivery — and is built to be forked, customized, and deployed.

---

## Workflows

| Workflow | Description | Stack |
|---|---|---|
| [📋 Daily Summary](./daily-summary) | Fetches active ClickUp tasks and generates a structured daily report (Google Doc + email) | ClickUp · Claude · Google Docs · Gmail |
| [✍️ Content Brief Generator](./content-brief) | Auto-generates a content brief when a tag is added to a ClickUp task | ClickUp Webhook · Claude · Google Docs |

*More workflows coming — focused on agency, content, and SaaS automation use cases.*

---

## Philosophy

- **Real problems, not demos.** Every workflow solves a task someone is doing manually right now.
- **Production-ready.** Error handling, edge cases, and anti-hallucination guardrails are part of the design — not afterthoughts.
- **Privacy-aware.** Claude is preferred over ChatGPT for business data — Anthropic doesn't train on API data by default, OpenAI requires explicit opt-out.
- **Forkable.** All credentials and IDs are placeholders. Each workflow folder includes setup instructions.

## Setup

Each workflow folder has its own README with:
- Required credentials
- ID placeholders to replace
- Import and configuration steps
- Customization options (prompts, triggers, filters)

General requirements:
- n8n instance (self-hosted or cloud)
- API access for the services involved (varies per workflow)

## About

Built by a backend developer (PHP / Java) focused on n8n + AI automation. Open to consulting work on similar projects — reach out via [LinkedIn](#) or email.

## License

MIT — fork, modify, and use freely.