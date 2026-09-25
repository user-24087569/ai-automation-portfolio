# AI Explainer API with Fact-Checking

## Overview
A live, webhook-triggered REST-style API that takes any topic and returns a simplified, 
fact-checked explanation, secured against public misuse.

## How It Works
1. **Webhook Trigger** receives a GET request with a `topic` query parameter
2. **IF node** checks for a valid API key (`?key=`) — invalid/missing requests get a 401 response immediately
3. **IF node** validates the topic field isn't empty or whitespace-only
4. **AI Agent** (with Wikipedia tool access) generates the explanation, instructed to verify facts before answering and disclose uncertainty when unsure
5. **Respond to Webhook** sends the answer directly back to the caller

## Tech Stack
n8n · Google Gemini API · Wikipedia API

## Challenges & Solutions
- **Silent input bugs** — empty or whitespace-only topic fields passed a naive "is not empty" check, since a string with just a space isn't technically empty. Fixed using `.trim()` inside the expression.
- **Infinite loop / Max Iterations error** — an overly strict system prompt ("must verify every fact") caused the agent to repeatedly re-query the tool without converging on an answer. Fixed by relaxing the instruction to "verify once, then answer," with fallback guidance for inconclusive results.
- **Unsecured public endpoint** — the API was initially callable by anyone with no restriction, a real cost/abuse risk. Fixed by adding API-key authentication via an IF node before any processing happens.

## Result
A publicly accessible, authenticated AI API with fact-checking and graceful failure handling.
