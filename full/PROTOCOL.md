# Just the Two of Us — Collaboration Protocol v1.0

**System:** Just the Two of Us  
**Scope:** Explicitly for exactly 2 agents: `agent-a` + `agent-b`. See DEC-006.  
**Version:** 1.0  
**Language:** English (DEC-007)

---

## §1 — Core Principles

1. **Files are the bus.** No API channel exists between agents of different providers. The shared filesystem is the only true cross-vendor standard.
2. **COMMS.md is append-only.** Never edit past messages. Corrections are new `type: correction` messages. This is the event log.
3. **BLACKBOARD.md is overwrite-only.** No history, no appending. Current state snapshot only.
4. **State files prevent double-processing.** Each agent updates `state/[agent]-state.json` after every turn. `last_seen_msg_id` is the dedup key.
5. **Rotating coordinator.** No fixed hierarchy. Whoever has the most current overall context coordinates situationally.
6. **Git commits as pheromones.** `last_seen_commit` is a secondary sync anchor. Commits on shared docs signal activity without polling.
7. **Boundary contracts.** Roles have implicit scopes. A reviewer cannot edit source files without an explicit `handoff` message.
8. **Stigmergy over chat.** Prefer updating shared state (BLACKBOARD, state files) over long message threads. Messages are for decisions, not status.
9. **English for agent-to-agent communication.** All content in COMMS.md, BLACKBOARD.md, and `state/*.json` is in English. Human-facing docs may use the project's primary language. (DEC-007)

---

## §2 — YAML Message Envelope

Every message in `COMMS.md` has a YAML front-matter header followed by a Markdown body:

```yaml
---
id: msg-YYYY-MM-DD-NNN        # unique, sequential per day, never reuse
from: agent-a                  # agent-a | agent-b
to: agent-b                    # agent-a | agent-b | both | human | coordinator
reply-to: msg-YYYY-MM-DD-000  # optional — set when replying to a specific message
type: question                 # see §3 for type list
tasks: []                      # affected task IDs, empty if none
priority: normal               # low | normal | high | blocker
created_at: "YYYY-MM-DDTHH:MM:SSZ"
---

Message body here — as long as needed, narrative allowed.
```

**Rules:**
- `id` increments per calendar day, reset to `001` each new day
- `reply-to` is optional but strongly preferred — creates a traceable thread
- `tasks` is an empty array when the message is not task-specific
- `priority: blocker` triggers an immediate response expectation

---

## §3 — Message Types

| Type | When to use |
|------|-------------|
| `task-proposal` | Proposing a new task for the board |
| `review-request` | Asking the other agent to review completed work |
| `handoff` | Transferring ownership of a task or context |
| `question` | Asking for clarification or a decision |
| `blocker` | Unresolvable without help — other agent must respond |
| `idea` | Sharing an observation or suggestion (no action required) |
| `decision` | Announcing a decision taken (informational) |
| `correction` | Correcting an error in a previous message (never edit original) |
| `escalation` | Routing to `human` when both agents are blocked |
| `retrospective` | End-of-session reflection (process improvements) |
| `protocol-update` | Proposing a change to this protocol document |

---

## §4 — Git Workflow

### Branch Strategy

| Branch | Purpose |
|--------|---------|
| `main` | Shared docs only: `COMMS.md`, `BLACKBOARD.md`, `state/`, `DECISIONS.md` |
| `agent-a/[task-id]` | agent-a's task code |
| `agent-b/[task-id]` | agent-b's task code |
| `develop` | Integration branch for reviewed code |

### Commit Message Prefixes

| Prefix | Meaning |
|--------|---------|
| `[comm]` | COMMS.md update |
| `[board]` | BLACKBOARD.md update |
| `[state]` | state file update |
| `[task]` | code change for a task |
| `[doc]` | documentation update |
| `[protocol]` | protocol or decision update |

### Commit Discipline
- Always commit `COMMS.md` and `state/[agent]-state.json` together after writing a message
- Commit `BLACKBOARD.md` after every meaningful state change
- Keep task-code commits on the agent's own branch until review is done

---

## §5 — State Management

Each agent maintains `state/[agent]-state.json`:

```json
{
  "agent": "agent-a",
  "last_seen_msg_id": "msg-YYYY-MM-DD-NNN",
  "last_seen_commit": "abc1234",
  "last_active": "YYYY-MM-DDTHH:MM:SSZ",
  "current_tasks": ["TASK-ID"],
  "blocked_on": null,
  "scheduler_interval_minutes": 10,
  "session": "live",
  "language": "english"
}
```

**Update protocol:**
1. Read COMMS.md — find all messages since `last_seen_msg_id`
2. Process relevant messages (addressed `to: agent-a` or `to: both`)
3. Write response(s) to COMMS.md if needed
4. Update `last_seen_msg_id` to the last processed message
5. Update `last_active` to now
6. Commit state file + COMMS.md together

### Scheduler Snippet (bash)

```bash
#!/bin/bash
# minimal polling loop — run in background or via cron
COMMS="/path/to/your/project/COMMS.md"
STATE="/path/to/your/project/state/agent-a-state.json"
INTERVAL=600  # 10 minutes

while true; do
  LAST=$(jq -r '.last_seen_msg_id' "$STATE")
  LATEST=$(grep "^id:" "$COMMS" | tail -1 | awk '{print $2}')
  if [ "$LAST" != "$LATEST" ]; then
    echo "New message: $LATEST — trigger agent-a"
  fi
  sleep $INTERVAL
done
```

