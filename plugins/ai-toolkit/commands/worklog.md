---
tags: ["workflow", "collaboration", "documentation"]
description: "Add timestamped work log entries to track manual changes and communicate with AI"
argument-hint: "\"your comment text\""
allowed-tools: ["Read", "Write", "Edit", "Grep", "Glob"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/worklog-format.md  # WORKLOG format and work documentation standards
---

# /worklog Command

**WHAT**: Add timestamped work log entries to track manual changes and communicate with AI.

**WHY**: Enable AI to understand manual work, avoid duplicating human effort, and maintain shared context.

**HOW**: See worklog-format.md for format standards. AI timestamps entry, finds WORKLOG, prepends in reverse chronological order.

## Usage

```bash
/worklog "Added login button to header"
/worklog "Fixed dark mode - using --color-grey-dark (#2d2d2d)"
/worklog "Don't use jsonwebtoken - jose has better TS support"
```

## How It Works

AI executes this workflow:

1. **Get timestamp**: Run `date '+%Y-%m-%d %H:%M'` (NEVER guess/estimate)
2. **Get username**: Run `git config user.name`
3. **Find WORKLOG**: Locate current task's WORKLOG.md in `pm/issues/TASK-###-*/`
4. **Prepend entry** (reverse chronological):
   ```markdown
   ## 2025-10-22 15:30 - @username
   Added login button to header
   ```
5. **Analyze impact**: Read TASK.md and check if comment relates to existing phases
6. **Offer update** (interactive): Ask if task plan needs updating based on comment
7. **Update if confirmed**: Modify TASK.md and log the change in WORKLOG.md

## When to Use

✅ **Use when:**
- Making manual code changes outside `/implement`
- Documenting gotchas or lessons learned
- Communicating constraints to AI ("must use library X")

❌ **Don't use when:**
- AI agents did the work (they auto-log)
- No changes made (just reading code)
- Already documented in commit message

## WORKLOG Format

```markdown
# Work Log - TASK-001: User Authentication

## 2025-10-22 15:30 - @taylor
Added login button with dark mode support.
Files: src/components/Header.tsx

## 2025-10-22 14:30 - backend-specialist
Implemented JWT middleware with refresh logic.
Gotcha: Token expiry configurable via TOKEN_EXPIRY_HOURS.
Files: src/middleware/auth.js
```

**Reverse chronological** (newest first) for quick context scanning.

## Interactive Plan Updates

After adding comment, AI analyzes task plan:

```
Your comment mentions login button. This might relate to:
- [ ] 2.1 Implement login UI components

Update task plan?
  1. Mark phase 2.1 complete
  2. Add new phase for login work
  3. No update needed

Choose (1/2/3): _
```

## Examples

**Styling work:**
```bash
/worklog "Tweaked button padding to 12px/24px for mobile"
# → Added to WORKLOG, no plan updates needed
```

**Feature addition:**
```bash
/worklog "Added email validation to login form"
# → Added to WORKLOG, AI asks: "Mark phase 1.3 complete? (y/n)"
```

**Gotcha documentation:**
```bash
/worklog "Don't use setTimeout for token refresh - use setInterval"
# → Added to WORKLOG, documented for future reference
```

**API change:**
```bash
/worklog "API changed - login endpoint now /api/v2/auth/login"
# → Added to WORKLOG, AI asks: "Update phase 3.1 description? (y/n)"
```

## Error Handling

**No active task**: AI lists available tasks and asks which one to associate comment with

**No WORKLOG.md**: AI creates it automatically with proper header

## Benefits

**For AI**: Understands manual changes, avoids duplicating human work, respects decisions
**For Humans**: Quick documentation, no context switching, AI keeps plan synchronized
**For Teams**: Shared history, captured gotchas, implementation timeline

## Philosophy

Human-AI collaboration through shared work log:
- Humans add comments for manual work
- AI agents add entries for automated work
- Both contribute to narrative history
- Result: AI remembers context, avoids breaking existing features
