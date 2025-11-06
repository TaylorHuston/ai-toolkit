---
# === Metadata ===
template_type: "guideline"
version: "1.0.0"
created: "2025-11-05"
last_updated: "2025-11-05"
status: "Active"
target_audience: ["AI Assistants", "Developers"]
description: "RESEARCH.md format for technical decisions, alternative analysis, and complex root cause investigations"

# === Configuration ===
research_threshold: "500_chars"          # If decision fits in 500 chars, use WORKLOG only
alternatives_threshold: 3                 # 3+ alternatives warrant RESEARCH.md
anchor_format: "#kebab-case"             # Heading anchors for WORKLOG references
---

# Research Documentation Guidelines

**Purpose**: Document complex technical decisions requiring detailed analysis, benchmarks, or alternative comparisons

**Referenced by**: `/implement` command (when creating RESEARCH.md), WORKLOG entries (linking to research)

## Quick Reference

**Create RESEARCH.md when:**
- Evaluating 3+ alternatives with trade-offs
- Performance benchmarks with data
- Deep root cause analysis for complex bugs
- Architecture decisions affecting multiple components
- Technical spikes exploring multiple approaches

**Keep in WORKLOG when:**
- Decision is straightforward (~500 chars)
- Following established patterns from ADRs
- Implementation details without alternatives
- Quick gotchas or lessons

**Rule of thumb**: Can you explain it in ~500 chars? → WORKLOG only. Need multiple pages with data? → RESEARCH.md

---

## When to Create RESEARCH.md

### Criteria for RESEARCH.md

**✅ Create RESEARCH.md when decisions involve:**

**Complex analysis requiring detailed documentation:**
- Evaluated **3+ alternatives** with detailed trade-off analysis
- Performed **benchmarks or performance testing** with data
- **Deep root cause analysis** for non-obvious bugs
- **Architecture decisions** affecting multiple components
- **Technical spikes** exploring multiple approaches
- **Security decisions** with threat modeling

**✅ Examples warranting RESEARCH.md:**
- "Evaluated PostgreSQL vs MongoDB vs Redis for session storage (6 criteria, benchmarks)"
- "Root cause: Memory leak from unclosed database connections in connection pool"
- "API architecture: REST vs GraphQL vs gRPC (performance tests, ecosystem analysis)"
- "Caching strategy: Redis vs Memcached vs in-memory (load testing results)"
- "Authentication: JWT vs session cookies vs OAuth (security analysis, threat models)"
- "State management: Redux vs Zustand vs Jotai (bundle size, DX, performance)"

### When to Keep in WORKLOG

**❌ Keep in WORKLOG when:**
- Decision is **straightforward** (~500 chars explains it fully)
- Following **established patterns** from ADRs or guidelines
- **Implementation details** without alternative approaches
- Quick **gotchas or lessons** learned during coding
- Single-option decisions ("used the recommended approach")

**❌ Examples NOT needing RESEARCH.md:**
- "Used React hooks instead of class components (team standard)"
- "Fixed off-by-one error in pagination logic"
- "Added input validation per security-guidelines.md"
- "Used bcrypt for password hashing (per ADR-003)"
- "Followed REST naming conventions from api-guidelines.md"

---

## RESEARCH.md File Structure

### Overall File Format

**File Location**: `pm/issues/TASK-###-name/RESEARCH.md` or `pm/issues/BUG-###-name/RESEARCH.md`

**Format**:
```markdown
# Technical Research

## #section-anchor - Decision Name

### Problem
[What decision needed to be made - context and constraints]

### Alternatives Considered

**Option 1: Name**
- Pros: [Advantages]
- Cons: [Disadvantages]
- Benchmark/Data: [Quantitative results if applicable]

**Option 2: Name**
- Pros: [Advantages]
- Cons: [Disadvantages]
- Benchmark/Data: [Quantitative results if applicable]

**Option 3: Name**
- Pros: [Advantages]
- Cons: [Disadvantages]
- Benchmark/Data: [Quantitative results if applicable]

### Decision
Selected **Option X** because:
1. [Primary reason with evidence]
2. [Secondary reason with justification]
3. [Trade-offs accepted and why]

### Implementation
[How the decision was implemented - key details]

### References
- [Links to benchmarks, ADRs, external docs]
```