---

## §6 — Blackboard Schema

`BLACKBOARD.md` is the live state snapshot. Overwrite entirely, never append.

```yaml
last_updated: "YYYY-MM-DDTHH:MM:SSZ"
updated_by: agent-a
session_phase: paused           # design | retrospective | live-test | paused
coordinator: null               # agent-a | agent-b | human
language: english               # DEC-007

build_status: unknown           # passing | failing | unknown
build_last_checked: null

review_queue: []                # task IDs waiting for review

active_tasks:                   # exactly 2 agents (DEC-006)
  agent-a: null
  agent-b: null

current_blocker: null           # null or short description

stale_tasks: []                 # tasks with no activity >3 days

next_priority: null             # task ID or description

open_decisions_needed: []       # pending decisions
```

### Stigmergy / Stale-Task Rule
- **>3 days** no activity on a task → add to `stale_tasks`
- **>7 days** → archive to `archive/` folder + remove from board
- The agent who notices the staleness updates BLACKBOARD and notifies via COMMS.md (`type: idea` or `type: question`)

---

## §7 — Trigger Hierarchy

How agents know when to run:

| Trigger | Reliability | Latency | Setup |
|---------|-------------|---------|-------|
| `fswatch` (file watcher) | Medium — dies with shell, not remote-safe | ~1s | Optional, Homebrew |
| CronCreate / scheduled loop | High — survives shell, configurable | 10 min baseline | Recommended |
| Post-commit git hook | High for local, not remote | Seconds | Optional |
| Manual invocation | 100% | On demand | Always available |
| Escalation (human trigger) | 100% | Human response time | Emergency only |

**Baseline:** 10-minute polling via CronCreate or equivalent is the default. fswatch is documented as optional enhancement (NDEC-004).

---

## §8 — Boundary Contracts

Each agent stays within the scope of their current task type:

| Role in turn | May do | May NOT do |
|-------------|--------|-----------|
| Implementer | Edit task-related source files, write tests, update own state | Edit reviewer's files, close tasks unilaterally |
| Reviewer | Comment via COMMS.md, update review_queue, approve/reject | Edit source files being reviewed without explicit handoff |
| Coordinator (situational) | Update BLACKBOARD, propose priorities, break ties | Override the other agent's in-progress work |
| Either agent | Write to COMMS.md, update own state file, update BLACKBOARD | Edit past COMMS.md messages |

---

## §9 — Roles & Escalation

### Rotating Coordinator
No fixed coordinator. Signals that an agent should take the coordinator role:
- Explicitly stated in a `type: decision` message
- Noted in `coordinator:` field on BLACKBOARD
- Naturally assumed when one agent has full current context and the other is blocked

### Escalation Path
```
agent-a blocked
  → sends type: blocker to agent-b
    → agent-b cannot resolve either
      → both send type: escalation to: human
        → human resolves + sends type: decision
          → agents resume
```

`coordinator: human` is a valid BLACKBOARD state during escalation.

---

## §10 — Error Paths

| Situation | Response |
|-----------|---------|
| Message missed (gap in `last_seen_msg_id`) | Process all messages since last known ID before responding |
| Conflicting BLACKBOARD state | More recent `last_updated` wins; note discrepancy in COMMS.md |
| Both agents write the same file simultaneously | Check git log; later commit is canonical; write `type: correction` |
| Agent has been inactive >24h | Other agent may take over their task with a `type: handoff` message |
| Stale task >7 days | Archive + notify human via `type: escalation` |
| Protocol disagreement | Both write `type: idea`, resolve with `type: decision`; update DECISIONS.md |

---

## §11 — Self-Improving Loop

At the end of each significant work session:

1. Write `type: retrospective to: both` in COMMS.md
2. Note: what worked, what caused friction, any protocol gaps
3. If a protocol change is proposed → write `type: protocol-update`
4. If both agents agree → update `PROTOCOL.md` + add/update entry in `DECISIONS.md`
5. Update `BLACKBOARD.md` → `session_phase: paused`

---

## §12 — Live-Test Checklist

Before going live with a new project:

- [ ] Both `state/*.json` files initialized with `session: "new"` reset to `session: "live"`
- [ ] `BLACKBOARD.md` updated to `session_phase: live-test`
- [ ] Both agents have read PROTOCOL.md and ONBOARDING.md
- [ ] First message sent in COMMS.md (`type: decision` — "ready to start")
- [ ] Git repo initialized, initial commit made
- [ ] Scheduler / polling loop active for both agents
- [ ] Human has read HUMAN-QUICKSTART.md

---

## §13 — Scope & Scaling

**v1.0 is explicitly a 2-agent system.** All conventions, schemas, and files are optimized for exactly 2 agents. See DEC-006.

- `to: both` is the standard broadcast
- `active_tasks` has exactly 2 fields
- `state/` contains exactly 2 files
- No coordinator slot, no agent registry, no bid mechanism

**When to upgrade to v2.0:** When there is a concrete need for a third agent. v2.0 is a new protocol, not a patch to v1.0.

**`to: coordinator`** is already valid as an envelope value — pluggable without breaking the current protocol (NDEC-001).

---

*Protocol version: 1.0 — Last updated: [DATE]*  
*Stable. Changes require `type: protocol-update` + mutual agreement + DECISIONS.md entry.*
