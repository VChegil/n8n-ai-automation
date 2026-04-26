# AI-Powered Daily Task Summary

Automated daily workflow that fetches tasks from ClickUp, generates 
a structured summary using Claude, and delivers it via Google Docs + Gmail.

## Problem

Project managers spend 30-60 minutes daily compiling status reports 
from ClickUp tasks across multiple team members. Manual aggregation 
is error-prone and doesn't scale beyond 5-10 active tasks.

## Solution

End-to-end automation: ClickUp → Claude AI → Google Docs → Email.
Triggers manually or on schedule. Generates a structured report with:
- In Progress section
- To Do section  
- Blockers (auto-detected from task descriptions)
- Statistics summary

## Stack

- **n8n** — orchestration layer
- **ClickUp API** — task data source
- **Anthropic Claude (via AI Agent node)** — content generation
- **Google Docs API** — document creation and formatting
- **Gmail API** — delivery

## Architecture

<img width="1519" height="413" alt="image" src="https://github.com/user-attachments/assets/29b933e9-d31a-4d85-874f-e8d7c9a5897c" />

The workflow handles edge cases:
- If no tasks are returned, sends a "nothing to report" notification 
  instead of generating an empty document
- Aggregation happens before AI processing to avoid token waste on 
  duplicate data
- Document creation and update are split into two operations for 
  reliability (create empty → populate with content)

## Customization

The Code node contains the prompt template. Edit `prompt.md` to 
customize tone, structure, or output language.

## Use cases

- Daily standup automation for distributed teams
- Weekly executive reports
- Client-facing project status updates (with appropriate filters)
