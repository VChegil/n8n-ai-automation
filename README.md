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

Крок 5: Loom-відео (15-20 хвилин)
Сценарій (англійською, навіть з акцентом — нормально):
"Hi, I'm [name]. Here's an N8N workflow I built that solves a real 
problem agencies have — daily task reporting.

[Show workflow diagram]

It pulls all 'In Progress' and 'To Do' tasks from ClickUp, aggregates 
them, sends them to Claude as a structured prompt, and generates 
a categorized summary that goes into a Google Doc and an email.

[Show ClickUp with tasks]
Here are my test tasks in ClickUp.

[Run the workflow live]
Let me run it. [wait 10 seconds]

[Show Google Doc result]
Here's the generated document — In Progress, To Do, Blockers, Stats sections.

[Show email]
And the email arrives in inbox.

The whole thing took about 30 minutes to build. Workflow JSON is on 
my GitHub: [link].

Happy to chat if you have similar automation needs."