### Key Elements

**Anchor IDs**: Use `##` headings with `#kebab-case-anchor` format for easy WORKLOG references
```markdown
## #caching-strategy - Redis vs Memcached Selection
## #auth-approach - JWT vs Session Cookies Analysis
## #memory-leak - Database Connection Pool Investigation
```

**Problem Statement**: Clear context about what decision needed to be made and constraints
```markdown
### Problem
Need sub-10ms cache response times for user session data at 10K req/sec.
Budget constraint: <$500/month infrastructure.
```

**Alternatives**: Each option with pros/cons and data (benchmarks, metrics, examples)
```markdown
**Option 1: Redis**
- Pros: Persistence, pub/sub, rich data structures (sorted sets, hashes)
- Cons: Higher memory usage (+30% vs Memcached), slightly slower
- Benchmark: 8ms avg latency @ 10K req/sec, 99th percentile: 15ms
- Cost: $450/month (AWS ElastiCache r6g.large cluster)
```

**Decision Rationale**: Why this choice was made with justification and trade-offs
```markdown
### Decision
Selected **Redis** despite slower benchmarks because:
1. Persistence protects against cold-start issues (10K sessions lost = bad UX)
2. 8ms still well under 10ms SLA requirement (20% margin)
3. Pub/sub enables real-time features later (roadmap: EPIC-005 notifications)
4. Sorted sets simplify leaderboard implementation (TASK-015)
5. Trade-off: Accept +$150/month cost for future flexibility
```

**Implementation Details**: How the decision was implemented in practice
```markdown
### Implementation
- Redis Cluster (3 nodes, replication factor 2)
- Connection pooling (min: 10, max: 50, idle timeout: 30s)
- Eviction policy: allkeys-lru
- Monitoring: CloudWatch alarms on memory usage >80%
- Fallback: Read-through cache pattern with PostgreSQL
```

**References**: Links to benchmarks, ADRs, code, external documentation
```markdown
### References
- Benchmark code: `/benchmarks/cache-comparison/` (see README for reproduction)
- Architecture discussion: docs/project/adrs/ADR-003-caching-strategy.md
- Redis docs: https://redis.io/docs/manual/eviction/
- Cost analysis spreadsheet: docs/project/cost-analysis.xlsx
```

---

## RESEARCH.md Examples

### Example 1: Technology Selection

