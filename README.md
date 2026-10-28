# n8n AI Automations

A growing collection of production-ready n8n workflows that solve real business problems with AI.

Each workflow demonstrates a different automation pattern — webhook triggers, API integration, structured prompt engineering, multi-output delivery — and is built to be forked, customized, and deployed.

---

## Workflows

| Workflow | Description | Stack |
|---|---|---|
| [🇨🇿 Czech Investment Tax Bot](./n8n-czech-investment-tax-bot) | Telegram bot that calculates Czech taxes on Trading 212 / IBKR investments: FIFO, 3-year test, 100k limit, ČNB rates, tax-return summary | Telegram · n8n Data Tables · ČNB API |
| [📋 Daily Summary](./clickup-ai-daily-summary) | Fetches active ClickUp tasks and generates a structured daily report (Google Doc + email) | ClickUp · Claude · Google Docs · Gmail |
| [✍️ Content Brief Generator](./clickup-content-brief) | Auto-generates a content brief when a tag is added to a ClickUp task | ClickUp Webhook · Claude · Google Docs |
| [▶️ YouTube Summary Bot + Video Memory](./youtube-summary-telegram-bot) | Telegram bot that summarizes YouTube videos and stores them in a self-hosted vector DB, searchable from Claude via MCP with timestamped sources | Telegram · TranscriptAPI · OpenAI · Gemini · Qdrant · MCP |
| [🎯 AI Lead Qualifier](./ai-lead-qualifier) | Scores inbound form leads with Claude, researches the company (website or Tavily web search), upserts to HubSpot, alerts hot leads in Telegram and drafts a personal Gmail reply | Tally · Claude · Tavily · HubSpot · Telegram · Gmail |

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
