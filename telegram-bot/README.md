# Telegram AI Chatbot with Memory, Custom Tools & Monitoring

## Overview
A production-style conversational AI chatbot built on Telegram, using n8n and Google Gemini. 
Unlike a simple Q&A bot, this maintains per-user conversation memory, calls a custom 
external API as an AI-callable tool, logs every conversation automatically, and includes 
a dedicated failure-monitoring system.

## How It Works
1. **Telegram Trigger** receives incoming messages
2. **AI Agent** (Google Gemini) processes the message, with:
   - **Simple Memory** — scoped per-user via Telegram Chat ID, so the bot remembers context across a conversation
   - **HTTP Request Tool** — calls an external public API (chucknorris.io) for real-time content the model can't know from training
3. Output splits into two parallel branches:
   - **Send Message** back to the user on Telegram
   - **Append Row** to a Google Sheet, logging timestamp, user, question, and answer
4. A separate **Error Workflow** watches this workflow and sends an instant Telegram alert if anything fails

## Tech Stack
n8n · Google Gemini API · Telegram Bot API · Google Sheets API

## Challenges & Solutions
- **Field-mismatch errors** — n8n's auto-connect features (Memory's `sessionId`, Agent's `chatInput`) expect specific field names that Telegram doesn't provide by default. Fixed by manually mapping expressions instead of relying on auto-detect.
- **Markdown crashes** — AI-generated markdown occasionally broke Telegram's strict parser. Fixed by disabling Parse Mode and instructing the AI (via system prompt) to avoid markdown formatting, with a specific rule for emoji placement to keep tone consistent.
- **Grounding failure (the hardest bug)** — the AI was calling the external tool successfully (visible as a green checkmark in execution logs) but silently discarding the result and answering from its own memory instead. This was only caught by comparing the tool's raw output against the final message sent. Fixed by explicitly instructing the model to use the tool's exact output rather than paraphrasing or replacing it.

## Result
A live, working chatbot with memory, live data access, automatic logging, and self-monitoring, running on n8n's free tier.
