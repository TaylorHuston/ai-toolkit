---
# === Metadata ===
template_type: "guideline"
created: "2025-11-05"
last_updated: "2025-11-06"
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

**Three Format Types**:
1. **Standard Format** - For implementation work, agent handoffs, phase completion
2. **Troubleshooting Format** - For debug loops with hypothesis tracking
3. **Investigation Format** - For research findings, external knowledge gathering

**When to write**:
- ✅ Completing work and handing off to another agent
- ✅ Receiving work back with changes needed
- ✅ Completing a phase or subtask
- ✅ Each troubleshooting loop iteration
- ✅ Completing external research (context-analyzer)
- ❌ Don't write "STARTED" entries (waste - just do the work)

**Key principle**: Newest entries at TOP, brief (~500 chars), focus on insights

---

## Format Selection Decision Tree

**What are you documenting?**

```
├─ Completed work being passed to another agent
│  └─ Use: Standard HANDOFF entry
│
├─ Completed phase/task (no more handoffs)
│  └─ Use: Standard COMPLETE entry
│
├─ Code review results
│  ├─ Approved (score >= 90)? → Use: Standard REVIEW APPROVED entry
│  └─ Issues found (score < 90)? → Use: Standard REVIEW REQUIRES CHANGES entry
│
├─ Security review results
│  ├─ No vulnerabilities? → Use: Standard REVIEW APPROVED entry
│  └─ Vulnerabilities found? → Use: Standard REVIEW REQUIRES CHANGES entry
│
├─ Review cycle resulted in plan changes
│  └─ Use: Standard PLAN CHANGES entry
│
├─ Debugging/troubleshooting work
│  ├─ Hypothesis succeeded? → Use: Troubleshooting Loop N - Success entry
│  └─ Hypothesis failed? → Use: Troubleshooting Loop N - Failed entry
│
└─ External research findings
   ├─ Found solutions? → Use: Investigation Complete entry
   └─ No clear solution? → Use: Investigation Incomplete entry
```

**Quick Examples:**

- **Agent handoff**: "I finished API endpoints, passing to frontend-specialist" → HANDOFF
- **Phase done**: "Phase 2.1 complete, all tests passing" → COMPLETE
- **Code review**: "Reviewed and found 3 issues needing fixes" → REVIEW REQUIRES CHANGES
- **Security audit**: "No vulnerabilities found, approved" → REVIEW APPROVED
- **Plan updated**: "Security audit found gaps, updated TASK/PLAN" → PLAN CHANGES
- **Debugging**: "Tried adding indexes, query still slow" → Troubleshooting Loop N - Failed
- **Research**: "Researched PostgreSQL JSONB performance solutions" → Investigation Complete

---

## Phase Commits Summary Section

**Purpose**: Provide quick rollback map for each completed phase

**Location**: At the **top** of WORKLOG.md (above all entries)

**Format**:
```markdown
## Phase Commits

- Phase 1.1: `abc123d` - Database schema setup
- Phase 1.2: `def456e` - JWT authentication implementation
- Phase 2.1: `ghi789j` - Frontend login form
- Phase 2.2: `klm012n` - Integration tests

---
```

**Workflow**:
1. Complete phase → Update PLAN.md/TASK.md → Write WORKLOG entry → Commit
2. Get commit ID: `git rev-parse --short HEAD`
3. Add one line to "Phase Commits" section: `- Phase X.Y: \`commit-id\` - Brief description`
4. Commit reference: `git add WORKLOG.md && git commit -m "docs(TASK-001): add phase X.Y commit reference"`

**Benefit**: Instant visual rollback map, no chicken-and-egg problem with commit IDs

**Rollback Example**:
```bash
# Roll back to Phase 1.2 (before Phase 2.1 changes)
git reset --hard def456e
```

---

## Standard WORKLOG Format

**Use for**: Implementation work (`/implement`), agent handoffs, phase completion, general development, code reviews, security reviews

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

#### REVIEW APPROVED Entry (code/security review passed)

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: code-reviewer|security-auditor] (Review Approved)

