---
# === Metadata ===
template_type: "guideline"
version: "1.0.0"
created: "2025-11-05"
last_updated: "2025-11-05"
status: "Active"
target_audience: ["AI Assistants", "Developers"]
description: "WORKLOG entry formats for standard workflow and troubleshooting contexts"

# === Configuration ===
worklog_ordering: "reverse_chronological"    # Newest entries at TOP
entry_philosophy: "stream_not_summarize"     # Write at handoffs, not after completion
entry_length: "500_chars_ideal"              # ~5-10 lines per entry
---

# WORKLOG Format Guidelines

**Purpose**: Document work flow between agents and across workflows with brief, scannable entries

**Philosophy**: Stream, don't summarize. Write entries as work happens (cross-agent handoffs), not retrospective summaries after phases complete.

**Entry Ordering**: **CRITICAL** - Always maintain **reverse chronological order** (newest entries at the TOP).

## Quick Reference

**Two Format Types**:
1. **Standard Format** - For implementation work, agent handoffs, phase completion
2. **Troubleshooting Format** - For debug loops with hypothesis tracking

**When to write**:
- ✅ Completing work and handing off to another agent
- ✅ Receiving work back with changes needed
- ✅ Completing a phase or subtask
- ✅ Each troubleshooting loop iteration
- ❌ Don't write "STARTED" entries (waste - just do the work)

**Key principle**: Newest entries at TOP, brief (~500 chars), focus on insights

---

## Standard WORKLOG Format

**Use for**: Implementation work (`/implement`), agent handoffs, phase completion, general development

### Entry Types

#### HANDOFF Entry (passing work to another agent)

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] → [NEXT: next-agent]

Brief summary of what was done (5-10 lines max).

Gotcha: [critical issues encountered, if any]
Lesson: [key insights, if any]
Files: [key/files/changed.js]

→ Passing to {next-agent} for {reason}
```

#### COMPLETE Entry (phase fully done, no more handoffs)

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] (Phase X.Y COMPLETE)

Phase complete summary (5-10 lines).

Status:
- ✅ Tests passing
- ✅ Quality gates met
- ✅ PLAN.md updated

Files: [key/files/changed.js]
```

### Required Elements

- **Timestamp**: Always run `date '+%Y-%m-%d %H:%M'` - never estimate
- **Agent identifier**: Name of the agent (or @username for humans via `/comment`)
- **Arrow notation**: Use `→` for handoffs to show work flow
- **Brief summary**: What YOU did (not entire phase history) - keep scannable
- **Gotchas/Lessons**: Only if significant (don't force it)
- **Files**: Key files modified (helps locate changes via diff)
- **Handoff note**: Who receives work and why (for handoffs only)

### Standard Format Examples

**Handoff entry**:
```markdown
## 2025-01-15 14:30 - [AUTHOR: backend-specialist] → [NEXT: code-reviewer]

Implemented JWT auth endpoint with bcrypt hashing (12 rounds) and Redis token storage.

Gotcha: Redis connection pooling required - single connection bottleneck
Files: src/auth/login.ts, src/middleware/jwt.ts, tests/auth.test.ts

→ Passing to code-reviewer for security validation
```

**Complete entry**:
```markdown
## 2025-01-15 15:35 - [AUTHOR: code-reviewer] (Phase 2.3 COMPLETE)

Re-review approved (score: 94/100). All security issues resolved.

Status:
- ✅ Tests passing (48/48)
- ✅ Security validated
- ✅ PLAN.md checkbox updated
```

**Human comment entry** (via `/comment`):
```markdown
## 2025-01-15 10:15 - [AUTHOR: @alice]

Disabled middleware auth check temporarily - was blocking sign-up flow. Cookie name mismatch.

Files: src/middleware.ts (disabled check on line 42)

→ Need to fix cookie naming before re-enabling
```

---

## Troubleshooting WORKLOG Format

**Use for**: Debug loops (`/troubleshoot`), hypothesis testing, issue investigation

**Key difference**: Structured format tracks hypothesis → findings → result

### Troubleshooting Entry Template

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] (Troubleshooting Loop N)

Hypothesis: [Theory based on research - brief]
Debug findings: [Key insights from debug logs]
Implementation: [What was changed - files and approach]
Research: [Docs/examples referenced]
Result: ✅ Fixed / ❌ Not fixed - [evidence]

Files: src/file.ts (added debug statements)
Tests: X/X passing
Manual verification: [User confirmed / Awaiting confirmation]

→ [If not fixed: Next research direction]
```

### Troubleshooting Format Variants

#### If Hypothesis FAILED

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] (Loop N - Failed)

Hypothesis: [Theory that didn't work]
Debug findings: [What logs revealed that disproved hypothesis]
Implementation: [What was tried]
Result: ❌ Not fixed - [why it failed, what was learned]
Rollback: ✅ Changes reverted

Files: (all changes reverted)
Next: [Alternative approach based on debug findings]
```

#### If Hypothesis SUCCEEDED

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] (Loop N - Success)

Hypothesis: [Theory that worked]
Debug findings: [Key logs that confirmed fix]
Implementation: [Final changes made]
Research: [Docs that led to solution]
Result: ✅ Fixed - [test results and user confirmation]

