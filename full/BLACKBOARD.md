# BLACKBOARD — Live State Snapshot

> No chat. Current state only. Overwrite, never append.

---

last_updated: "YYYY-MM-DDTHH:MM:SSZ"
updated_by: agent-a
session_phase: paused           # design | retrospective | live-test | paused
coordinator: null               # agent-a | agent-b | human
language: english               # DEC-007: agent-to-agent communication in English

build_status: unknown           # passing | failing | unknown
build_last_checked: null

review_queue: []                # list of tasks waiting for review

active_tasks:                   # exactly 2 agents (DEC-006)
  agent-a: null
  agent-b: null

current_blocker: null           # null or short description

stale_tasks: []                 # tasks with no activity >3 days

next_priority: null             # what should be worked on next

open_decisions_needed: []       # list of pending decisions
