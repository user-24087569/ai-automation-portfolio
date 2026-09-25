# ai-automation-portfolio
# AI Automation Portfolio

Built by a digital marketer (4+ years in SEO, Google/Facebook Ads, WordPress/Shopify) 
who started teaching himself AI automation in June 2026, applying it to the repetitive 
side of marketing work: reports, content drafts, lead follow-ups.

Every workflow below was built, broken, debugged, and fixed personally, no tutorials 
copy-pasted. The "Challenges & Solutions" sections in each project folder, and the 
journal below, are the real record of that process.

---

## 📁 Projects

| Project | What it does | Stack |
|---|---|---|
| [`telegram-bot/`](./telegram-bot) | Conversational AI chatbot with memory, custom tools, logging, monitoring | n8n · Gemini · Telegram · Sheets |
| [`AI Explainer API with Fact-Ch.../`](<./AI Explainer API with Fact-Ch...>) | Secured, fact-checked AI API via webhook | n8n · Gemini · Wikipedia API |
| [`Daily Digest/`](./Daily%20Digest) | Batch-processes conversation logs into one AI-summarized report | n8n · Sheets · Gemini |
| [`Schedule Agent/`](./Schedule%20Agent) | Fully autonomous, timer-based automation | n8n · Gemini |
| [`Error Notifier/`](./Error%20Notifier) | Reusable monitoring workflow, alerts on failure | n8n · Telegram |
| [`AI Answer Generator for Othe.../`](<./AI Answer Generator for Othe...>) | Reusable "AI-as-a-function" sub-workflow | n8n · Gemini |

---

## 📓 Debug Journal — Every Bug, Cause & Fix

### 🔑 The One Pattern (most important lesson)

> When a node offers **"auto-connect to previous node,"** it's silently expecting an 
> **exact field name**. If the trigger doesn't provide that name, it fails. The fix is 
> always the same: switch to manual/"Define Below" mode and write the expression yourself.

> `$json` only ever refers to the **immediately preceding connected node**. Need data 
> from further back? Use `$('Node Name')` to reference it explicitly.

---

### 🐛 Errors — Full Table

| # | Symptom | Root Cause | Fix |
|---|---------|-----------|-----|
| 1 | Reply never reached Telegram | Output was placed in "Reply Markup" field instead of "Text" | Move output into the **Text** field |
| 2 | "No session ID found" | Memory node expects a `sessionId` field; Telegram doesn't provide one | Session ID → **Define Below** → `{{ $json.message.chat.id }}` |
| 3 | "No prompt specified" | Agent expects `chatInput`; Telegram provides `message.text` | Prompt Source → **Define Below** → `{{ $json.message.text }}` |
| 4 | "chat_id is empty" | `$json` was resolving to the AI Agent's output (immediate predecessor), not the Telegram Trigger | `{{ $('Telegram Trigger').item.json.message.chat.id }}` |
| 5 | "Can't parse entities" — message send crashed | AI-generated Markdown was malformed; Telegram's parser is strict | Delete the Parse Mode field (or instruct the AI to avoid Markdown) |
| 6 | Dropdown "None" selection didn't take | Value was typed, not properly selected | Delete the field, or select via mouse from the dropdown |
| 7 | Joke wasn't from the intended API, despite tool showing as executed | Agent called the tool but discarded its result and generated its own answer ("grounding failure") | System prompt: "use the tool's exact result, never your own; if it fails, say so honestly" |
| 8 | Google Sheets fields went blank/broken unexpectedly | Adjacent fields got touched accidentally while editing | Touch only the field being tested; verify others with a screenshot before/after |
| 9 | A mapped field came through empty | Field names are **case-sensitive** (`Message` ≠ `message`) | Always match the exact casing the trigger provides |
| 10 | Error Workflow didn't fire despite a tool failing | The Agent recovered from the tool failure gracefully; the overall execution still reported "Succeeded" | Error Trigger only fires on **hard failures**, not agent-recovered ones |
| 11 | 14 rows produced 14 separate Telegram messages instead of 1 | The send-message node was wired *inside* the loop | Move it to the loop's **"done"** output, after an Aggregate node |
| 12 | Combining results into one message was confusing | `.map()` / `.join()` syntax was new | `.map(item => item.output)` pulls one field from each item; `.join("\n\n")` merges them with spacing |

---

### 💡 Concepts

| Concept | Summary |
|---|---|
| **Parallel branches** | One node's output can feed multiple independent paths; if one fails, the other still runs |
| **HTTP Request Tool** | Any public API can become an AI-callable tool — just a URL + a clear Tool Description |
| **Grounding** | "Use the tool" isn't enough — the model must be told to use the tool's *exact* result |
| **Error Workflow** | One monitoring workflow can be reused across every other automation |
| **Loop Over Items** | Processes a list one item at a time; "loop" output = current item, "done" output = after all items finish |
| **Aggregate** | Combines per-item results into a single list after a loop, so a downstream action runs once, not per-item |
| **Sub-workflows (Execute Workflow)** | A workflow can call another like a function — one place to update, many places benefit |
| **Structural control > Prompt control** | Telling the AI "don't do X" in a prompt is a soft rule it can ignore; not connecting the tool at all is a hard guarantee |

---

### 🔐 Credentials & Security

| Concept | Summary |
|---|---|
| **Credentials are references, not secrets** | Exported workflow JSON only contains `{ id, name }` — e.g. `"googleSheetsOAuth2Api": { "id": "mVAq...", "name": "Google Sheets account" }` — never the actual token |
| **This is what makes sharing safe** | Exporting to GitHub, sending to a client, etc. never leaks the real credential |
| **Importing requires reconnecting** | Anyone importing the workflow must connect their own credentials — nothing transfers automatically |

---

### 🛡️ Webhook Security

| Concept | Summary |
|---|---|
| **Public webhooks are an open risk** | Anyone can call the URL and exhaust API quota/cost |
| **Fix pattern** | Webhook → IF (`{{ $json.query.key }}` correct?) → true: continue, false: 401 + reject |
| **Generic vs. specific errors** | Public-facing errors should stay generic ("Access Denied"); specific detail belongs in private logs, for debugging, not exposed to callers |

---

### ✅ Pre-Test Checklist

- [ ] Break only **one thing** at a time — leave everything else untouched
- [ ] Revert the break immediately after testing (credential, URL, interval)
- [ ] Changed something in the editor? **Publish/Save** before testing live
- [ ] New field mapping? Double-check exact **case and spelling** against the trigger's actual data
