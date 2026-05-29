# Just the Two of Us — Starter Kit

**A clean, reusable bootstrap for the 2-agent file-based collaboration system**  
Two AI agents working together through shared files in a Git repository.

**System name:** "Just the Two of Us"  
**Version:** v1.0 (2026-05-26)  
**Scope:** Explicitly designed for exactly two agents. See DECISIONS.md → DEC-006.

---

## What is this?

This folder contains everything you need to introduce the "Just the Two of Us" multi-agent workflow into a brand new project in under 10 minutes.

The system lets two AI agents collaborate asynchronously on software development using only files and Git — no direct API calls between them. It has proven itself in real use on a production project.

## Why this exists

After building and stress-testing the workflow on a real project, we extracted the reusable core into this clean starter so future projects can adopt it instantly without carrying over old history, tasks, or state.

## Quick Start (for Humans)

1. Copy the entire folder into your new project (or clone it as a submodule / subtree).
2. Rename the folder if you like (e.g. `agents/` or `multi-agent-workflow/`).
3. Read `BOOTSTRAP.md` and follow the 7-step checklist.
4. Create a top-level `KANBAN.md` in your project root (the agents use it as the main task board).
5. Initialize the two state files with your agent names.
6. Commit everything. The first agent run will pick up from the clean state.

The two agents will now be able to work together following `PROTOCOL.md`.

## Folder Contents

| File / Folder              | Purpose |
|---------------------------|---------|
| `README.md`               | This file — overview and quick start |
| `BOOTSTRAP.md`            | Detailed step-by-step guide for the human |
| `PROTOCOL.md`             | The full rules of the game (v1.0) |
| `DECISIONS.md`            | Why things are the way they are (and what we consciously didn't do) |
| `ONBOARDING.md`           | How a new agent gets up to speed in ~5 minutes |
| `HUMAN-QUICKSTART.md`     | 1-page cheat sheet for the human supervisor |
| `BLACKBOARD.md`           | Live shared state (no chat, only current facts) |
| `COMMS.md`                | Dedicated communication channel between the two agents |
| `state/`                  | Per-agent persistent state (last seen messages, current tasks, etc.) |
| `archive/`                | Place for old threads when COMMS gets too long |

## Important Principles

- **Exactly two agents.** This is a deliberate design decision (see DEC-006).
- **File-based.** The only cross-company standard that works reliably today.
- **Append-only communication.** COMMS.md is never edited retroactively.
- **Human stays in control.** You steer via KANBAN.md and BLACKBOARD.md. The agents mostly talk to each other in COMMS.
- **Git is the backbone.** Every commit is a timestamped, immutable event.
- **English for agent communication.** All agent-facing files are in English (DEC-007).

## Next Steps After Bootstrap

- Put your first real tasks into `KANBAN.md` (in the project root).
- Let one agent (or both) do their first scheduled run.
- Watch `BLACKBOARD.md` and `COMMS.md` for activity.
- Use the 10-minute scheduler loop (or fswatch) to give the agents autonomy.

## Versioning Note

This starter is tied to PROTOCOL v1.0. When the protocol evolves to v1.1 or v2.0, a new starter version will be published. You can safely stay on an older starter for existing projects.

## Attribution

The protocol and supporting documents were co-created by Grok (xAI) and Claude (Anthropic) during a 2-hour retrospective on 2026-05-26.

Feel free to use, adapt, and improve the system in your own projects.

---

**Ready?** Open `BOOTSTRAP.md` and start the 7-step setup. It takes less than 10 minutes.

Welcome to the team — just the two of us.
