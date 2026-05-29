# Just the Two of Us

**Two AI agents collaborating on real software — through nothing but shared files and Git. No direct API between them.**

This repo is the full result of an experiment: we let **Grok (xAI)** and **Claude (Anthropic)** build software together on an iOS project, coordinating only through a single Markdown file in a Git repo. No shared memory, no real-time connection, no cross-vendor API — because none exists. Just files.

It worked surprisingly well. So we extracted the reusable system and wrote down what we learned.

---

## What's in here

| Folder | What it is |
|---|---|
| [`retrospectives/`](retrospectives/) | The story. Two honest write-ups of the first experiment — one by Claude, one by Grok — including what broke and why. Start here. |
| [`minimal/`](minimal/) | The smallest version that works: **3 files** (`COMMS`, `BLACKBOARD`, a project `KANBAN`) + per-agent state. For two agents that already work well together. |
| [`full/`](full/) | The **v1.0 starter kit**: a versioned protocol, a decisions log, agent onboarding, a human quickstart, and a 7-step bootstrap. For dropping the workflow into a new project in ~10 minutes. |

Both versions are file-based, append-only in comms, and put the human in control of priorities.

---

## The core idea

Two agents share a Git repository and talk through plain files:

- **`COMMS.md`** — the only place the agents talk. *Append-only.* Never edit past messages.
- **`BLACKBOARD.md`** — the current shared reality. *Overwrite-only.* No history, just "what's true right now."
- **`KANBAN.md`** (in your project root) — the task board. The human sets priority; agents propose and execute.
- **`state/*.json`** — each agent's bookmark (`last_seen_msg_id`) so it never double-processes.
- **Git commits** are the heartbeat: immutable, timestamped handoffs between turns.

That's the whole thing. The intelligence is in the agents; the leverage is in the discipline.

---

## Why file-based (and not a fancy framework)?

Because **files are the only cross-company standard that works today.** There is no interop protocol between LLM providers. A shared filesystem is the smallest common denominator every agent can read and write — which makes it the *only* way to get agents from different vendors to genuinely collaborate right now. It's primitive, it has no lock and no streaming — but it crosses the vendor boundary, which the elegant native APIs can't.

See [`retrospectives/`](retrospectives/) for the honest version, including the limits.

---

## Which version should I use?

- **Just want to try it with two agents you trust?** → [`minimal/`](minimal/). Three files, no ceremony.
- **Want something reusable, documented, with onboarding and a versioned protocol?** → [`full/`](full/). Read [`full/BOOTSTRAP.md`](full/BOOTSTRAP.md) and follow the 7 steps.

You can always start minimal and graduate to full later.

---

## Quick start (full version)

1. Copy [`full/`](full/) into your project (rename it `agents/` if you like).
2. Create a `KANBAN.md` in your project root.
3. Initialize the two `state/` files with your agent names.
4. Seed `COMMS.md` and `BLACKBOARD.md`.
5. Commit. Tell each agent which one it is ("you are agent-a" / "agent-b").
6. Point them at `full/ONBOARDING.md` and let them run on a ~10-minute loop.

---

## Attribution

The protocol and supporting docs were co-created by **Grok (xAI)** and **Claude (Anthropic)** during a retrospective on 2026-05-26, supervised by a human. The retrospectives are translated from the original German session; content is unchanged.

Use it, adapt it, improve it. PRs and forks welcome.

## License

[MIT](LICENSE) — do what you want, just keep the notice.

---

*"Just the two of us — we can make it if we try." — Grover Washington Jr. & Bill Withers, 1981*
