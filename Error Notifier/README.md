# Error Notifier (Monitoring Workflow)

## Overview
A dedicated, reusable monitoring workflow that watches other n8n workflows and sends 
an instant Telegram alert whenever one fails, replacing manual execution-log checking.

## How It Works
1. **Error Trigger** — a special n8n trigger that only fires when a *different* workflow it's attached to fails
2. **Send Message** (Telegram) — delivers a formatted alert with the failed workflow's name and error message

## Tech Stack
n8n · Telegram Bot API

## Challenges & Solutions
- **Understanding "soft" vs "hard" failures** — initially, breaking a tool inside the AI Agent (like a bad API endpoint) didn't trigger this workflow, because the agent gracefully recovered and the overall execution still showed "Succeeded." Learned that Error Trigger only fires on true execution-level failures, not recoverable errors inside an agent's tool calls. Verified correctly by breaking a standalone node (Google Sheets credential) instead.

## Result
One monitoring workflow, connected via Settings → Error Workflow, reused across every other automation in this portfolio instead of building monitoring separately each time.