Reviewed: [Phase/feature/files reviewed]
Scope: [Quality/Security/Performance - which aspects reviewed]
Verdict: ✅ Approved [clean / with minor notes]

Strengths:
- [Key positive aspect 1]
- [Key positive aspect 2]

[Optional] Notes:
- [Minor suggestion or observation]

Files: [files reviewed]
```

#### REVIEW REQUIRES CHANGES Entry (issues found, passing back)

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: code-reviewer|security-auditor] → [NEXT: implementation-agent]

Reviewed: [Phase/feature/files reviewed]
Scope: [Quality/Security/Performance]
Verdict: ⚠️ Requires Changes

Critical:
- [Issue description] @ file.ts:line - [Fix needed]

[Optional] Major:
- [Issue description] @ file.ts:line - [Fix needed]

[Optional] Minor:
- [Issue description] - [Suggestion]

Files: [files reviewed]

→ Passing back to {agent-name} for fixes
```

### Review Entry Examples

**Code review approved**:
```markdown
## 2025-01-17 14:20 - [AUTHOR: code-reviewer] (Review Approved)

Reviewed: Phase 2.1 - JWT authentication implementation
Scope: Code quality, security basics, testing
Verdict: ✅ Approved clean

Strengths:
- Proper async error handling throughout
- Comprehensive test coverage (94%)
- Clear separation of concerns

Files: src/auth/login.ts, src/middleware/jwt.ts, tests/auth.test.ts
```

**Code review with issues**:
```markdown
## 2025-01-17 15:45 - [AUTHOR: code-reviewer] → [NEXT: backend-specialist]

Reviewed: Phase 3.2 - Payment processing implementation
Scope: Code quality, error handling, edge cases
Verdict: ⚠️ Requires Changes

Critical:
- Missing error handling for Stripe webhook timeout @ payment.ts:156 - Add try/catch with idempotency check
- Race condition in payment status update @ payment.ts:203 - Use database transaction

Major:
- Test coverage only 65% (target: 80%) - Add tests for refund flow and webhook retries

Minor:
- Magic number for retry count - Extract to config constant

Files: src/payment/payment.ts, src/payment/webhook.ts, tests/payment.test.ts

→ Passing back to backend-specialist for fixes
```

**Security review approved**:
```markdown
## 2025-01-18 10:30 - [AUTHOR: security-auditor] (Review Approved)

Reviewed: Phase 1.3 - OAuth2 authentication flow
Scope: Security (OWASP Top 10, auth best practices)
Verdict: ✅ Approved with minor notes

Strengths:
- PKCE implemented correctly (prevents authorization code interception)
- State parameter validated (CSRF protection)
- Tokens stored in httpOnly cookies (XSS protection)

Notes:
- Consider adding rate limiting on token endpoint (good-to-have, not required)

Files: src/auth/oauth.ts, src/middleware/auth.ts
```

**Security review with vulnerabilities**:
```markdown
## 2025-01-18 11:15 - [AUTHOR: security-auditor] → [NEXT: backend-specialist]

Reviewed: Phase 2.4 - User profile API endpoints
Scope: Security (OWASP A01 Access Control, A03 Injection)
Verdict: ⚠️ Requires Changes - Critical vulnerabilities found

Critical:
- Missing authorization check on DELETE /users/:id @ users.ts:89 - Add ownership verification (OWASP A01: Broken Access Control)
- SQL injection risk in search query @ users.ts:145 - Use parameterized query or ORM (OWASP A03: Injection)

Major:
- PII returned without consent check @ users.ts:67 - Add GDPR consent validation before exposing email/phone

Files: src/api/users.ts, src/middleware/auth.ts

→ Passing back to backend-specialist for security fixes (URGENT)
```

#### PLAN CHANGES Entry (documenting adaptations after reviews)

```markdown
## YYYY-MM-DD HH:MM - Review Cycle: Plan Updated

[Review type] completed on [Phase X] implementation.

**Key Findings**:
- [Finding 1 that requires action]
- [Finding 2 that requires action]
- [Finding 3 if applicable]

**Decisions**:
- [What changed in TASK.md and why]
- [What changed in PLAN.md and why]
- [What was deferred/descoped and why]

**Files Updated**: TASK.md, PLAN.md
**Full report**: [link if needed]
```

