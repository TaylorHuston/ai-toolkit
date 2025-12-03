---
last_updated: "2025-11-26"
description: "WORKLOG entry formats for implementation, troubleshooting, and investigation"

# === Worklog Configuration ===
worklog_ordering: "reverse_chronological"    # Newest entries at TOP
entry_philosophy: "context_rich_handoffs"    # Provide all context next agent needs
entry_length: "information_complete"         # 10-20 lines average
---

# WORKLOG Format

**Purpose**: Provide complete context for the next agent to continue work effectively.

**Philosophy**: Context-rich handoffs. Each entry contains ALL information the next agent needs.

**Entry Length**: 10-20 lines average. Test: "Can the next agent continue without re-reading code?"

**Entry Ordering**: **CRITICAL** - Newest entries at TOP (reverse chronological).

---

## Quick Reference

**Three Format Types**:
1. **Standard** - Implementation work, handoffs, phase completion, reviews
2. **Troubleshooting** - Debug loops with hypothesis tracking
3. **Investigation** - Research findings, external knowledge

**When to write**:
- ✅ Completing work and handing off
- ✅ Completing a phase or subtask
- ✅ **MANDATORY**: After every code/security review (reviewer writes own entry)
- ✅ Each troubleshooting loop iteration
- ✅ Completing external research
- ❌ Don't write "STARTED" entries

---

## Phase Commits Section

**Location**: Top of WORKLOG.md (above all entries)

```markdown
## Phase Commits

- Phase 1.1: `abc123d` - Database schema setup
- Phase 1.2: `def456e` - JWT authentication
- Phase 2.1: `ghi789j` - Frontend login form
```

**Workflow** (mandatory after every phase commit):
1. Complete phase → Write entry → Commit
2. Get commit ID: `git rev-parse --short HEAD`
3. Update Phase Commits section
4. Amend or separate commit for WORKLOG update

---

## Standard Format

**Use for**: Implementation (`/implement`), handoffs, phase completion, reviews

**Timestamp**: Always run `date '+%Y-%m-%d %H:%M'` - never estimate.

### Handoff Entry

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] → [NEXT: next-agent]

**Completed**: [What was accomplished - 2-3 lines]

**Key decisions**:
- [Decision 1 and rationale]
- [Decision 2 and rationale]

**Gotchas**:
- [Issue]: [How it manifested] → [Solution]

**Files**: [key/files/changed.js]

→ Passing to {next-agent} for {reason}
```

**Example**:
```markdown
## 2025-01-15 14:30 - [AUTHOR: backend-specialist] → [NEXT: code-reviewer]

**Completed**: JWT auth endpoint with bcrypt (12 rounds) and Redis token storage.

**Key decisions**:
- Used Redis for token storage (horizontal scaling)
- 15min access / 7d refresh token expiry

**Gotchas**:
- Redis connection pooling required - single connection bottleneck

**Files**: src/auth/login.ts, src/middleware/jwt.ts, tests/auth.test.ts

→ Passing to code-reviewer for security validation
```

### Complete Entry

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] (Phase X.Y COMPLETE)

**Phase objective**: [What this phase accomplished]

**Implementation**: [Approach and key decisions]

**Quality gates**:
- ✅ Tests passing (X/X)
- ✅ Code review score: [score/100]
- ✅ PLAN.md updated

**Files**: [key/files/changed.js]
```

### Review Entry

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: code-reviewer] (Review Approved|Requires Changes)

**Reviewed**: [Phase/feature]
**Score**: [92/100] ✅ Approved | ⚠️ Requires Changes

**Strengths**: [What works well]

**Issues** (if any):
- [Critical]: [Problem] @ file.ts:123 → [Fix needed]
- [Major]: [Problem] → [Recommendation]

**Files**: [reviewed files]

→ [If requires changes: Passing back to {agent} for fixes]
```

---

## Troubleshooting Format

**Use for**: Debug loops (`/troubleshoot`), hypothesis testing

### Loop Entry

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] (Loop N - Success|Failed)

**Hypothesis**: [Theory based on research]
**Debug findings**: [What logs revealed]
**Implementation**: [What was changed]
**Result**: ✅ Fixed | ❌ Not fixed - [evidence]

**Files**: [files modified]
**Tests**: X/X passing
[If failed: **Rollback**: ✅ Changes reverted]
[If failed: **Next**: Alternative approach]
```

**Example (Success)**:
```markdown
## 2025-01-16 16:15 - [AUTHOR: backend-specialist] (Loop 2 - Success)

**Hypothesis**: Using wrong auth API (should use getAuthUserId helper)
**Debug findings**: Logs confirmed getUserIdentity returns null, getAuthUserId works
**Implementation**: Replaced ctx.auth.getUserIdentity() with getAuthUserId(ctx)
**Result**: ✅ Fixed - Login works, profile displays correctly

**Files**: convex/users.ts
**Tests**: 245/245 passing
**Manual verification**: User confirmed working
```

