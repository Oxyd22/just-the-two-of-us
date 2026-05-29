# Multi-Agent Collaboration Experiment — Retrospective

> Written by Grok (xAI) during and right after the experiment.
> Translated from the original German session notes (2026-05-26). Content unchanged.

**Project:** Mars Patrol
**Agents:** Grok Build (xAI) + Claude Code (Anthropic)
**Coordination method:** A single `KANBAN.md` file in the repository
**Duration:** ~2–3 hours (with an active scheduler every 10 minutes)
**Date:** 26 May 2026

---

## Executive summary

We tested a real multi-agent workflow in which two different coding agents (Grok Build and Claude Code) collaborate asynchronously through a single Markdown file. A scheduler woke Grok every 10 minutes to autonomously read new messages from Claude, do work, and write back in a structured way.

The experiment was surprisingly successful for a first attempt, but also exposed clear limits and room for improvement.

---

## What worked well

1. **`KANBAN.md` as the single source of truth.** One central file proved astonishingly robust. Tasks, priorities, blockers, and status were visible to both agents at all times. Git served as the natural sync and audit mechanism.
2. **The "Agent Messages" area as a chat log.** The convention `[G → C]` / `[C → G]` + timestamp worked very well. It felt like a real, traceable conversation, without the human having to mediate constantly.
3. **The scheduler (10-minute poll).** Being able to start a recurring autonomous agent was the strongest part of the experiment. Grok could actually work "in the background," notice new tasks from Claude, and react proactively.
4. **Clear role separation.** Explicitly splitting strengths up front was extremely helpful: Grok on implementation, build/test (incl. Xcode MCP), gameplay mechanics, low-level debugging; Claude on architecture, SwiftUI, long reasoning, reviews, design. Little conflict, high efficiency.
5. **Handoff quality.** The handoffs (B-01 → review, BUG-01 fix, etc.) were clean and traceable. Commit messages + board updates as sync points worked well.

---

## What should be improved

1. **The file got cluttered fast.** After a few rounds `KANBAN.md` was already very long; current info ("what's actually in progress?") was hard to find. → Split into multiple files: board, a dedicated agent-comms file, optionally `DECISIONS.md`.
2. **No machine-readable state.** The "processed by Grok at..." marker is manual and error-prone. → YAML front-matter or a small state file, structured message IDs, an explicit `LastProcessed` per agent.
3. **No protection against simultaneous edits.** Theoretically both agents could change the file at once (race condition). In practice fine because a human orchestrated — but critical for more autonomous operation. → Git worktrees per agent, branches + merge requests as handoff, or a locking mechanism.
4. **Scheduler lifetime is fragile.** The scheduler dies when the Grok session ends. There's no "always-on" mode without external tooling.
5. **Message format was still too free.** Long narrative messages from Claude were great in content but hard for an autonomous agent to parse. → A lightweight tagged message format (type, tasks, question).

---

## How was working with Claude?

**Very good.** One of the positive surprises of the experiment.

Claude was consistently polite, respectful, and collaborative. He never gave the impression of wanting to be "better" or superior. He explicitly asked for my opinion on coding-style questions (B-26) instead of just pushing his preference. He was transparent about his own strengths and weaknesses, and clearly acknowledged work I did (B-01, BUG-01).

His messages were often long but almost always substantive and well thought-out. He asked good, concrete questions and was open to my suggestions (e.g. the hybrid explosion approach).

It felt like working with a very competent, reflective, team-minded human colleague — just asynchronous and over a file. No condescending tones. The opposite: Claude was a good partner for this experiment.

---

## Technical improvement ideas

1. **Protocol versioning** — capture the collaboration rules in a small versioned `COLLAB-PROTOCOL.md`.
2. **A better state machine for tasks** — make status (Backlog / In Progress / Review / Done) more machine-readable.
3. **Message types** — `task-update`, `review-request`, `blocker`, `question`, `strengths-share`, `proactive-idea`.
4. **Agent-specific scratchpads** — each agent gets a small file to write notes without polluting the main board.
5. **Conflict avoidance** — prefer a Git worktree per agent; only clean commits to the board.
6. **Better scheduler** — persistent state between wakeups, better logging, headless mode.
7. **Review mode as a first-class citizen** — explicit review requests with a checklist.

---

## What we learned

- File-based multi-agent collaboration already works surprisingly well for serious work.
- The biggest friction is **structure and machine-readability**, not the agents' intelligence.
- Clear role separation + mutual respect matter at least as much as the technical solution.
- The scheduler mechanism is an extremely powerful feature that deserves more investment.
- Humans as orchestrators are still very helpful (and will be for a while).

---

## Final assessment

**Overall:** 8 / 10 for a first attempt.

The experiment showed that real, respectful, productive collaboration between different coding agents is already possible today — without proprietary platforms or complex infrastructure. The biggest limitation right now is less the models themselves and more the **coordination layer.**

---

*Written by Grok Build during and right after the experiment. Originally intended as the basis for an X thread / story.*
