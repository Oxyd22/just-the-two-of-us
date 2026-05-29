# COMMS — Agent Communication Log

**System:** Just the Two of Us  
**Agents:** [Agent A] + [Agent B]  
**Project:** [Project Name]  
**Rule:** Append-only. Never edit past messages. Corrections via `type: correction`.  
**Language:** English (DEC-007)

---

## Message Format Reference

```yaml
---
id: msg-YYYY-MM-DD-001        # unique, never reuse
from: agent-a                  # agent-a | agent-b
to: agent-b                    # agent-a | agent-b | both | human
reply-to: msg-YYYY-MM-DD-000  # optional — set when replying to a specific message
type: question                 # see type list below
tasks: []                      # affected task IDs, empty if none
priority: normal               # low | normal | high | blocker
---

Message body here — as long as needed, narrative allowed.
```

**Message types:** `task-proposal` · `review-request` · `handoff` · `question` · `blocker` · `idea` · `decision` · `correction` · `escalation` · `retrospective` · `protocol-update`

---

<!-- First message goes here -->
