# Human Quickstart — Just the Two of Us

**For:** Product owners, developers, and anyone supervising the two-agent system.  
**Reading time:** < 2 minutes.

---

## What is this?

Two AI agents (`agent-a` and `agent-b`) collaborate on your project via shared files. No real-time API connection exists between them — they communicate through the filesystem and Git.

You are the **human-in-the-loop**: you supervise, unblock, and make final calls when the agents can't resolve something on their own.

---

## The 5 Files You Need to Know

| File | What it is | Your role |
|------|-----------|-----------|
| `BLACKBOARD.md` | Live status snapshot | Read this to understand current state at a glance |
| `COMMS.md` | Full agent conversation log | Read for context; you can also write messages here |
| `KANBAN.md` *(your file)* | Task board | Manage tasks here; agents will reference it |
| `DECISIONS.md` | Why things are the way they are | Read when you want to understand decisions |
| `HUMAN-QUICKSTART.md` | This file | — |

---

## How to Communicate with the Agents

Write a message directly in `COMMS.md` using this format:

```yaml
---
id: msg-YYYY-MM-DD-001
from: human
to: both
type: decision
tasks: []
priority: normal
created_at: "YYYY-MM-DDTHH:MM:SSZ"
---

Your message here. Plain text is fine.
```

Common message types for humans:
- `decision` — you've made a call the agents were waiting for
- `task-proposal` — you want to add a new task
- `escalation` — (agents use this to reach you; you respond with `type: decision`)

---

## Quick Status Check

Open `BLACKBOARD.md`. It tells you:
- `session_phase` — what stage is the work in right now?
- `active_tasks` — what is each agent working on?
- `current_blocker` — is anything stuck?
- `review_queue` — what needs your attention?
- `next_priority` — what should be worked on next?

---

## When Do You Need to Act?

Agents will send `type: escalation to: human` in COMMS.md when:
- Both agents are blocked on the same issue
- A decision requires product/business judgment
- A task has been stale for >7 days with no resolution

**Your response:** Write `type: decision to: both` with your answer. Agents will resume.

---

## Important Rules (Don't Break These)

| Rule | Why |
|------|-----|
| Never edit past messages in COMMS.md | It's the permanent record — corrections are new messages |
| BLACKBOARD.md is the live state — agents overwrite it | Don't be surprised when it changes |
| Agents communicate in English | Technical terms are cleaner in English (DEC-007) |
| This is a 2-agent system by design | Adding a 3rd agent requires a protocol upgrade (DEC-006) |

---

## Typical Flow for a New Project

1. **Set up** — copy this folder to your project, rename `[Project Name]` placeholders
2. **Assign agents** — tell each AI "you are agent-a" or "you are agent-b"
3. **Send the start signal** — write `type: decision to: both` in COMMS.md: "System is initialized. Start with [first task]."
4. **Monitor** — check BLACKBOARD.md periodically; agents run on a ~10-minute polling cycle
5. **Respond to escalations** — agents will reach out when they need you
6. **Review completed work** — agents will send `type: review-request` when tasks are done
7. **Close the session** — agents will write a `type: retrospective`; review it and confirm

---

*Part of the "Just the Two of Us" protocol v1.0*
