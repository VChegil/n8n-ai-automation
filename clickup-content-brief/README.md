# n8n AI Automations

Production-ready n8n workflows that combine **ClickUp**, **Claude AI**, and **Google Workspace** to automate repetitive project management tasks.

Built as portfolio projects to demonstrate real-world AI automation patterns: API integration, webhook triggers, structured prompt engineering, and multi-output delivery.

---

## Workflows

### 1. ClickUp Task Summarizer

**Problem:** Project managers spend 30–60 minutes daily compiling status reports from active ClickUp tasks. Manual aggregation is error-prone and doesn't scale beyond a small team.

**Solution:** Manual or scheduled trigger fetches all `In Progress` and `To Do` tasks, sends them through Claude for structured summarization, and delivers the result as a Google Doc + email.

**Flow:**
```
Manual Trigger → ClickUp (Get Tasks) → Aggregate → IF (any tasks?)
   ├── YES → Code (clean data + build prompt) → Claude AI Agent
   │           → Create Google Doc → Update Google Doc → Send Email
   └── NO  → Send "no tasks" notification
```

**Output sections:** In Progress / To Do / Blockers / Stats (with most-loaded assignee).

**File:** [`daily-summary-workflow.json`](./content-brief-workflow.json)

---

### 2. AI Content Brief Generator

**Problem:** Content teams spend the first hour of any new article briefing it — defining target audience, structure, SEO keywords, and read time. Most of this is repeatable from the article title alone.

**Solution:** Real-time webhook trigger. When a `content-brief` tag is added to any ClickUp task, the workflow auto-generates a structured brief using Claude and writes it back into the task description + a Google Doc copy.

**Flow:**
```
ClickUp Webhook (taskTagUpdated) → IF (tag = "content-brief")
   → HTTP Request (fetch full task) → Claude (Basic LLM Chain)
   → Update ClickUp task → Create Google Doc → Update Google Doc
```

**Output sections:** Target Audience / Key Points / SEO Keywords (primary + secondary) / Suggested Structure / Estimated Read Time.

**Note on architecture:** The HTTP Request node is used (instead of the native ClickUp node) because the webhook payload only contains `task_id`, not the full task object. A direct API call returns all fields needed for the prompt.

**File:** [`content-brief-workflow.json`](./content-brief-workflow.json)

---

## Stack

- **n8n** (self-hosted or cloud) — orchestration
- **ClickUp API** — task data source / sink
- **Anthropic Claude (Sonnet 4)** — content generation
  - Used via n8n's AI Agent node (with memory/tool support) and Basic LLM Chain node
- **Google Docs API** — document creation and population
- **Gmail API** — delivery

## Why Claude over ChatGPT for this use case

These workflows handle business data (tasks, project information, internal communications). Anthropic's API does not train models on data sent through the API by default, which matters for agency-client confidentiality. OpenAI requires explicit opt-out.

## License

MIT — fork, modify, and use freely.