Files: src/component.ts, tests/component.test.ts
Tests: 245/245 passing
Manual verification: User confirmed working
Debug cleanup: [Kept as comments / Removed / Converted to logger]
```

### Troubleshooting Format Examples

**Loop 1 - Failed**:
```markdown
## 2025-01-16 15:30 - [AUTHOR: backend-specialist] (Loop 1 - Failed)

Hypothesis: Query runs before auth completes (based on Convex Auth docs)
Debug findings: Logs show isLoading=false before query, but userId still null
Implementation: Added isLoading gate to skip query during auth
Result: ❌ Not fixed - User still sees "Not signed in" after login
Rollback: ✅ Changes reverted

Files: (all changes reverted)
Next: Research getAuthUserId() vs ctx.auth.getUserIdentity() API difference
```

**Loop 2 - Success**:
```markdown
## 2025-01-16 16:15 - [AUTHOR: backend-specialist] (Loop 2 - Success)

Hypothesis: Using wrong auth API (should use getAuthUserId helper)
Debug findings: Logs confirmed getUserIdentity returns null, getAuthUserId works
Implementation: Replaced ctx.auth.getUserIdentity() with getAuthUserId(ctx)
Research: @convex-dev/auth/server documentation
Result: ✅ Fixed - Login works, profile displays correctly

Files: convex/users.ts
Tests: 245/245 passing
Manual verification: User confirmed working in browser
Debug cleanup: Kept as comments for future reference
```

---

## WORKLOG Best Practices

**Apply to both standard and troubleshooting formats**:

1. **Keep entries scannable**: ~500 chars is ideal, can be longer for critical gotchas
2. **Focus on insights**: Document WHY things were done certain ways, not just WHAT
3. **Capture alternatives**: "Tried X but Y worked better because..." helps future work
4. **Reference deep dives**: "See RESEARCH.md #caching-strategy for full rationale"
5. **Write for the future**: Developers reading weeks/months later need context
6. **Newest first**: Always add new entries at the TOP of WORKLOG.md (reverse chronological)
7. **Be honest about failures**: Failed attempts are valuable documentation

### Entry Length Guidelines

**Good length** (scannable):
```markdown
## 2025-01-15 14:30 - [AUTHOR: backend-specialist] → [NEXT: code-reviewer]

Implemented JWT auth endpoint with bcrypt (12 rounds) and Redis token storage.

Gotcha: Redis connection pooling required - single connection bottleneck
Files: src/auth/login.ts, src/middleware/jwt.ts, tests/auth.test.ts

→ Passing to code-reviewer for security validation
```

**Too brief** (missing context):
```markdown
## 2025-01-15 14:30 - [AUTHOR: backend-specialist] → [NEXT: code-reviewer]

Implemented auth endpoint.

→ Passing to code-reviewer
```

**Too verbose** (belongs in RESEARCH.md):
```markdown
## 2025-01-15 14:30 - [AUTHOR: backend-specialist] → [NEXT: code-reviewer]

Implemented JWT auth endpoint. After evaluating 5 different hashing algorithms
(bcrypt, scrypt, argon2, PBKDF2, and SHA-256), selected bcrypt because...
[500 more words explaining the decision]

→ Passing to code-reviewer for security validation
```

### When to Split Across Formats

**Use BOTH formats in same WORKLOG** when troubleshooting during implementation:

```markdown
## 2025-01-16 16:45 - [AUTHOR: backend-specialist] (Phase 2.2 COMPLETE)

Phase 2.2 complete after resolving auth issue (see troubleshooting entries below).

Status:
- ✅ Tests passing (48/48)
- ✅ Auth working correctly
- ✅ PLAN.md updated

---

## 2025-01-16 16:15 - [AUTHOR: backend-specialist] (Loop 2 - Success)

Hypothesis: Using wrong auth API (should use getAuthUserId helper)
[... full troubleshooting entry ...]

---

## 2025-01-16 15:30 - [AUTHOR: backend-specialist] (Loop 1 - Failed)

Hypothesis: Query runs before auth completes
[... full troubleshooting entry ...]

---

## 2025-01-16 14:00 - [AUTHOR: backend-specialist] → [NEXT: code-reviewer]

Implemented initial auth backend. Hit unexpected issue with getUserIdentity.
Starting troubleshooting loop.

→ Passing to self for debug investigation
```

---

## Integration with Commands

### Commands Using Standard Format

- **`/implement`**: HANDOFF and COMPLETE entries for phase work
- **`/comment`**: Manual entries by humans with @username
- **All agents**: Handoff entries during multi-agent workflows
- **`/quality`**: Review entries with quality scores

### Commands Using Troubleshooting Format

- **`/troubleshoot`**: Structured hypothesis/result entries
- **During `/implement`**: When hitting issues requiring debug loop

### Commands Reading WORKLOG

- **`/implement --next`**: Reads WORKLOG to understand previous work
- **`/sanity-check`**: Reviews WORKLOG for drift detection
- **`/plan`**: May reference WORKLOG for context
- **All agents**: Read WORKLOG before continuing work

---

## Related Documentation

**For troubleshooting methodology**:
- See `troubleshooting.md` for complete 5-step debug loop
- See `troubleshooting.md` for debug logging best practices

**For workflow context**:
- See `development-loop.md` for agent handoff patterns
- See `development-loop.md` for quality gates and workflow phases

**For file structure**:
- See `issue-management.md` for TASK.md, BUG.md formats
- See `plan-structure.md` for PLAN.md format
- See `research-documentation.md` for RESEARCH.md format