**Plan changes entry example**:
```markdown
## 2025-11-06 14:30 - Security Audit: Plan Updated

Security audit completed on Phase 2 (email/password auth) implementation.

**Key Findings**:
- Email verification required to prevent spam accounts (high priority)
- Rate limiting needed on auth endpoints (medium priority)
- Session timeout should be 24h not 7d (configuration issue)

**Decisions**:
- Added "Email verification required" to TASK.md acceptance criteria
- Inserted Phase 3 for email verification in PLAN.md (before OAuth)
- Rate limiting deferred to post-MVP (tracked in backlog)
- Updated session config in Phase 2 deliverables

**Files Updated**: TASK.md, PLAN.md
**Full report**: docs/security/audit-2025-11-06.md
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

## Investigation Results WORKLOG Format

**Use for**: External research (`context-analyzer`), documentation lookup, resource discovery, knowledge gathering

**Key difference**: Focuses on research findings and resource curation, not code changes or hypothesis testing

### Investigation Entry Template

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: context-analyzer] (Investigation Complete)

Query: [What was being researched]
Sources: [Number] resources analyzed ([docs/blogs/SO/GitHub breakdown])
Key findings: [2-3 sentence summary of discoveries]

Top solutions:
1. [Solution name] - [Brief description and use case]
2. [Solution name] - [Brief description and use case]
3. [Solution name] - [Brief description and use case]

Curated resources:
- [Resource title] - [url] - [Why valuable]
- [Resource title] - [url] - [Why valuable]

💡 Suggested for CLAUDE.md:
- [Resource] → [Category] - [Why add to project resources]

→ Passing findings to {agent-name} for implementation
```

### Required Elements

- **Timestamp**: Always run `date '+%Y-%m-%d %H:%M'` - never estimate
- **Query**: What was being researched (the request from calling agent)
- **Sources**: Count and breakdown (docs/blogs/SO/GitHub)
- **Key findings**: High-level summary (2-3 sentences max)
- **Top solutions**: Ranked approaches with use cases
- **Curated resources**: Best resources found (2-5 links with context)
- **Suggested resources**: Exceptional finds worth adding to CLAUDE.md (0-3 max)
- **Handoff note**: Which agent receives findings and for what purpose

### Investigation Format Examples

**Research for troubleshooting**:
```markdown
## 2025-01-16 14:45 - [AUTHOR: context-analyzer] (Investigation Complete)

Query: PostgreSQL JSONB aggregation performance issues causing timeouts
Sources: 12 resources (Context7: 1, blogs: 4, SO: 5, GitHub: 2)
Key findings: jsonb_agg loads entire result set into memory causing timeouts on large datasets. Three proven solutions with different trade-offs. Memory configuration alone won't solve root issue.

Top solutions:
1. Chunked aggregation pattern - Best for large datasets, requires query restructure, proven at scale
2. array_agg + json_build_object - Alternative aggregation method, better memory profile
3. Increase work_mem - Quick temporary fix, doesn't scale, risks OOM on concurrent queries

Curated resources:
- "PostgreSQL JSONB Performance Deep Dive" - https://example.com/pg-jsonb - Comprehensive benchmarks comparing all approaches with real-world data
- Stack Overflow chunked pattern answer - https://stackoverflow.com/... - Working code example with step-by-step implementation

💡 Suggested for CLAUDE.md:
- "PostgreSQL JSONB Performance Deep Dive" → Performance & Optimization - Core tech, extensive benchmarks, covers aggregation patterns we'll likely reference again for similar issues

→ Passing findings to backend-specialist for implementation decision
```

