# Just the Two of Us — Agent Onboarding

**Read this first.** This is your cold-start guide. You are one of exactly 2 agents in this system.  
Estimated reading time: ~5 minutes.

---

## §1 — What is this system?

"Just the Two of Us" is a **file-based collaboration protocol for two AI agents** working on a shared project. There is no real-time API between agents — the shared filesystem and Git are the coordination layer.

You are either **agent-a** or **agent-b**. Your human will tell you which. If they haven't, ask before proceeding.

The system has exactly 5 core files you need to know:

---

## §2 — The 5 Key Files (read in this order on first start)

| File | Type | Rule |
|------|------|------|
| `PROTOCOL.md` | Reference | Read once. Re-read after retrospectives. |
| `DECISIONS.md` | Reference | Understand what was decided and why. |
| `BLACKBOARD.md` | Live state | Read every session. Overwrite, never append. |
| `COMMS.md` | Event log | Append-only. Never edit past messages. |
| `state/[your-agent]-state.json` | Your state | Update after every turn. |

**Also check:** `state/[other-agent]-state.json` to understand what the other agent last did.

---

## §3 — First Run: Step by Step

1. **Read** `PROTOCOL.md` (this entire file, once)
2. **Read** `DECISIONS.md` — understand the why behind the system
3. **Read** `BLACKBOARD.md` — what is the current state?
4. **Read** `COMMS.md` — scan from the beginning, or from `last_seen_msg_id` in your state file if resuming
5. **Check** `state/[other-agent]-state.json` — when were they last active?
6. **Update** `state/[your-agent]-state.json`:
   - Set `last_seen_msg_id` to the latest message ID in COMMS.md
   - Set `last_active` to now (ISO 8601)
   - Set `session` to `"live"` (not `"new"`)
7. **Write** your first message to `COMMS.md` if appropriate (see §4)
8. **Commit** your state file + COMMS.md together

---

## §4 — How to Communicate

Every message in `COMMS.md` has a YAML front-matter header. **Always use this format:**

```yaml
---
id: msg-YYYY-MM-DD-001
from: agent-a
to: agent-b
reply-to: msg-YYYY-MM-DD-000
type: question
tasks: []
priority: normal
created_at: "YYYY-MM-DDTHH:MM:SSZ"
---

Your message body here. As long as needed. Narrative is fine.
```

### Message types at a glance:
- `task-proposal` — suggest a new task
- `review-request` — ask the other agent to review your work
- `handoff` — transfer task ownership
- `question` — ask for clarification
- `blocker` — you're stuck and need help
- `idea` — observation or suggestion (no action required)
- `decision` — announce a decision
- `correction` — fix an error in a past message (never edit the original)
- `escalation` — route to human when both agents are blocked
- `retrospective` — end-of-session reflection
- `protocol-update` — propose a protocol change

**Message body written in English (DEC-007).**

### The golden rule:
> COMMS.md is **append-only**. Never edit past messages. Ever.

---

## §5 — Your Role

You and the other agent have **complementary strengths**. There is no fixed hierarchy.

- **Rotating coordinator**: whoever has the most current overall context leads situationally. This is noted in `coordinator:` field on BLACKBOARD.md.
- **Boundary contracts**: stay within your task scope. A reviewer does not edit source files. An implementer does not close tasks unilaterally.
- **Escalation**: if both agents are stuck → `type: escalation to: human`.

---

## §6 — Git Conventions

| Branch | Purpose |
|--------|---------|
| `main` | Shared docs: COMMS.md, BLACKBOARD.md, state/, DECISIONS.md |
| `agent-a/[task-id]` | agent-a task code |
| `agent-b/[task-id]` | agent-b task code |
| `develop` | Integration (reviewed code) |

**Commit prefixes:** `[comm]` `[board]` `[state]` `[task]` `[doc]` `[protocol]`

Always commit your state file together with COMMS.md after writing a message.

---

## §7 — What NOT to Do

- ❌ Edit past messages in COMMS.md — write a `type: correction` instead
- ❌ Overwrite `COMMS.md` entirely — it is append-only
- ❌ Skip updating your state file — this causes double-processing
- ❌ Start a task that's already in `active_tasks` on BLACKBOARD — check first
- ❌ Escalate to human before trying to resolve with the other agent first
- ❌ Write in a language other than English in agent-facing files (COMMS, BLACKBOARD, state)
- ❌ Assume the other agent has read your message — check their `last_seen_msg_id`

---

## §8 — This is a 2-Agent System

This protocol (v1.0) is designed for **exactly 2 agents**. The documents, schemas, and conventions are optimized for this:

- `to: both` is the broadcast
- `active_tasks` has exactly 2 slots
- `state/` has exactly 2 files

If a third agent ever joins, that requires **PROTOCOL.md v2.0**, not a patch. See DEC-006.

---

## §9 — Common Situations

**"I don't know what to work on next"**
→ Read BLACKBOARD.md → `next_priority`. If null, send `type: question to: [other-agent]`.

**"The other agent hasn't responded in a long time"**
→ Check `state/[other-agent]-state.json` → `last_active`. If >24h, you may take over their task with a `type: handoff` message.

**"I found a bug/issue not in my task"**
→ Send `type: idea to: both` — document it but don't act on it unless you're assigned.

**"Something in the protocol feels wrong"**
→ Send `type: retrospective to: both` — propose changes. If both agree → update PROTOCOL.md + DECISIONS.md.

**"I need a human decision"**
→ `type: escalation to: human` — state clearly what you need decided and why agents cannot resolve it.

**"I finished my task"**
→ Update your state (`current_tasks: []`), update BLACKBOARD (`active_tasks.[you]: null`), send `type: review-request` if review is needed, or `type: handoff` if passing to the other agent.

---

## §10 — Ready Checklist

Before your first active turn:

- [ ] I know which agent I am (agent-a or agent-b)
- [ ] I have read PROTOCOL.md
- [ ] I have read DECISIONS.md
- [ ] I have read BLACKBOARD.md (current state)
- [ ] I have read COMMS.md (full history or since my last_seen_msg_id)
- [ ] I have checked the other agent's state file
- [ ] My state file is updated (`last_active`, `last_seen_msg_id`, `session: "live"`)
- [ ] I know what my first action is

---

*Part of the "Just the Two of Us" protocol v1.0*  
*Questions about the system? → `type: question to: [other-agent]` in COMMS.md*
