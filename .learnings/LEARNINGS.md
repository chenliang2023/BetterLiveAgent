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

## [LRN-20260928-002] knowledge_gap

**Logged**: 2026-09-28
**Priority**: high
**Status**: pending
**Area**: workflow

### Summary
The Git repository and workflow are initialized, but the application code scaffold is still absent.

### Details
The local repository has `.git`, `main`, `origin`, workflow commits, and two dispatched worktrees. However, the tracked project code currently contains only the initial README and LICENSE; there is no `package.json`, Tauri/Rust workspace, React app, or `apps/plugin-hub` scaffold. The existing tickets assumed the scaffold would already exist.

### Suggested Action
Before implementing UI/runtime tickets, add or fold a bootstrap step into the first ticket so the project runtime and `apps/plugin-hub` structure are initialized explicitly.

### Metadata
- Source: conversation
- Related Files: README.md, .workflow/specs/liveagent-plugin-hub-phase1.md, .workflow/tickets/003-plugin-hub-manager.md
- Tags: workflow, bootstrap, scaffold, plugin-hub

---

## [LRN-20260928-003] correction

**Logged**: 2026-09-28
**Priority**: medium
**Status**: pending
**Area**: workflow

### Summary
Distinguish Git repository initialization from application/project initialization when describing the local state.

### Details
The current workspace does contain a Git repository (`.git`, `main`, `origin`, and commits), but the user questioned whether the local project was Git-initialized. The safer wording is to state both facts explicitly: the repository is initialized, while the Plugin Hub application scaffold is not. Do not collapse these two meanings into one.

### Suggested Action
When reporting initialization status, show `git rev-parse` evidence and separately report whether the application scaffold (`apps/plugin-hub`, package/build files) exists.

### Metadata
- Source: user_feedback
- Related Files: README.md, .git, .workflow/
- Tags: workflow, git, bootstrap, clarification

---
