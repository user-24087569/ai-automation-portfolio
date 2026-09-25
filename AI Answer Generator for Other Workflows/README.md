# AI Answer Generator (Reusable Sub-Workflow)

## Overview
A standalone, reusable "AI-as-a-function" workflow. Instead of duplicating the same 
AI Agent setup across multiple automations, this workflow takes a question as input 
and returns an answer, callable from anywhere via n8n's Execute Workflow node.

## How It Works
1. **When Executed by Another Workflow** trigger receives a `question` input field
2. **AI Agent** answers it in one short sentence
3. Result is returned to whichever workflow called it

## Tech Stack
n8n · Google Gemini API

## Challenges & Solutions
- **Avoiding duplicated logic** — built this after noticing the same AI-calling setup was being rebuilt in multiple workflows (Daily Digest, potential future automations). Extracting it here means a single update (e.g., switching AI models) propagates everywhere it's called from.
- **Design consideration**: for use cases needing different AI behavior per caller (e.g., one caller needing Wikipedia access, another not), the plan is to keep tool access structurally separate (different sub-workflow variants) rather than relying on prompt instructions alone, since prompt-only restrictions proved unreliable (see the grounding bug in the Telegram bot project).

## Result
A single, reusable AI-calling workflow currently powering the Daily Digest project, designed to be called from future automations without rebuilding the setup each time.
