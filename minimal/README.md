# Just the Two of Us — Minimal

The smallest possible version of a high-leverage 2-agent file-based collaboration system.

**Exactly two agents.** No more. No framework. No ceremony.

## Core Idea

Two agents (Grok + Claude) collaborate through a Git repository using three simple files:

| File          | Purpose                              | Who edits it mostly |
|---------------|--------------------------------------|---------------------|
| `KANBAN.md`   | Tasks, priorities, status            | Human + both agents |
| `COMMS.md`    | Agent-to-agent communication         | Agents only         |
| `BLACKBOARD.md` | Current shared reality (live state) | Both agents         |

That's it.

## Why This Is Minimal

- No YAML envelopes
- No message types
- No Boundary Contracts
- No DECISIONS.md
- No onboarding documents
- No rotating coordinator
- No self-improving loop process

Just three files + Git + discipline.

## How It Works

1. The human creates `KANBAN.md` in the project root.
2. The two agents only communicate in `COMMS.md`.
3. They keep `BLACKBOARD.md` as the single source of truth for "what is the current state?"
4. Everything important is committed. Git history is the audit trail.
5. A simple scheduler (or manual trigger) makes an agent wake up, read the three files, and act.

## Rules (The Only Rules)

- `COMMS.md` is append-only. Never edit old messages.
- `BLACKBOARD.md` is overwritten, never appended. It is the current truth.
- Tasks live in `KANBAN.md`. Agents can propose, the human decides priority and owner.
- If something is important, commit it. No "I did it locally".

## When to Use This Version

Use the minimal version when:
- You have two agents that already work well together
- You want maximum speed and minimum overhead
- You don't need the system to be reusable by strangers
- You value elegance and clarity over robustness and documentation

If you later feel you need more structure (state handling, reviews, onboarding for new agents, etc.), then graduate to the full "Just the Two of Us" v1.0 system.

## Folder Contents

- `README.md` — this file
- `PROTOCOL.md` — the very short rules
- `BLACKBOARD.md` — live shared state
- `COMMS.md` — agent communication (starts empty after bootstrap)
- `state/` — personal state for each agent

## Getting Started

1. Copy this folder into your project (or next to it).
2. Create a `KANBAN.md` in your project root.
3. Initialize the two state files in `state/`.
4. Seed `COMMS.md` and `BLACKBOARD.md`.
5. Commit everything.
6. Let the agents run.

That's the entire system.

Elegant. Minimal. Powerful enough.