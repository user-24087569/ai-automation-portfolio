# Scheduled AI Automation

## Overview
A proactive automation that runs entirely on its own timer, with no external trigger 
or manual action required, demonstrating time-based (as opposed to event-based) automation design.

## How It Works
1. **Schedule Trigger** fires on a fixed interval
2. **AI Agent** generates content (e.g., a random fact) on each run
3. Output is available for logging, sending, or further processing

## Tech Stack
n8n · Google Gemini API

## Challenges & Solutions
- **Execution quota awareness** — testing at a 1-minute interval would exhaust a free-tier execution quota (1,000/month) within hours. Adjusted the interval to a realistic production cadence (weekly) once testing was verified working.

## Result
A working example of proactive, timer-based automation as a complement to event-driven (webhook) automation.
