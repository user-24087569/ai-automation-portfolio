# Automated Daily Digest (Batch Processing)

## Overview
A scheduled automation that reads all logged conversations from the Telegram bot's 
Google Sheet, summarizes each one with AI, and delivers a single consolidated report.

## How It Works
1. **Trigger** (schedule or manual) starts the run
2. **Get Row(s) in Sheet** pulls every logged conversation
3. **Loop Over Items** processes each row individually
4. Inside the loop: an **Execute Workflow** call to the reusable "AI Answer Generator" sub-workflow summarizes each Q&A pair in one line
5. **Aggregate** combines all individual summaries into a single list once the loop completes
6. **Send Message** delivers one combined digest via Telegram

## Tech Stack
n8n · Google Sheets API · Google Gemini API (via sub-workflow)

## Challenges & Solutions
- **Duplicate messages instead of one digest** — the send-message step was initially wired inside the loop, so it fired once per row (14 separate messages for 14 rows) instead of once total. Fixed by moving it to after the loop's "done" output, downstream of a new Aggregate node.
- **Combining results into one message** — required learning array methods (`.map()` to extract each summary, `.join()` to merge them with line breaks).
- **Duplicated AI logic across projects** — rather than rebuilding the AI-call setup here, this project calls the shared `AI Answer Generator` sub-workflow, keeping the logic in one maintainable place.

## Result
A one-click (or fully scheduled) daily summary of all bot activity, with zero manual review needed.
