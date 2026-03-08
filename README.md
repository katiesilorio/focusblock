# FocusBlock
> AI attention layer for async teams — surfaces what matters across
> Slack, Jira, and Google Docs.


**Status:** In development · Built with React, Supabase, Anthropic API
**Audience:** Engineers, PMs, designers at 10-200 person async-first teams


---


## The Problem


Knowledge workers at async-first companies coordinate across at least
four tools: Slack, Jira, Google Docs, and email. Each sends its own
notifications on the sender's timeline, not the recipient's. The result
is a context-switching tax that makes deep work structurally impossible.


The problem is not too many conversations — it is that conversations are
spread across tools that don't talk to each other, arriving at random
times, requiring full mental reconstruction on every interaction.


---


## What FocusBlock Does


FocusBlock sits on top of your existing tools as an AI attention layer.
It reads everything — every Slack message, Jira comment, and Google Doc
annotation — and organizes it into a single prioritized Feed.


**Three core concepts:**


| Concept | Definition |
|---------|------------|
| **Block** | A container for a project or initiative. Contains everything related to it across all tools.
| **Signal** | A discrete conversation thread within a Block: one question, one decision, one issue.
| **Cue** | A single raw message, comment, or notification from a connected tool.


---


## Core Features


**Feed** — One card per Block, always. AI paragraph summary consolidates
cross-tool activity into prose. Sorted by urgency (Blocking → Action
Required → FYI) then Block priority.


**Signals & Cues** — Slack threads as discrete Signals. Reply with AI
populates reply text using Signal history, Block decision log, and linked
Jira context. Never auto-sends.


**Block View** — Side panel to full Block view. Five tabs: All, Slack,
Jira, Docs, Chat. Block-level AI chat scoped to complete Block history.


**Focus Mode** — User-declared deep work with Slack notification. Blocking
Signals break through. Everything else held, then released by priority.


---


## Technical Architecture


**Stack:** React · Supabase (PostgreSQL + Edge Functions + Realtime)
· Anthropic API


**Integrations:** Slack (OAuth + Events API) · Jira (OAuth + REST API)
· Google Docs (OAuth + Drive API)


**AI pipeline:**
1. Webhook events queue in raw_events table
2. pg_cron triggers edge function every 30 seconds
3. Event processor classifies urgency, assigns to Block, clusters into Signal
4. Signal summary and Block Feed summary regenerated via Claude
5. Supabase Realtime pushes updates to Feed in browser


**Key schema tables:**
  blocks        → core organizing unit (name, status, priority, decision_log)
  block_members → who belongs to each block (RLS enforced)
  signals       → topic clusters within a block (urgency, status, ai_summary)
  cues          → individual raw messages/comments from source tools
  raw_events    → webhook event queue (pending → processing → processed)


**Row Level Security:** Every table uses Supabase RLS. Users can only
read Blocks they are members of. OAuth tokens are encrypted before storage.


---


## Product Documentation


- [Full PRD](./docs/PRD.md) — Problem, target users, core concepts,
  feature table, success metrics
- [Database Schema](./docs/schema.md) — Full SQL with indexes and
  RLS policies
- [AI Architecture](./docs/ai-architecture.md) — Signal clustering,
  urgency classification, reply drafting
- [Design Decisions](./design/DESIGN_DECISIONS.md) — Key tradeoffs
  and rationale


---


## Why I Built This


I'm a Staff PM at DoorDash with a background in sales operations and
business systems. The context-switching problem is one I watch affect
high-performing async teams every day. The cost is real (23-minute
average recovery time per interruption), but most solutions address the
symptom (notification volume) rather than the cause (no shared context
model across tools). FocusBlock is my attempt to build the product
I'd want my team to use.


*Built by Katie Silorio · [LinkedIn](https://linkedin.com/in/katie-silorio)*