**Research for best practices**:
```markdown
## 2025-01-17 10:20 - [AUTHOR: context-analyzer] (Investigation Complete)

Query: Node.js API rate limiting best practices for distributed systems
Sources: 15 resources (Context7: 2, blogs: 8, GitHub: 5)
Key findings: Three main algorithms (token bucket, leaky bucket, fixed window). Token bucket most flexible for APIs. Redis required for distributed rate limiting to share state across instances.

Top solutions:
1. rate-limiter-flexible + Redis - Most comprehensive, battle-tested, supports multiple algorithms
2. Custom Redis implementation - Full control, requires more maintenance, good learning exercise
3. express-rate-limit (memory) - Simple but single-server only, not suitable for production

Curated resources:
- "Rate Limiting Algorithms Explained" - https://example.com/rate-limiting - Visual algorithm comparison with use cases and trade-offs
- rate-limiter-flexible docs - https://github.com/... - Production-ready library with Redis examples

→ Passing findings to backend-specialist for implementation decision
```

**Research with no suggestions**:
```markdown
## 2025-01-18 09:15 - [AUTHOR: context-analyzer] (Investigation Complete)

Query: Specific error "TypeError: Cannot read property 'id' of undefined" in React component
Sources: 8 resources (blogs: 3, SO: 5)
Key findings: Common issue when component renders before async data loads. Two main patterns: conditional rendering or optional chaining.

Top solutions:
1. Optional chaining (?.id) - Modern, concise, handles deeply nested properties
2. Conditional rendering with loading state - More explicit, better UX with spinner

Curated resources:
- Stack Overflow answer - https://stackoverflow.com/... - Clear explanation of both approaches

→ Passing findings to frontend-specialist for implementation
```

### Investigation Format Variants

#### When No Clear Solution Found

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: context-analyzer] (Investigation Incomplete)

Query: [What was being researched]
Sources: [Number] resources analyzed
Key findings: No definitive solution found. [What was learned]

Partial findings:
- [Finding 1 with uncertainty noted]
- [Finding 2 with caveats]

Resources checked:
- Official docs - [What was missing]
- Community sources - [What was found but not conclusive]

Recommendation: [Suggest alternative approach, ask domain expert, try different search terms]

→ Passing to {agent-name} with findings (no implementation recommended yet)
```

**Example**:
```markdown
## 2025-01-19 11:30 - [AUTHOR: context-analyzer] (Investigation Incomplete)

Query: Custom Convex validator for international phone numbers with country codes
Sources: 9 resources (Context7: 1, blogs: 3, SO: 3, GitHub: 2)
Key findings: No built-in validator in Convex. Found general phone validation libraries but unclear how to integrate custom validators in Convex schema.

Partial findings:
- libphonenumber-js library handles international formats well
- Convex validators are TypeScript types, not runtime validation
- May need custom function wrapper for validation

Resources checked:
- Convex docs - Shows string validators but not custom validation patterns
- Community discussions - Limited examples of complex custom validators

Recommendation: Ask in Convex Discord or check if validator can be custom function wrapping v.string()

→ Passing to backend-specialist with partial findings (implementation approach unclear)
```

---

## WORKLOG Best Practices

**Apply to all formats (standard, troubleshooting, investigation, reviews)**:

1. **Keep entries scannable**: ~500 chars is ideal, can be longer for critical gotchas
2. **Focus on insights**: Document WHY things were done certain ways, not just WHAT
3. **Capture alternatives**: "Tried X but Y worked better because..." helps future work
4. **Reference decisions**: For architecture decisions, use `/adr` command to create ADR
5. **Write for the future**: Developers reading weeks/months later need context
6. **Newest first**: Always add new entries at the TOP of WORKLOG.md (reverse chronological)
7. **Be honest about failures**: Failed attempts are valuable documentation
8. **Review specificity**: For review entries, always include file:line references for issues

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

**Too verbose** (consider using `/adr` for architecture decisions):
```markdown
## 2025-01-15 14:30 - [AUTHOR: backend-specialist] → [NEXT: code-reviewer]

Implemented JWT auth endpoint. After evaluating 5 different hashing algorithms
(bcrypt, scrypt, argon2, PBKDF2, and SHA-256), selected bcrypt because...
[500 more words explaining the decision]

→ Passing to code-reviewer for security validation
```

**Better approach for complex decisions**:
```markdown
## 2025-01-15 14:30 - [AUTHOR: backend-specialist] → [NEXT: code-reviewer]

