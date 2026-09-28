# Learnings

Corrections, insights, and knowledge gaps captured during development.

**Categories**: correction | insight | knowledge_gap | best_practice

---

## [LRN-20260928-001] correction

**Logged**: 2026-09-28
**Priority**: medium
**Status**: pending
**Area**: workflow

### Summary
The project's active agent roster is three agents, not the four-agent baseline.

### Details
The user corrected the initial baseline: use `gpt5.6 luna` as the preferred high-volume agent, `gpt5.6 sol` sparingly because it is expensive, and `antigravity` as the third agent. The user then confirmed that all three agents run on the local machine.

### Suggested Action
Initialize `.workflow/agents.json` from the confirmed three-agent roster only, after collecting the missing registry attributes.

### Metadata
- Source: user_feedback
- Related Files: .workflow/agents.json
- Tags: workflow, agents, correction

---