```markdown
# Technical Research

## #caching-strategy - Redis vs Memcached Selection

### Problem
Need sub-10ms cache response times for user session data at 10K req/sec.
Current approach: PostgreSQL queries averaging 45ms, causing slow page loads.
Budget constraint: <$500/month infrastructure.

### Alternatives Considered

**Option 1: Redis**
- Pros: Persistence, pub/sub, data structures (sorted sets, hashes, lists)
- Cons: Slightly slower, more memory usage (+30% vs Memcached)
- Benchmark: 8ms avg latency @ 10K req/sec, 99th percentile: 15ms
- Cost: $450/month (AWS ElastiCache r6g.large, 3-node cluster)

**Option 2: Memcached**
- Pros: Fastest, simple, less memory, lower cost
- Cons: No persistence, cache-only, limited data types
- Benchmark: 5ms avg latency @ 10K req/sec, 99th percentile: 10ms
- Cost: $300/month (AWS ElastiCache cache.r6g.large, 2-node cluster)

**Option 3: In-memory (Node.js Map)**
- Pros: Fastest, no network overhead, no cost
- Cons: Not shared across instances, memory limits per container
- Benchmark: 1ms avg latency (local access)
- Cost: $0 (runs in app servers)

### Decision
Selected **Redis** despite slower benchmarks and higher cost because:
1. Persistence protects against cold-start issues (10K sessions lost = 30% user churn spike)
2. 8ms still well under 10ms SLA requirement (20% margin)
3. Pub/sub enables real-time features later (EPIC-005: Live notifications, Q2 roadmap)
4. Sorted sets simplify leaderboard implementation (TASK-015 due next sprint)
5. Horizontal scaling path: Add read replicas if needed
6. Trade-off: Accept +$150/month cost and 3ms slower for future flexibility

### Implementation
- **Infrastructure**: Redis Cluster (3 nodes, replication factor 2)
- **Connection**: ioredis client with pooling (min: 10, max: 50, idle timeout: 30s)
- **Eviction**: allkeys-lru policy (session TTL: 30 days, active users retained)
- **Monitoring**: CloudWatch alarms on memory >80%, latency >10ms
- **Fallback**: Read-through cache pattern with PostgreSQL (stale-while-revalidate)
- **Deployment**: Terraform module in `infrastructure/redis/`

### References
- Benchmark code: `/benchmarks/cache-comparison/README.md` (reproduction steps)
- ADR: docs/project/adrs/ADR-003-caching-strategy.md
- Redis eviction docs: https://redis.io/docs/manual/eviction/
- Cost analysis: docs/project/infrastructure-costs.xlsx (tab: Caching Options)
- Load test results: `/benchmarks/cache-comparison/results/report-2025-01-15.html`
```

### Example 2: Bug Root Cause Analysis

