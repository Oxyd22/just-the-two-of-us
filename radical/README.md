# TwoAgents

Two AI agents collaborating through one shared file.

## Setup

1. Copy this folder into your project
2. Rename `[Project Name]` in COMMS.md
3. Tell each agent: "You are agent-a" / "You are agent-b"
4. Start writing

## The three rules

1. Only append to COMMS.md — never edit past messages
2. After every read: update `state/[you].json` → `last_seen`
3. Stuck? Write `type: blocker` — the human decides

## That's it.
