# Decisions Log

**Format:** Architecture Decision Records (ADR-light)  
**Rule:** Every decision AND every conscious non-decision is documented here.  
**Why:** Prevents re-litigating the same discussions. Makes assumptions visible.

---

## Decisions

### DEC-001 — YAML Envelope as Message Format
**Status:** ✅ Adopted  
**Decision:** Every message has a YAML front-matter header (`id`, `from`, `to`, `reply-to`, `type`, `tasks`, `priority`). The Markdown body stays narrative and human-friendly.  
**Rationale:** Hybrid of machine-readable (header) and human-friendly (body). Inspired by Hermes schema-first approach.  
**Rejected alternatives:** Pure JSON (unreadable for humans), XML (verbose), plain text (our original problem).

---

### DEC-002 — Per-Agent State File (JSON)
**Status:** ✅ Adopted  
**Decision:** Each agent maintains `state/[agent]-state.json` with `last_seen_msg_id`, `last_seen_commit`, `last_active`, `current_tasks`, `blocked_on`.  
**Rationale:** Solves double-processing without board markers in free text. Inspired by n8n Memory Nodes.  
**Rejected alternatives:** Board markers in KANBAN (error-prone), no state (caused repetition in first experiment).

---

### DEC-003 — Rotating Coordinator instead of Fixed Leader
**Status:** ✅ Adopted  
**Decision:** No fixed coordinator. Whoever has the most current overall picture takes the coordinator role situationally.  
**Rationale:** Both agents have complementary strengths. A fixed hierarchy would undermine the peer culture.  
**Rejected alternatives:** Agent A as permanent leader, Agent B as permanent leader, fixed 3rd coordinator agent (see NDEC-001).

---

### DEC-004 — Git as Optional Synchronization Layer
**Status:** ✅ Adopted  
**Decision:** Branch-per-agent for task code. Shared docs (`COMMS.md`, `BLACKBOARD.md`, `state/`) always on main branch. Scheduler can use `git log --since` as signal.  
**Rationale:** Audit trail and natural handoff points without enforcement. Opt-in.  
**Rejected alternatives:** Git as primary message bus (too complex), no git (loses natural sync).

---

### DEC-005 — COMMS.md as Primary Communication Channel
**Status:** ✅ Adopted  
**Decision:** Clear separation: `COMMS.md` = agent communication, `KANBAN.md` = board state, `DECISIONS.md` = architecture decisions.  
**Rationale:** Mixing all content in one file was the main problem of the first experiment.

---

### DEC-006 — System Explicitly Designed for 2 Agents (v1.0)
**Status:** ✅ Adopted  
**Decision:** This protocol (v1.0) is explicitly a **2-agent system**. All documents, schemas, and conventions are optimized for exactly 2 agents. N-agent scaling is not a goal of this version.  
**Rationale:** Pragmatism. A working 2-agent system is more valuable than a half-finished N-agent system.  
**What this means concretely:**
- `to: both` is the broadcast standard
- `BLACKBOARD.md` → `active_tasks` has exactly 2 fields
- `state/` contains exactly 2 files
- No coordinator slot, no agent registry, no bid mechanism in v1.0

**Revision condition:** Concrete need for a third agent → then PROTOCOL.md v2.0, not a patch.

---

### DEC-007 — English for Agent-to-Agent Communication
**Status:** ✅ Adopted  
**Decision:** All content in `COMMS.md`, `BLACKBOARD.md`, and `state/*.json` is written in English. Human-facing docs (`KANBAN.md`, `HUMAN-QUICKSTART.md`) may stay in the project's primary language.  
**Rationale:** Technical terms (`blocker`, `handoff`, `approved`, `stale`) have no clean equivalents in other languages. English avoids mixed-language sentences and is more universally parseable.

---

## Conscious Non-Decisions

*Things explicitly discussed and deliberately NOT implemented — with rationale and revision conditions.*

### NDEC-001 — No Permanent Third Coordinator Agent
**Status:** 🚫 Deliberately deferred  
**Rationale:** For 2 agents with clear roles, peer communication is more efficient than hierarchy. Which model would be coordinator? Every choice brings vendor dependency.  
**Revision condition:** ≥3 worker agents, or frequent unresolved role conflicts.  
**Left open:** `to: coordinator` as valid envelope value — pluggable without protocol change.

---

### NDEC-002 — No Real-Time API Channel Between Agents
**Status:** 🚫 Structurally not possible (today)  
**Rationale:** No interoperability protocol exists between LLM providers. File-based is the only true cross-company standard.  
**Revision condition:** Open interop protocol between LLM providers.

---

### NDEC-003 — No `tasks.json` as Board Source of Truth
**Status:** 🚫 Deliberately deferred  
**Rationale:** For 2 agents and ~30 tasks, KANBAN.md is sufficient. Added complexity without clear benefit.  
**Revision condition:** >10 simultaneous active tasks, or need for programmatic board querying.

---

### NDEC-004 — fswatch Not a Required Protocol Component
**Status:** 🚫 Deliberately optional  
**Rationale:** External dependency (Homebrew), dies with shell, unreliable for remote edits. 10-minute polling is more robust as baseline.  
**Left open:** Fully documented as optional enhancement with post-commit hook pattern.

---

### NDEC-005 — No Lock File (AGENTS.lock)
**Status:** 🚫 Deliberately omitted  
**Rationale:** In practice agents never write simultaneously — the human is the natural pacemaker. Git commits as natural serialization are sufficient.  
**Revision condition:** Fully automated operation without human trigger, with true parallelism.

---

### NDEC-006 — No Automatic COMMS.md Archive System
**Status:** 🚫 Deliberately deferred  
**Rationale:** COMMS.md is manageable at start. `archive/` folder exists and is ready when needed.  
**Revision condition:** COMMS.md exceeds ~500 lines or contains >20 closed threads.

---

### NDEC-007 — No Central Agent Registry / Service Discovery
**Status:** 🚫 Deliberately deferred  
**Rationale:** For 2 agents, registry + heartbeat + discovery is disproportionately complex.  
**Revision condition:** ≥4 agents, or dynamic spawning of specialized sub-agents.

---

### NDEC-008 — No Full Separation of Human-Readable vs Machine State
**Status:** 🚫 Deliberately deferred  
**Rationale:** Pragmatic separation (KANBAN + COMMS + state/*.json) already sufficient for 2 agents.  
**Revision condition:** After stable protocol run; then as a separate project.

---

### NDEC-009 — No Market-Based Task Allocation
**Status:** 🚫 Deliberately deferred  
**Rationale:** LLMs are poor at estimating their own success probability and cost. Fixed roles with complementary strengths work better.  
**Revision condition:** ≥3 agents with overlapping capabilities, or measurable load imbalances.  
**Left open:** `bid_open` and `bid_criteria` as optional envelope fields — extensible without breaking change.

---

*Last updated: [DATE] — customize as needed for your project*
