# Errors

Command failures and integration errors.

---

## [ERR-20260928-001] browser_navigate

**Logged**: 2026-09-28T00:00:00Z
**Priority**: medium
**Status**: pending
**Area**: infra

### Summary
Browser automation exited before opening the shared conversation URL.

### Error
```
浏览器进程提前退出（exit code: 0）。若独立 profile 已被其他实例占用，请先关闭该实例。
```

### Context
- Attempted to navigate to the user-provided shared project URL.
- Will retry once and use a non-browser fallback if necessary.
- No secrets or session data recorded.

---

## [ERR-20260928-002] curl_shared_url

**Logged**: 2026-09-28T00:00:00Z
**Priority**: medium
**Status**: pending
**Area**: infra

### Summary
Direct HTTP request to the shared conversation URL timed out after 30 seconds.

### Error
```
curl: (28) Connection timed out after 30000 milliseconds
```

### Context
- Attempted as a fallback after browser automation exited before navigation.
- The shared conversation contents could not be retrieved through the local network path.
- No secrets or session data recorded.

---

## [ERR-20260928-003] edit_learnings_log

**Logged**: 2026-09-28T00:00:00Z
**Priority**: low
**Status**: pending
**Area**: config

### Summary
Initial append to the learning error log used a non-unique delimiter and was rejected.

### Error
```
old_string matched 2 locations in .learnings/ERRORS.md
```

### Context
- Retried with a unique surrounding block and the log update succeeded.
- This confirms append edits should include enough surrounding context when the delimiter repeats.

---

## [ERR-20260928-004] cua_browser_prepare

**Logged**: 2026-09-28T00:00:00Z
**Priority**: medium
**Status**: pending
**Area**: infra

### Summary
The browser driver could not launch an isolated Chromium instance because no supported signed executable was available.

### Error
```
refused (browser_route_unavailable): no vendor-signed protected Chromium executable is available for isolated launch
```

### Context
- Attempted as a fallback after the standard Browser tool exited.
- No browser target or tab was created.

---

## [ERR-20260928-005] bundle_inspection_shell_quote

**Logged**: 2026-09-28T00:00:00Z
**Priority**: low
**Status**: pending
**Area**: infra

### Summary
A shell command intended to inspect the remote JavaScript bundle failed because nested quoting was parsed by the shell.

### Error
```
/usr/bin/bash: line 1: /api/[^"': No such file or directory
```

### Context
- The remote HTML was reachable over HTTP, but the regex command did not execute as intended.
- No workspace files were changed by the failed command.

---

## [ERR-20260928-006] validate_agents_json

**Logged**: 2026-09-28
**Priority**: medium
**Status**: resolved
**Area**: config

### Summary
A malformed edit temporarily corrupted `.workflow/agents.json` while changing the Antigravity host.

### Error
```
Expecting ',' delimiter: line 54 column 42 (char 1171)
```

### Context
- The edit inserted stray text into the description line.
- The file was immediately rewritten from the intended complete JSON and is valid again.

---

## [ERR-20260928-007] memory_list_limit

**Logged**: 2026-09-28
**Priority**: low
**Status**: resolved
**Area**: workflow

### Summary
The first memory index lookup used a limit above the tool's maximum.

### Error
```
limit: must be <= 32
```

### Context
- Retried with `limit: 32` and retrieved the current memory index successfully.

---

## [ERR-20260928-009] git_status_safe_directory

**Logged**: 2026-09-28
**Priority**: low
**Status**: pending
**Area**: config

### Summary
A read-only Git status check was blocked by Git's dubious ownership guard.

### Error
```
fatal: detected dubious ownership in repository at 'C:/Users/Windows/Desktop/Task/ongoing/BetterLiveAgent'
```

### Context
- The handoff summary was written successfully before the status check.
- No global Git safe-directory setting was changed.

---

## [ERR-20260928-008] grill_question_limit

**Logged**: 2026-09-28
**Priority**: low
**Status**: resolved
**Area**: workflow

### Summary
The first grill question batch exceeded the AskUserQuestion limit.

### Error
```
AskUserQuestion supports at most 4 questions per call; got 5.
```

### Context
- The batch was regrouped into four questions before retrying.

---

## [ERR-20260928-009] grill_question_limit

**Logged**: 2026-09-28
**Priority**: low
**Status**: pending
**Area**: workflow

### Summary
A later grill question batch again exceeded the AskUserQuestion limit.

### Error
```
AskUserQuestion supports at most 4 questions per call; got 5.
```

### Context
- The launch-policy question will be deferred to the next round or folded into the lifecycle question.

---