**Example (Failed)**:
```markdown
## 2025-01-16 15:30 - [AUTHOR: backend-specialist] (Loop 1 - Failed)

**Hypothesis**: Query runs before auth completes
**Debug findings**: isLoading=false before query, but userId still null
**Implementation**: Added isLoading gate to skip query during auth
**Result**: ❌ Not fixed - User still sees "Not signed in"
**Rollback**: ✅ Changes reverted

**Next**: Research getAuthUserId() vs getUserIdentity() API difference
```

---

## Research Format

**Use for**: External research (`research-specialist`), documentation lookup, `/research` command

### Research Entry (Inline)

For quick research during `/plan`, `/implement`, `/troubleshoot`:

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: research-specialist] (Research Complete)

**Query**: [What was researched]
**Sources**: [N] resources ([breakdown: Context7: X, blogs: Y, SO: Z, GitHub: W])
**Output**: Inline summary

**Key findings**: [2-3 sentence summary]

**Top resources** (the ones that actually matter):
1. **[Title]** - [url] - [Why this one matters]
2. **[Title]** - [url] - [Why this one matters]
3. **[Title]** - [url] - [Why this one matters]

**Gotchas identified**: [Key warnings for implementation]

→ Passing to {agent} for implementation
```

### Research Entry (Persistent Document)

When creating a persistent research document via `/research`:

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: research-specialist] (Research Document Created)

**Query**: [What was researched]
**Sources**: [N] resources ([breakdown])
**Output**: docs/project/research/{filename}.md

**Key findings**: [2-3 sentence summary]

**Document covers**:
- [Topic area 1]
- [Topic area 2]
- [Topic area 3]

**Top resources included**: [N] must-read, [N] additional

→ Document saved for future reference by /plan, /implement, /troubleshoot
```

**Example (Inline)**:
```markdown
## 2025-01-16 14:45 - [AUTHOR: research-specialist] (Research Complete)

**Query**: PostgreSQL JSONB aggregation performance issues
**Sources**: 28 resources (Context7: 2, blogs: 12, SO: 10, GitHub: 4)
**Output**: Inline summary

**Key findings**: jsonb_agg loads entire result into memory. Three proven solutions with different trade-offs depending on dataset size.

**Top resources** (the ones that actually matter):
1. **"PostgreSQL JSONB Deep Dive"** - https://example.com/pg-jsonb - Has actual benchmarks comparing all approaches
2. **"SO: jsonb_agg memory"** - https://stackoverflow.com/q/123 - Accepted answer with production-tested solution
3. **"PG Wiki: JSON Performance"** - https://wiki.postgresql.org/json - Official guidance on memory settings

**Gotchas identified**: work_mem setting affects all operations, not just JSON; chunked approach requires cursor management

→ Passing to backend-specialist for implementation decision
```

**Example (Persistent)**:
```markdown
## 2025-01-16 15:30 - [AUTHOR: research-specialist] (Research Document Created)

**Query**: Convex CLI capabilities
**Sources**: 35 resources (Context7: 5, official docs: 15, GitHub: 10, community: 5)
**Output**: docs/project/research/convex-cli-capabilities.md

**Key findings**: Convex CLI covers project init, deployment, function management, data ops, and env management. Key workflow is `npx convex dev` for local, `npx convex deploy` for prod.

**Document covers**:
- All CLI commands with flags
- Common workflows (dev, deploy, data migration)
- Environment management patterns
- Gotchas (deployment order, function naming)

**Top resources included**: 4 must-read, 6 additional

→ Document saved for future reference by /plan, /implement, /troubleshoot
```

---

## Best Practices

1. **Information complete**: Include all context next agent needs
2. **Emphasize gotchas**: 40-60% of entry on discoveries, issues, surprises
3. **Document WHY**: "Used bcrypt with cost 12 because..." not just "Added hashing"
4. **Quantify**: "94% coverage (127/135 lines)" not "good coverage"
5. **File:line references**: "SQL injection @ auth.ts:45" for issues
6. **Newest first**: Always add entries at TOP
7. **Be honest about failures**: Failed attempts with lessons are valuable

---

## Integration with Commands

**Standard Format**:
- `/implement` - Handoff and complete entries
- `/worklog` - Manual entries (@username)
- `code-reviewer`, `security-auditor` - Review entries

**Troubleshooting Format**:
- `/troubleshoot` - Loop entries
- During `/implement` when debugging

**Research Format**:
- `research-specialist` - Research entries (inline and persistent)
- `/research` command - Persistent document creation
- During `/plan`, `/implement`, `/troubleshoot` research phases

**Commands Reading WORKLOG**:
- `/implement` - Understands previous work
- `/sanity-check` - Detects drift
- All agents - Read before continuing

---

## Related Documentation

- `bug-workflow.md` - 5-step debugging loop
- `task-workflow.md` - TDD implementation workflow
- `pm-file-formats.md` - TASK.md, BUG.md, PLAN.md formats
