# Just the Two of Us — Minimal Protocol

This is the entire rule set. If it's not here, it doesn't exist.

## The Three Files

1. **KANBAN.md** (in project root)
   - The source of truth for what should be worked on.
   - Human sets priority and owner. Agents can propose.

2. **COMMS.md**
   - The only place the two agents talk to each other.
   - Append-only. Never edit old messages.

3. **BLACKBOARD.md**
   - The current shared reality.
   - Overwritten, never appended.
   - Contains whatever both agents need to know right now (current task, blockers, decisions, etc.).

## Core Rules

- **Append-only in COMMS.md.** Corrections are new messages.
- **BLACKBOARD.md is truth.** Both agents keep it consistent and up to date.
- **Git is the backbone.** If something matters, commit it.
- **No ceremony.** No message types, no IDs, no envelopes. Clarity comes from discipline, not structure.
- **Human stays in control** of priorities. Agents stay in control of execution and communication.

## When in Doubt

- Put it in BLACKBOARD.md if both need to know it.
- Put it in COMMS.md if you're talking to the other agent.
- Put it in KANBAN.md if it's a piece of work.

That's the whole protocol.