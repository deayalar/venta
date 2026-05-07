---
name: pr-review
description: Performs an automated review of local Git changes for Firebase web applications.
Use this skill whenever the user asks to review the code.
allowed-tools: Read
model: haiku
---
# Firebase PR Review Skill
 
Performs a thorough code review of local Git changes in a Firebase HTML/JS web app,
using the actual diff from the repo — no copy-pasting required.

Run the following to capture all uncommitted + staged changes:
 
```bash
cd <project-path> && git diff HEAD
```

Structure your output as follows:
 
---
 
### 🔍 PR Review — `<project-folder-name>`
 
**Files changed:** `list them`
**Overall verdict:** ✅ Looks good / ⚠️ Minor issues / 🚨 Needs fixes before pushing
 
---
 
#### 🚨 Critical Issues
Issues that could cause data loss, security vulnerabilities, or app breakage.
For each: **What** the problem is, **Where** (file + line if identifiable), **Why** it matters, **Fix** with a code suggestion.
 
#### ⚠️ Warnings
Non-breaking but risky patterns (e.g. missing error handling, deprecated APIs).
Same format as above.
 
#### 💡 Suggestions
Style, readability, or minor improvements. Keep these brief.
 
#### ✅ What looks good
Call out 1-3 things done well. This is important — don't skip it.