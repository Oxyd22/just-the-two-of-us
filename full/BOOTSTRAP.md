# Bootstrap Guide — Just the Two of Us (v1.0)

**Time required:** 5–10 minutes  
**Who does this:** The human (Product Owner / developer)  
**Goal:** Get a completely fresh "Just the Two of Us" 2-agent system running in a new project.

---

## Prerequisites

- You have Git in the target project.
- You have decided on the two agents you want to use (any two AI agents from any providers).
- You have copied this folder into your project (recommended location: project root or a dedicated `agents/` folder).

---

## Step-by-Step Setup

### 1. Place the folder

Copy or move the starter folder to the desired location inside your project.

Recommended names:
- `agents/`
- `multi-agent-workflow/`
- `just-the-two-of-us/`

Do **not** put it too deep — agents will reference it often.

### 2. Create the project-level KANBAN.md (very important)

In the **root** of your project (not inside the agents folder), create a file called `KANBAN.md`.

Start with this minimal structure (you can expand it later):

```markdown
# Project Kanban — Shared with the two agents

## Roles & Strengths
| Agent   | Strengths | Typical Tasks |
|---------|-----------|---------------|
| Agent A | [describe] | Features, implementation |
| Agent B | [describe] | Reviews, architecture |

## In Progress
| # | Task | Agent | Started |
|---|------|-------|---------|

## Review
| # | Task | From | Reviewer | Notes |
|---|------|------|----------|-------|

## Backlog
| # | Task | Agent | Priority | Notes |
|---|------|-------|----------|-------|
```

Commit this file. The agents treat this as the single source of truth for work.

### 3. Initialize the two state files

Go into `state/` inside the starter folder.

Rename and edit `agent-a-state.json` and `agent-b-state.json`.

Minimal starting content (replace `agent-a` with the actual name/identifier you use for that agent):

**agent-a-state.json**
```json
{
  "agent": "agent-a",
  "last_seen_msg_id": "msg-init-001",
  "last_seen_commit": "",
  "last_active": "[current ISO timestamp]",
  "current_tasks": [],
  "blocked_on": null,
  "scheduler_interval_minutes": 10,
  "session": "bootstrap",
  "language": "english"
}
```

**agent-b-state.json** — same structure, change `agent` field to `"agent-b"`.

Save both. If your agents have specific names (e.g. "grok" and "claude"), you can rename the files accordingly — just keep the naming consistent with BLACKBOARD.md.

### 4. Prepare BLACKBOARD.md (shared live state)

Open `BLACKBOARD.md` in the starter folder.

Set sensible initial values:

```yaml
last_updated: "[current ISO timestamp]"
updated_by: human
session_phase: bootstrap          # bootstrap | live-test | paused
coordinator: human
language: english

build_status: unknown
review_queue: []
active_tasks:
  agent-a: null
  agent-b: null
current_blocker: null
stale_tasks: []
next_priority: "First agent run after bootstrap"
open_decisions_needed: []
```

This file is overwritten frequently by the agents — treat it as the current dashboard.

### 5. Seed COMMS.md with a clean starting message

Open `COMMS.md`.

The file already contains the format reference header. Add a single clean starter message below it:

```markdown
## System Initialized

---
id: msg-init-001
from: human
to: both
type: decision
tasks: []
priority: normal
created_at: "[current ISO timestamp]"
---

The "Just the Two of Us" workflow has been bootstrapped for this project.

All rules are in PROTOCOL.md.
Current shared state is in BLACKBOARD.md.
Tasks live in the top-level KANBAN.md.

First real task will be posted by the human or proposed by an agent.

---
```

From now on the agents will only append (never edit earlier messages).

### 6. (Optional but recommended) Update DECISIONS.md

The `DECISIONS.md` already contains the universal core decisions (DEC-001 to DEC-007 + the main NDECs).

Add a new entry for your project:

```markdown
### DEC-010 — Adopted "Just the Two of Us" starter in [Your Project]
**Date:** [today]
**Decision:** Using the v1.0 starter kit for [Agent A] + [Agent B] collaboration.
```

### 7. First commit & first agent run

1. `git add` the entire starter folder + the new top-level `KANBAN.md`.
2. Commit with a clear message, e.g.:
   ```
   chore: bootstrap "Just the Two of Us" 2-agent workflow (v1.0 starter)
   ```
3. Tell one (or both) agents to do their first run:
   - Point them at the folder.
   - Ask them to read `ONBOARDING.md` first, then `BLACKBOARD.md`, then their own `state/[agent]-state.json`, then `COMMS.md` (from the init message onward), then `KANBAN.md`.
   - They should now be able to propose the first task or wait for your direction.

---

## Common First Actions After Bootstrap

- Post the first 3–5 real tasks into KANBAN.md with rough priority and suggested owner.
- Decide on a scheduler rhythm (10 minutes is the proven default).
- Let the agents introduce themselves in COMMS with a `type: idea` or `type: decision` message.
- Watch BLACKBOARD.md for the first state updates.

## Troubleshooting

**"The agents don't see my changes"**  
→ Make sure they re-read BLACKBOARD + their state file at the beginning of every run.

**"COMMS.md is getting long"**  
→ Move finished threads into `archive/YYYY-MM/` and leave a one-line pointer in COMMS. Trigger: >500 lines or >20 closed threads (NDEC-006).

**"I want to change the protocol later"**  
→ Never edit PROTOCOL.md directly in a running project. Open a `type: protocol-update` discussion in COMMS and only change it after both agents have explicitly agreed (`type: decision`). Then update DECISIONS.md.

**"One agent has been inactive for days"**  
→ Check their `state/[agent]-state.json` → `last_active`. If >24h, the other agent may take over their task with a `type: handoff` message. If >7 days, escalate to human via `type: escalation`.

**"Both agents are stuck"**  
→ Both send `type: escalation to: human` in COMMS. Respond with `type: decision to: both`. Agents resume.

---

## You are done.

You now have a clean, fully functional "Just the Two of Us" instance.

From this point forward the two agents can work with a high degree of autonomy while you stay in strategic control via KANBAN and BLACKBOARD.

Welcome aboard — just the two of us.

*Created from the v1.0 protocol (2026-05-26)*
