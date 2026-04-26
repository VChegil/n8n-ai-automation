# AI Content Brief Generator

Real-time webhook automation. When a `content-brief` tag is added to any ClickUp task, this workflow auto-generates a structured content brief using Claude and writes it back into the task description + a Google Doc copy.

## Problem

Content teams spend the first hour of every new article on briefing — defining target audience, structure, SEO keywords, and read time. Most of this is repeatable from the article title alone.

## Solution

## Architecture

<img width="1324" height="362" alt="image" src="https://github.com/user-attachments/assets/0af7f152-957b-48de-bc4a-641fd5b23f91" />

A tag-triggered workflow that turns the briefing step into a 5-second action: tag the task, get a structured brief.

## Flow

```
ClickUp Webhook (taskTagUpdated) → IF (tag = "content-brief")
   → HTTP Request (fetch full task) → Claude (Basic LLM Chain)
   → Update ClickUp task → Create Google Doc → Update Google Doc
```

## Output sections

- 🎯 Target Audience
- 📌 Key Points (5 max)
- 🔍 SEO Keywords (primary + secondary)
- 📐 Suggested Structure
- ⏱ Estimated Read Time

## Architecture note

The HTTP Request node is used instead of the native ClickUp node because the webhook payload only contains `task_id`, not the full task object. A direct API call returns all fields needed for the prompt.

## Stack

- n8n
- ClickUp Webhook + API
- Anthropic Claude (via Basic LLM Chain node)
- Google Docs API

## Setup

1. Import [`content-brief-workflow.json`](./content-brief-workflow.json) into n8n
2. Replace placeholders:
   - `YOUR_TEAM_ID` — ClickUp team ID
   - `YOUR_FOLDER_ID` — ClickUp folder to monitor
   - `YOUR_GOOGLE_DRIVE_FOLDER_ID` — destination folder for generated docs
3. Configure credentials:
   - ClickUp API (used by Trigger and Update Task nodes)
   - HTTP Header Auth named "ClickUp API token" — header `Authorization`, value = your ClickUp token (used by HTTP Request node)
   - Anthropic API key
   - Google Docs OAuth2
4. Activate the workflow
5. Add the `content-brief` tag to any ClickUp task to trigger generation

## Customization

- **Prompt:** edit the `Basic LLM Chain` node's text and system message to change tone, language, or output structure
- **Trigger condition:** the `IF` node currently matches the exact string `content-brief` — change to match a different tag name
- **Filter scope:** the trigger watches a specific `folderId` — remove this filter to monitor the entire workspace
