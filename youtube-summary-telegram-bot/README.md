# YouTube Summary Telegram Bot

Send the bot `/yt <youtube link>` and it replies with a summary of the video: a short overview plus the key points as bullets. Long videos are split into parts and summarized one message per part.

## Problem

Watching a 40-minute video to find out whether it's worth 40 minutes is a bad trade. Most of the time you only need the main points.

## Solution

Telegram → transcript API → GPT → Telegram.

1. User sends `/yt https://youtu.be/...`
2. The bot extracts the video ID (supports `watch?v=`, `youtu.be/`, `shorts/`, `live/`)
3. Fetches the transcript from [TranscriptAPI](https://transcriptapi.com)
4. Splits long transcripts into parts (40k chars each, max 5)
5. Summarizes each part with GPT and sends it back as `📄 Part 1/3`, `📄 Part 2/3`...

## Stack

- **n8n** — orchestration
- **Telegram Bot API** — trigger and replies
- **TranscriptAPI** — YouTube transcripts
- **OpenAI** — summarization

## Edge cases handled

| Situation | What the user gets |
|---|---|
| Message from someone not on the allowlist | Nothing (ignored, no API cost) |
| Random text, sticker, photo, voice | "I didn't recognize that command" + help |
| `/yt` without a valid link | "Send me a YouTube link" + example |
| Video with no captions / API failure | "Couldn't get a transcript..." (after 1 retry) |
| OpenAI error | "Summary failed, try again later" (after 3 retries, sent once) |
| Very long video (3h+ podcast) | First 5 parts summarized, warning on the last message |
| Slow processing | "⏳ Got it, fetching the transcript..." right away |

## Setup

1. **Import** `youtube-summary-bot.workflow.json` into n8n.
2. **Credentials** — create these and select them in the matching nodes (all set to `PLACEHOLDER` in the export):
   - **Telegram API** — bot token from [@BotFather](https://t.me/BotFather). Used by the trigger and all reply nodes.
   - **Bearer Auth** — your TranscriptAPI key. Used by `Fetch Transcript`.
   - **OpenAI API** — used by `Summarize With GPT`.
3. **Allowlist** — in `Allowed User?`, replace `123456789` with your Telegram chat ID. Easiest way to find it: activate the workflow, message the bot, and read `message.chat.id` from the Telegram Trigger output in the execution log.
4. **Model** — `Summarize With GPT` uses `gpt-5.4-mini`. Change it in the node if you prefer another model.
5. **Optional, recommended** — in workflow settings:
   - set an Error Workflow so you get notified when a run fails
   - enable Timeout Workflow (5 minutes is plenty)
6. Activate the workflow.

## Customization

- **Summary format** — edit the prompt in `Summarize With GPT`.
- **Chunk size / max parts** — `CHUNK_SIZE` and `MAX_CHUNKS` at the top of the `Parse Transcript Text` code node.
- **More users** — change the `Allowed User?` condition to something like `{{ [111, 222].includes($json.message.chat.id) }}` (boolean "is true").
- **More commands** — add a rule to the `Route Command` switch and wire a new branch.