Implemented JWT auth using bcrypt for password hashing (see ADR-007 for algorithm evaluation).
Token expiry: 15min access, 7d refresh. HttpOnly cookies prevent XSS.

→ Passing to code-reviewer for security validation
```

### When to Mix Formats

**Use MULTIPLE formats in same WORKLOG** when work involves research + troubleshooting + implementation:

**Example: Investigation → Troubleshooting → Implementation**:

```markdown
## 2025-01-16 16:45 - [AUTHOR: backend-specialist] (Phase 2.2 COMPLETE)

Phase 2.2 complete after resolving auth issue (see investigation + troubleshooting entries below).

Status:
- ✅ Tests passing (48/48)
- ✅ Auth working correctly
- ✅ PLAN.md updated

---

## 2025-01-16 16:15 - [AUTHOR: backend-specialist] (Loop 2 - Success)

Hypothesis: Using wrong auth API (should use getAuthUserId helper per context-analyzer findings)
Debug findings: Logs confirmed getUserIdentity returns null, getAuthUserId works
Implementation: Replaced ctx.auth.getUserIdentity() with getAuthUserId(ctx)
Research: @convex-dev/auth/server documentation (from context-analyzer curated resources)
Result: ✅ Fixed - Login works, profile displays correctly

Files: convex/users.ts
Tests: 48/48 passing
Manual verification: User confirmed working

---

## 2025-01-16 15:30 - [AUTHOR: backend-specialist] (Loop 1 - Failed)

Hypothesis: Query runs before auth completes (based on Convex Auth docs)
Debug findings: Logs show isLoading=false before query, but userId still null
Implementation: Added isLoading gate to skip query during auth
Result: ❌ Not fixed - User still sees "Not signed in" after login
Rollback: ✅ Changes reverted

Next: Request research on getAuthUserId() vs getUserIdentity() API difference

---

## 2025-01-16 15:00 - [AUTHOR: context-analyzer] (Investigation Complete)

Query: Convex Auth getAuthUserId vs getUserIdentity API difference
Sources: 5 resources (Context7: 1, GitHub: 2, Convex Discord: 2)
Key findings: Two different auth APIs with different use cases. getAuthUserId is helper from @convex-dev/auth, getUserIdentity is core Convex API. Auth library uses different flow.

Top solutions:
1. Use getAuthUserId(ctx) - Recommended for @convex-dev/auth library
2. Use ctx.auth.getUserIdentity() - For native Convex auth only

Curated resources:
- @convex-dev/auth/server docs - https://labs.convex.dev/auth/... - Clear explanation of helper functions
- GitHub discussion - https://github.com/get-convex/convex-helpers/discussions/... - Comparison of both approaches

→ Passing findings to backend-specialist for implementation

---

## 2025-01-16 14:00 - [AUTHOR: backend-specialist] → [NEXT: context-analyzer]

Implemented initial auth backend using getUserIdentity(). Hit unexpected issue - returns null after login.
Need research on correct auth API usage for @convex-dev/auth library.

Files: convex/users.ts (initial implementation)

→ Passing to context-analyzer for API research
```

---

## Integration with Commands

### Commands Using Standard Format

- **`/implement`**: HANDOFF and COMPLETE entries for phase work
- **`/comment`**: Manual entries by humans with @username
- **All agents**: Handoff entries during multi-agent workflows
- **`/quality`**: Review entries with quality scores
- **`code-reviewer`**: REVIEW APPROVED and REVIEW REQUIRES CHANGES entries
- **`security-auditor`**: REVIEW APPROVED and REVIEW REQUIRES CHANGES entries with OWASP classifications

### Commands Using Troubleshooting Format

- **`/troubleshoot`**: Structured hypothesis/result entries
- **During `/implement`**: When hitting issues requiring debug loop

### Commands Using Investigation Format

- **`context-analyzer`**: Research findings and resource curation entries
- **During `/troubleshoot`**: Research phase before hypothesis formation
- **Implementation agents**: When requesting external knowledge

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
- See `pm-guide.md` for TASK.md, BUG.md formats
- See `pm-guide.md` for PLAN.md format

**For complex decisions**:
- Use `/adr` command to create Architecture Decision Records for significant technical decisions