```markdown
# Technical Research

## #memory-leak - Database Connection Pool Investigation

### Problem
Production memory usage grew from 512MB to 8GB over 72 hours, causing OOM crashes.
Pattern: Memory increased linearly with request volume, didn't decrease during off-peak.
Affected: 3 production instances (us-east-1a, us-east-1b, us-east-1c).

### Investigation

**Hypothesis 1: Memory leak in Redis client**
- Tested: Added heap snapshots every 15 minutes
- Result: ❌ Redis connections stable at ~50 (within pool limits)
- Evidence: Heap dump showed no retained Redis objects

**Hypothesis 2: Unclosed database connections**
- Tested: Monitored PostgreSQL `pg_stat_activity`, added connection logging
- Result: ✅ Found 1,200+ IDLE connections (max_connections: 100)
- Evidence: Connections opened but never released, query timeouts left hanging

**Hypothesis 3: Event emitter memory leak**
- Tested: Checked EventEmitter listener counts
- Result: ❌ No excessive listeners (max: 10 per emitter)

### Root Cause
**Database connection pool not releasing connections on query timeout.**

**Specific issue**: Using `pg` library with manual connection management, forgot to call `client.release()` in error handlers.

**Code location**: `src/database/queries.ts:45-67` (user lookup queries)

**Pattern**:
```typescript
// Broken code (before fix)
const client = await pool.connect();
try {
  const result = await client.query(sql);
  return result.rows;
} finally {
  // ❌ Missing: client.release() in error cases
}
```

**Why it leaked**:
1. Query timeout throws error (default: 30s)
2. Error bypasses `finally` block if connection already dead
3. Connection never returned to pool
4. Pool creates new connection for next request
5. Old connections accumulate in IDLE state

### Decision
**Fix**: Always use connection pool's `.query()` method (auto-releases) instead of manual `.connect()`.

**Reason**: pg library's pool.query() handles connection lifecycle automatically, including error cases.

### Implementation
**Changes**:
1. Replaced manual connection management with `pool.query()` in 15 files
2. Added ESLint rule: `no-restricted-syntax` for `pool.connect()` usage
3. Added integration test: Query timeout doesn't leak connections
4. Added monitoring: CloudWatch alarm on PostgreSQL connection count >80

**Code after fix**:
```typescript
// Fixed code
const result = await pool.query(sql);  // Auto-releases even on error
return result.rows;
```

**Files modified**: `src/database/*.ts` (see commit SHA: abc123def)

**Monitoring**:
- Metric: `postgresql.connections.idle` (threshold: 50)
- Alert: PagerDuty if idle connections >50 for 5 minutes

### References
- Heap dumps: `/debugging/heap-dumps-2025-01-14/`
- pg library docs: https://node-postgres.com/features/pooling
- ESLint rule: `.eslintrc.js:45` (no-restricted-syntax)
- Monitoring dashboard: https://cloudwatch.aws.amazon.com/dashboard/prod-db
- Incident postmortem: docs/incidents/2025-01-14-memory-leak.md
```

---

## Linking RESEARCH.md from WORKLOG

### Reference Pattern

**In WORKLOG entries**, reference RESEARCH.md sections using anchor links:

```markdown
## 2025-01-15 16:45 - [AUTHOR: backend-specialist] (Phase 2.2 COMPLETE)

Implemented Redis caching layer. See RESEARCH.md #caching-strategy for full analysis
(Redis vs Memcached vs in-memory comparison with benchmarks).

Files: src/cache/redis.ts, tests/cache.test.ts
```

### Anchor Link Format

**Pattern**: `RESEARCH.md #section-anchor`

**Examples**:
- `See RESEARCH.md #caching-strategy for decision rationale`
- `Root cause documented in RESEARCH.md #memory-leak`
- `Alternative approaches in RESEARCH.md #auth-approach`

**Why use anchors**: Allows direct linking to specific sections within RESEARCH.md as it grows with multiple decision records.

---

## Multiple Decisions in Same RESEARCH.md

**Format**: Add new sections with unique anchor IDs

```markdown
# Technical Research

## #caching-strategy - Redis Selection
[... full section ...]

---

## #auth-tokens - JWT vs Session Cookies
[... full section ...]

---

## #database-indexing - Composite Index Strategy
[... full section ...]
```

**Separator**: Use `---` (horizontal rule) between sections for visual clarity

**Ordering**: Add new sections at BOTTOM (chronological order, unlike WORKLOG which is reverse chronological)

---

## When to Create ADR Instead

**Use ADR (Architecture Decision Record) when:**
- Decision affects **multiple projects or teams** (not just current task)
- Decision sets **precedent for future work** (establishes pattern)
- Decision has **long-term consequences** (hard to reverse)
- Decision requires **team consensus** (not implementation detail)

**Use RESEARCH.md when:**
- Decision is **task-specific** (isolated to current work)
- Decision is **exploratory** (spike, POC, experiment)
- Decision is **implementation detail** (doesn't set precedent)
- Decision is **reversible** (can change later without impact)

**Example boundaries**:
- "Use Redis for session storage in this service" → RESEARCH.md (task-specific)
- "Adopt Redis as standard cache for all services" → ADR (company-wide pattern)
- "Implement JWT authentication for this API" → RESEARCH.md (single service)
- "Adopt OAuth as company authentication standard" → ADR (affects all services)

**See**: `docs/project/adrs/README.md` for ADR format and when to use ADRs vs RESEARCH.md

---

## Related Documentation

**For WORKLOG entries**:
- See `worklog-format.md` for WORKLOG entry formats
- See `worklog-format.md` for how to reference RESEARCH.md

**For planning context**:
- See `plan-structure.md` for when to create RESEARCH.md during implementation
- See `plan-structure.md` for progress tracking with RESEARCH references

**For file structure**:
- See `issue-management.md` for complete issue directory layout
- See `issue-management.md` for TASK.md, BUG.md formats

**For decision records**:
- See `docs/project/adrs/README.md` for ADR format
- See `docs/project/adrs/template.md` for ADR vs RESEARCH.md guidance
