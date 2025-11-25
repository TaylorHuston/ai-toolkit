---
last_updated: "2025-11-18"
description: "Comprehensive WORKLOG entry examples for all format types"
---

# WORKLOG Format Examples

**This document provides concrete examples for all WORKLOG entry types defined in worklog-format.md.**

**See worklog-format.md for:**
- Format definitions and templates
- Required elements
- Best practices
- Integration with commands

---

## Standard Format Examples

### Handoff Entry Example

```markdown
## 2025-01-15 14:30 - [AUTHOR: backend-specialist] → [NEXT: code-reviewer]

Implemented JWT auth endpoint with bcrypt hashing (12 rounds) and Redis token storage.

Gotcha: Redis connection pooling required - single connection bottleneck
Files: src/auth/login.ts, src/middleware/jwt.ts, tests/auth.test.ts

→ Passing to code-reviewer for security validation
```

### Complete Entry Example

```markdown
## 2025-01-15 15:35 - [AUTHOR: code-reviewer] (Phase 2.3 COMPLETE)

Re-review approved (score: 94/100). All security issues resolved.

Status:
- ✅ Tests passing (48/48)
- ✅ Security validated
- ✅ PLAN.md checkbox updated
```

### Human Comment Entry Example (via `/worklog`)

```markdown
## 2025-01-15 10:15 - [AUTHOR: @alice]

Disabled middleware auth check temporarily - was blocking sign-up flow. Cookie name mismatch.

Files: src/middleware.ts (disabled check on line 42)

→ Need to fix cookie naming before re-enabling
```

---

## Review Entry Examples

### Code Review Approved Example

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

### Code Review with Issues Example

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

### Security Review Approved Example

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

### Security Review with Vulnerabilities Example

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

### Plan Changes Entry Example

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

## Troubleshooting Format Examples

### Loop 1 - Failed Example

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

### Loop 2 - Success Example

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

## Investigation Format Examples

### Investigation Complete Example (Troubleshooting)

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

### Investigation Complete Example (Best Practices)

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

### Investigation Complete Example (Simple)

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

### Investigation Incomplete Example

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

## Entry Length Guidelines with Examples

### Good Length Example (Scannable)

```markdown
## 2025-01-15 14:30 - [AUTHOR: backend-specialist] → [NEXT: code-reviewer]

Implemented JWT auth endpoint with bcrypt (12 rounds) and Redis token storage.

Gotcha: Redis connection pooling required - single connection bottleneck
Files: src/auth/login.ts, src/middleware/jwt.ts, tests/auth.test.ts

→ Passing to code-reviewer for security validation
```

### Too Brief Example (Missing Context)

```markdown
## 2025-01-15 14:30 - [AUTHOR: backend-specialist] → [NEXT: code-reviewer]

Implemented auth endpoint.

→ Passing to code-reviewer
```

**Why problematic**: Missing implementation details (JWT, bcrypt, Redis), no gotchas/lessons, no file references

### Too Verbose Example (Should Use ADR)

```markdown
## 2025-01-15 14:30 - [AUTHOR: backend-specialist] → [NEXT: code-reviewer]

Implemented JWT auth endpoint. After evaluating 5 different hashing algorithms
(bcrypt, scrypt, argon2, PBKDF2, and SHA-256), selected bcrypt because of its
adaptive cost factor and proven track record. Bcrypt is intentionally slow...
[500 more words explaining the decision]

→ Passing to code-reviewer for security validation
```

**Why problematic**: Too much detail for WORKLOG. Complex architectural decisions belong in ADRs.

### Better Approach (Reference ADR)

```markdown
## 2025-01-15 14:30 - [AUTHOR: backend-specialist] → [NEXT: code-reviewer]

Implemented JWT auth using bcrypt for password hashing (see ADR-007 for algorithm evaluation).
Token expiry: 15min access, 7d refresh. HttpOnly cookies prevent XSS.

→ Passing to code-reviewer for security validation
```

**Why better**: Concise summary with ADR reference for details. All key decisions documented, entry remains scannable.

---

## Multi-Format Workflow Example

**Example showing investigation → troubleshooting → implementation in one WORKLOG:**

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

**Why this works**: Complete story of problem → research → failed attempt → successful fix. Future developers can see entire debugging journey and learn from both failures and successes.

---

## Related Documentation

**For format definitions**: See `worklog-format.md` for templates, required elements, and best practices

**For troubleshooting methodology**: See `troubleshooting.md` for complete 5-step debug loop

**For workflow context**: See `development-loop.md` for agent handoff patterns

**For file structure**: See `pm-workflows.md` for TASK.md, BUG.md, and PLAN.md formats
