---
last_updated: "2025-11-24"
description: "Time-boxed technical exploration workflow to answer feasibility and approach questions"
---

# Spike Workflow

Time-boxed technical exploration to answer "Can we?" or "Which approach?" questions before implementation.

## What is a Spike?

A **spike** is a time-boxed research and prototyping activity that produces **knowledge and findings**, not production code. Spikes explore technical feasibility, compare alternatives, and inform architectural decisions before committing to implementation.

### Spike vs Task

| Aspect | TASK | SPIKE |
|--------|------|-------|
| **Goal** | Deliver feature | Answer question |
| **Output** | Production code | Findings + recommendation |
| **Code** | Merged to main | Throwaway prototypes |
| **Success** | Acceptance criteria met | Questions answered |
| **Scope** | Feature-driven | Time-boxed (must end) |
| **Planning** | PLAN.md with phases | Multiple PLAN-*.md for approaches |
| **When** | After spike complete | Before task planning |

### Spike vs Troubleshooting

| Aspect | SPIKE | BUG/Troubleshoot |
|--------|-------|------------------|
| **Goal** | Explore before building | Fix broken code |
| **Output** | Recommendation | Bug fix |
| **Code Status** | No code exists yet | Production code broken |
| **When** | Proactive (prevent mistakes) | Reactive (something broke) |

## When to Use Spikes

### Use Spike When

✅ **Technical approach is uncertain**
- "Should we use GraphQL or REST?"
- "Which state management library fits our needs?"

✅ **Need to compare 2+ alternatives**
- "Library A vs Library B - which performs better?"
- "Client-side caching vs server-side - which is simpler?"

✅ **Feasibility is unknown**
- "Can we integrate with this legacy system?"
- "Is real-time sync possible with our constraints?"

✅ **High risk that prototyping would reduce**
- "This approach seems complex - let's prototype first"
- "Never used this tech - need hands-on before committing"

✅ **Learning new technology**
- "Team hasn't used WebSockets - need spike before production"
- "New framework version has breaking changes - explore migration path"

### Don't Use Spike When

❌ **Approach is clear from requirements**
- Standard CRUD endpoints
- Well-understood patterns team has done before

❌ **Team has done similar work**
- "We've built similar features 3 times"
- Existing patterns can be reused directly

❌ **Just need to read documentation**
- API documentation answers all questions
- No hands-on exploration needed

❌ **Uncertainty is in requirements, not technology**
- Use `/spec` to clarify requirements
- Spike won't help with unclear business needs

## Complete Workflow

### 1. Create Spike

```bash
/spike "Should we use GraphQL or REST for our API?"
```

**Interactive prompts:**
- What questions need to be answered?
- Time box (max hours)? → `8 hours`
- Deadline? → `2025-11-25`
- Related spec (if any)? → `SPEC-003`
- How many approaches to explore? → `2`

**Creates:** `pm/issues/SPIKE-001-graphql-vs-rest/SPIKE.md`

### 2. Create Exploration Plans

```bash
/plan --spike SPIKE-001
```

**Interactive prompts:**
- How many approaches to explore? → `2` (AI suggests based on SPIKE.md)
- Approach 1 description? → `GraphQL with Apollo Server`
- Approach 2 description? → `REST with Express`

**Creates:**
- `pm/issues/SPIKE-001-graphql-vs-rest/PLAN-1.md` (GraphQL exploration plan)
- `pm/issues/SPIKE-001-graphql-vs-rest/PLAN-2.md` (REST exploration plan)

### 3. Execute Explorations

```bash
/implement --spike SPIKE-001 --plan 1
/implement --spike SPIKE-001 --plan 2
```

**Also supports:**
```bash
/implement --spike SPIKE-001 --next  # Sequential exploration
```

**Behavior:**
- Uses existing `/implement` workflow
- Creates WORKLOG-1.md and WORKLOG-2.md
- Tracks exploration progress with Phase Commits
- **Does NOT commit changes** (--spike flag prevents commits)
- Code goes in `prototype/` directory

### 4. Complete Spike

```bash
/spike --complete SPIKE-001
```

**Workflow:**
1. AI reads all WORKLOG-*.md files
2. AI generates draft SPIKE-SUMMARY.md
3. User refines summary
4. AI asks: "Keep prototype code for reference?" (yes/no)
5. AI asks: "Create ADR?" → yes (creates ADR-004)
6. AI asks: "Update SPEC-003?" → yes (adds technical approach)
7. AI asks: "Create implementation tasks?" → yes (creates TASK-015)
8. Commits spike documentation to git

## File Templates

### SPIKE.md Template

**Template file**: `docs/development/templates/spike-template.md`

The SPIKE.md template follows the same structure as TASK.md and BUG.md templates with:
- Structured frontmatter for template metadata
- Conversational prompts for each section
- Footer with next step guidance

**Example instance** (SPIKE-001: GraphQL vs REST):

```markdown
---
id: "SPIKE-001"
title: "Should we use GraphQL or REST for our API?"
status: "in_progress"
related_spec: "SPEC-003"
created: "2025-11-19"
---

# SPIKE-001: Should we use GraphQL or REST for our API?

## Questions to Answer

1. What is the performance difference for our use case?
2. Which has better caching characteristics?
3. What is the team learning curve?
4. What are the tooling/library options?
5. Which approach better fits our existing infrastructure?

## Time Box

**Maximum time**: 8 hours
**Deadline**: 2025-11-22
**Actual time**: 0 hours

## Approaches to Explore

### Approach 1: GraphQL with Apollo Server
**PLAN**: See PLAN-1.md
**WORKLOG**: See WORKLOG-1.md

### Approach 2: REST with Express
**PLAN**: See PLAN-2.md
**WORKLOG**: See WORKLOG-2.md

## Success Criteria

- [ ] Both approaches prototyped with minimal working examples
- [ ] Performance benchmarks completed under realistic load
- [ ] Caching strategies documented for both
- [ ] Team learning curve assessed
- [ ] Clear recommendation made with rationale

## Related Work

**Related Spec**: SPEC-003 - API Design for Mobile App

---

**Exploration Plans**: Run `/plan --spike SPIKE-001` to create exploration plans for each approach.
**See**: `docs/development/workflows/spike-workflow.md` for complete spike methodology.
```

### PLAN-N.md Template (Spike Reminder)

```markdown
---
issue_id: "SPIKE-001"
plan_number: 1
approach: "GraphQL with Apollo Server"
created: "2025-11-19"
status: "pending"
---

# SPIKE-001 Exploration Plan 1: GraphQL with Apollo Server

> **⚠️ SPIKE EXPLORATION**
>
> This is exploratory work for SPIKE-001. Code will NOT be committed to production.
> Use `/implement --spike SPIKE-001 --plan 1` to execute this plan.
> Track discoveries in WORKLOG-1.md, not production deliverables.

## Exploration Goal

Prototype a GraphQL API with Apollo Server to evaluate:
- Setup complexity
- Query flexibility benefits
- Performance under load
- Caching strategies

## Phase 1: Setup (Estimated: 1 hour)

**Objective:** Get minimal GraphQL server running

### Steps
- [ ] Install Apollo Server and GraphQL dependencies
- [ ] Create basic schema (User, Post types)
- [ ] Set up server with single query endpoint
- [ ] Test with GraphQL Playground

**Success criteria:**
- GraphQL server responds to basic queries
- Schema includes representative types for our domain

## Phase 2: Implement Representative Queries (Estimated: 2 hours)

**Objective:** Build queries that mirror our actual use cases

### Steps
- [ ] Implement nested queries (user with posts)
- [ ] Add query parameters (filtering, pagination)
- [ ] Implement data loaders for N+1 prevention
- [ ] Test query flexibility

**Success criteria:**
- Queries match our real-world API needs
- No N+1 query problems
- Query flexibility demonstrated

## Phase 3: Performance Testing (Estimated: 1 hour)

**Objective:** Benchmark under realistic load

### Steps
- [ ] Set up load testing tool (k6, artillery)
- [ ] Run benchmarks with 100 concurrent users
- [ ] Measure response times, throughput
- [ ] Document results in WORKLOG-1.md

**Success criteria:**
- Performance metrics captured
- Compared against requirements

## Phase 4: Evaluate Findings (Estimated: 30 min)

**Objective:** Document learnings

### Steps
- [ ] Review Phase Commits in WORKLOG-1.md
- [ ] Document setup complexity vs benefits
- [ ] Note surprises and gotchas
- [ ] Prepare findings for summary

**Success criteria:**
- All findings documented in WORKLOG-1.md
- Ready for comparison with other approaches

## Notes

- Keep code simple - this is exploration, not production
- Focus on answering questions, not building features
- Document surprises and gotchas in WORKLOG
- All code goes in `prototype/graphql/`
- Remember: This code will likely be discarded
```

### WORKLOG-N.md for Spikes

Track **exploration discoveries**, not implementation steps:

```markdown
# SPIKE-001 Exploration 1: GraphQL Worklog

## Phase Commits
- Phase 1 (Setup): `abc123d` - GraphQL server with Apollo Server
- Phase 2 (Queries): `def456e` - Implemented nested queries with data loaders
- Phase 3 (Performance): `ghi789f` - Added benchmarking with k6

## 2025-11-19 - Initial GraphQL Setup (Phase 1)

**What I explored**: Built minimal GraphQL server with Apollo Server

**Findings:**
- Setup took 2 hours vs estimated 1 hour
- Apollo Server has steep learning curve
- Schema definition is verbose but type-safe
- GraphQL Playground excellent for testing

**Questions surfaced:**
- Do we need query flexibility? (Asked PM - no, endpoints are well-defined)
- Is the setup complexity worth it? (TBD after comparing with REST)

**Surprises:**
- Data loader pattern required to prevent N+1
- More boilerplate than expected

**Time spent**: 2h (6h remaining in time box)

## 2025-11-19 - Implemented Representative Queries (Phase 2)

**What I explored**: Built nested queries matching our use cases

**Findings:**
- Nested queries work well (user + posts in single request)
- Data loaders add complexity but necessary for performance
- Query flexibility impressive but may be overkill for our use case
- Type safety excellent (caught several errors early)

**Performance notes:**
- Single query for nested data (vs multiple REST calls)
- But requires data loader setup per type

**Time spent**: 2.5h (3.5h remaining)

## 2025-11-20 - Performance Benchmarking (Phase 3)

**What I explored**: Load tested with k6 (100 concurrent users)

**Findings:**
- Nested query avg: 45ms (impressive!)
- Simple query avg: 52ms (REST likely faster here due to overhead)
- GraphQL parsing/validation adds ~7ms overhead
- Excellent performance for complex nested queries

**Benchmark results:**
- 100 concurrent users: 45ms avg response time
- Throughput: 2,200 req/sec
- No errors under load

**Time spent**: 1h (2.5h remaining)

---

**Total time spent**: 5.5 hours
**Time remaining**: 2.5 hours
**Ready for summary**: Waiting for REST exploration completion
```

### SPIKE-SUMMARY.md Template

```markdown
---
spike_id: "SPIKE-001"
title: "Should we use GraphQL or REST for our API?"
completed: "2025-11-20"
time_spent: "7.5 hours"
approaches_explored: 2
recommendation: "REST"
---

# SPIKE-001 Summary: GraphQL vs REST API

## Executive Summary

**Recommendation: Use REST with Express**

After exploring both GraphQL (Approach 1) and REST (Approach 2), we recommend REST for our API based on:
- Simpler setup and maintenance (2 hours vs 5 hours to working prototype)
- Adequate performance (5% slower than GraphQL, within acceptable range)
- Lower team learning curve (team already familiar with REST patterns)
- Better fit for our use case (well-defined endpoints, limited need for query flexibility)

## Questions Answered

### 1. Performance Difference

**Finding:** GraphQL was 5% faster for complex nested queries, REST was 10% faster for simple queries.

**Details:**
- GraphQL: Avg 45ms for nested user+posts query (100 concurrent users)
- REST: Avg 48ms for equivalent endpoint
- Simple queries: REST faster due to less overhead (GraphQL parsing/validation)

**Source:** WORKLOG-1.md (Phase 3), WORKLOG-2.md (Phase 3)

### 2. Caching Characteristics

**Finding:** REST has simpler, more predictable caching.

**Details:**
- REST: Standard HTTP caching works out of the box (Cache-Control, ETags)
- GraphQL: Requires custom cache key generation based on query structure
- GraphQL cache invalidation more complex due to flexible queries

**Source:** WORKLOG-1.md (Phase 4), WORKLOG-2.md (Phase 4)

### 3. Team Learning Curve

**Finding:** REST requires zero ramp-up, GraphQL needs 2-3 weeks.

**Details:**
- Team already proficient with REST/Express patterns
- GraphQL requires learning: schema design, resolvers, data loaders, caching strategies
- Estimated 2-3 weeks for team to become productive with GraphQL

**Source:** Team discussion, prototype complexity analysis

### 4. Tooling/Library Options

**Finding:** Both have mature ecosystems, GraphQL more specialized.

**Details:**
- REST: Express, Fastify, Koa (team familiar)
- GraphQL: Apollo Server (feature-rich but heavy), GraphQL Yoga (lighter)
- Both have excellent TypeScript support

**Source:** WORKLOG-1.md (Phase 1), WORKLOG-2.md (Phase 1)

### 5. Infrastructure Fit

**Finding:** REST fits existing infrastructure better.

**Details:**
- Current API gateway optimized for REST
- Monitoring/logging tools understand REST endpoints
- GraphQL would require custom instrumentation

**Source:** Infrastructure review during exploration

## Approach Comparison

| Criteria | GraphQL | REST | Winner |
|----------|---------|------|--------|
| Setup complexity | High (5h to prototype) | Low (2h to prototype) | REST |
| Performance (nested) | 45ms avg | 48ms avg | GraphQL |
| Performance (simple) | 52ms avg | 47ms avg | REST |
| Caching | Complex (custom) | Simple (HTTP standard) | REST |
| Learning curve | 2-3 weeks | 0 (familiar) | REST |
| Query flexibility | High | Low | GraphQL |
| Infrastructure fit | Requires changes | Drop-in | REST |

## Recommendation Rationale

**Choose REST because:**

1. **Team velocity** - Zero learning curve means immediate productivity
2. **Adequate performance** - 5% slower for nested queries is acceptable
3. **Simpler operations** - Standard HTTP caching, familiar patterns
4. **Better fit** - Our API has well-defined endpoints; GraphQL flexibility not needed
5. **Lower risk** - Proven with our infrastructure

**GraphQL would be better if:**
- We needed highly flexible queries (we don't - endpoints are well-defined)
- We were starting fresh (we have existing REST infrastructure)
- Team was already GraphQL-proficient (they're not)
- Performance difference was significant (it's marginal)

## Prototype Code Status

**Location:** `pm/issues/SPIKE-001-graphql-vs-rest/prototype/`

**Kept for reference:** Yes
- GraphQL prototype: `prototype/graphql/` (Apollo Server example)
- REST prototype: `prototype/rest/` (Express example)
- Benchmarking scripts: `prototype/benchmarks/`

**Note:** This code is for reference only, not production use.

## Next Steps

### Immediate Actions
1. **Create ADR** - Document REST decision with spike findings as evidence
2. **Update SPEC-003** - Add technical approach section referencing this spike
3. **Create implementation tasks** - TASK-015: Implement REST API endpoints

### Follow-up Tasks
- [ ] TASK-015: Implement REST API with Express (reference PLAN-2.md for patterns)
- [ ] TASK-016: Set up HTTP caching strategy (use findings from WORKLOG-2.md)
- [ ] TASK-017: Add API documentation with OpenAPI/Swagger

### Architecture Decisions
- [ ] ADR-004: Use REST over GraphQL for API (reference SPIKE-001)

## References

- **Plans**: PLAN-1.md (GraphQL), PLAN-2.md (REST)
- **Work logs**: WORKLOG-1.md (GraphQL exploration), WORKLOG-2.md (REST exploration)
- **Related spec**: SPEC-003 - API Design for Mobile App
- **Time spent**: 7.5 hours (within 8-hour time box)

## Lessons Learned

1. **Setup time matters** - GraphQL's 2.5x longer setup time was first red flag
2. **Query flexibility overrated** - Our endpoints are well-defined; flexibility unused
3. **Caching complexity** - GraphQL caching proved more complex than expected
4. **Infrastructure fit critical** - Integration with existing tools heavily favored REST
5. **Team familiarity valuable** - Zero ramp-up time is a significant advantage

---

**Spike completed:** 2025-11-20
**Total time:** 7.5 hours (0.5 hours under budget)
**Recommendation confidence:** High
```

## Integration with Other Commands

### /spec Integration

When creating specs with technical uncertainty:

```bash
/spec
# AI detects uncertainty: "Which API pattern should we use?"
# AI suggests: "Consider creating a spike to explore GraphQL vs REST"
```

SPEC.md references spike after completion:

```markdown
## Technical Approach

**Approach determined by:** SPIKE-001 (GraphQL vs REST evaluation)
**Recommendation:** REST with Express
**Rationale:** See pm/issues/SPIKE-001-graphql-vs-rest/SPIKE-SUMMARY.md

REST chosen based on:
- Simpler setup and maintenance
- Team familiarity (zero ramp-up)
- Adequate performance (5% slower acceptable)
- Better infrastructure fit
```

### /plan Integration

**If task references incomplete spike:**

```bash
/plan TASK-015
# Error: "SPIKE-001 (referenced in TASK-015) is incomplete."
# Suggestion: "Run: /spike --complete SPIKE-001"
```

**If spike complete, plan references findings:**

```markdown
## Phase 1: API Setup

**Reference:** SPIKE-001 PLAN-2.md and WORKLOG-2.md

**Patterns to use from spike:**
- Express server setup (see prototype/rest/server.js)
- Route organization (see prototype/rest/routes.js)
- Error handling middleware

**Key learnings from spike:**
- Keep routes simple and RESTful
- Use standard HTTP caching (Cache-Control headers)
- OpenAPI documentation from start
```

### /plan --spike for Explorations

Create exploration plans for spike:

```bash
/plan --spike SPIKE-001
```

**Interactive workflow:**
1. Reads SPIKE.md to understand approaches
2. AI suggests number of approaches based on SPIKE.md content
3. For each approach, creates PLAN-N.md with:
   - Spike reminder at top
   - Exploration phases (not implementation phases)
   - Focus on answering questions
   - Prototype code location
4. Creates numbered PLAN-1.md, PLAN-2.md, etc.

### /implement --spike for Exploration

Execute exploration without commits:

```bash
/implement --spike SPIKE-001 --plan 1
/implement --spike SPIKE-001 --plan 2
```

**Also supports:**
```bash
/implement --spike SPIKE-001 --next  # Sequential exploration
```

**Behavior differences with --spike flag:**
- NO git commits during exploration
- Code goes in `prototype/` directory
- WORKLOG-N.md tracks discoveries (not deliverables)
- Phase Commits section tracks prototype commits
- Reminder to user: "This is exploration - code won't be committed"

### /adr Integration

Spike findings become ADR evidence:

```bash
/adr "Use REST over GraphQL for API"
# AI prompts: "Reference SPIKE-001 findings? (yes/no)"
# If yes, AI includes:
#   - Link to SPIKE-001-SUMMARY.md
#   - Key findings from spike
#   - Comparison data from spike
```

ADR example referencing spike:

```markdown
# ADR-004: Use REST over GraphQL for API

## Status
Accepted

## Context
SPEC-003 requires an API for mobile app integration. Two approaches were considered: GraphQL and REST.

**Spike conducted:** SPIKE-001 (GraphQL vs REST evaluation)
**Findings:** See pm/issues/SPIKE-001-graphql-vs-rest/SPIKE-SUMMARY.md

## Decision
Use REST with Express for our API.

## Rationale

Based on SPIKE-001 findings:
- **Performance**: REST 5% slower for nested queries (acceptable)
- **Simplicity**: 2h setup vs 5h for GraphQL
- **Team velocity**: Zero learning curve (familiar with REST)
- **Infrastructure fit**: Works with existing API gateway

Full comparison: See SPIKE-001 comparison table

## Consequences

**Positive:**
- Immediate productivity (no learning curve)
- Standard HTTP caching works out of box
- Familiar patterns for maintenance

**Negative:**
- Multiple requests for nested data (acceptable tradeoff)
- Less query flexibility (not needed for our use case)

**Reference:** SPIKE-001-SUMMARY.md for complete findings
```

## Best Practices

### Time Boxing

**Strict time limits:**
- Set realistic time box (4-8 hours common)
- Track actual time spent
- Must end even if questions unanswered
- Document what wasn't explored if time runs out

**Example:**
```markdown
## Time Box

**Maximum time**: 8 hours
**Deadline**: 2025-11-22
**Actual time**: 7.5 hours (0.5 hours under budget)

**Not explored due to time:**
- Advanced caching strategies (deprioritized)
- WebSocket alternative (out of scope)
```

### Multiple Approaches

**Parallel exploration:**
- Explore 2-3 approaches maximum
- Each approach gets own PLAN-N.md and WORKLOG-N.md
- Can work sequentially or in parallel
- Compare findings in SPIKE-SUMMARY.md

**When to explore multiple approaches:**
- ✅ Two viable alternatives exist
- ✅ Trade-offs need empirical data
- ✅ Team divided on approach
- ❌ Don't explore 5+ approaches (too much overhead)

### Findings Documentation

**Record in WORKLOG-N.md as you go:**
- Setup time (actual vs estimated)
- Surprises and gotchas
- Questions that surface
- Performance data
- Complexity observations

**Don't wait until end to document - you'll forget details!**

### Prototype Code

**Keep code minimal:**
- Just enough to answer questions
- Don't build features
- Don't worry about production quality
- Focus on learning, not delivery

**Example:**
```javascript
// prototype/graphql/server.js
// SPIKE EXPLORATION CODE - NOT PRODUCTION READY
// Purpose: Test GraphQL setup complexity and performance

const { ApolloServer, gql } = require('apollo-server');

// Minimal schema to test nested queries
const typeDefs = gql`
  type User {
    id: ID!
    name: String!
    posts: [Post!]!
  }

  type Post {
    id: ID!
    title: String!
  }

  type Query {
    user(id: ID!): User
  }
`;

// ... minimal resolvers to answer spike questions
```

### Follow-up Actions

**Always create follow-ups:**
- ADR to document decision
- Update spec with technical approach
- Create implementation tasks
- Archive/delete prototype code

**Don't leave spike orphaned - it should produce actionable output!**

## Common Pitfalls

### ❌ Analysis Paralysis

**Problem:** Spike never ends, keeps exploring "one more thing"

**Solution:**
- Strict time box enforcement
- Clear success criteria upfront
- Accept "good enough" answers

### ❌ Building Production Code

**Problem:** Prototype becomes production code, loses focus on learning

**Solution:**
- Keep code in `prototype/` directory
- Remind: "This will be discarded"
- Focus on answering questions, not building features

### ❌ Skipping Documentation

**Problem:** Findings only in developer's head, spike value lost

**Solution:**
- Document as you go in WORKLOG-N.md
- Generate SPIKE-SUMMARY.md before moving on
- Create ADR to preserve decision rationale

### ❌ No Follow-up Actions

**Problem:** Spike findings never used, work wasted

**Solution:**
- Always create ADR
- Update related specs
- Create implementation tasks
- Reference spike in plans

## Example Scenarios

### Scenario 1: Library Comparison

**Question:** "Which state management library - Redux vs Zustand?"

**Spike workflow:**
1. Create SPIKE-002: State Management Library Selection
2. Approaches: Redux, Zustand
3. Explore: Setup, boilerplate, devtools, performance
4. Findings: Zustand simpler (1h setup vs 3h), adequate for our needs
5. Recommendation: Zustand
6. Follow-up: ADR-005, update SPEC-004, create TASK-020

### Scenario 2: Feasibility Check

**Question:** "Can we integrate with legacy SOAP API?"

**Spike workflow:**
1. Create SPIKE-003: Legacy SOAP Integration Feasibility
2. Single approach: Node.js SOAP client
3. Explore: Authentication, data mapping, error handling
4. Findings: Possible but requires XML parsing complexity
5. Recommendation: Use if necessary, but explore REST alternative first
6. Follow-up: ADR-006, document integration approach in SPEC-005

### Scenario 3: Performance Validation

**Question:** "Can WebSockets handle our real-time requirements?"

**Spike workflow:**
1. Create SPIKE-004: WebSocket Performance Validation
2. Approaches: Socket.io, native WebSockets
3. Explore: Connection handling, message throughput, reconnection
4. Findings: Both handle 10k concurrent connections easily
5. Recommendation: Socket.io (better fallbacks, familiar API)
6. Follow-up: ADR-007, update SPEC-006, create TASK-025

## File Structure Reference

```
pm/issues/SPIKE-001-graphql-vs-rest/
├── SPIKE.md                    # Spike definition
├── PLAN-1.md                   # Exploration plan 1 (with spike reminder)
├── WORKLOG-1.md                # Exploration 1 findings
├── PLAN-2.md                   # Exploration plan 2 (with spike reminder)
├── WORKLOG-2.md                # Exploration 2 findings
├── SPIKE-SUMMARY.md            # Consolidated findings + recommendation
└── prototype/                   # Exploration code (optional)
    ├── graphql/
    │   ├── server.js
    │   └── schema.graphql
    ├── rest/
    │   ├── server.js
    │   └── routes.js
    └── benchmarks/
        └── load-test.js
```

## Related Workflows

- **pm-workflows.md** - PM artifact hierarchy, where spikes fit
- **implement.md** - `/implement --spike` flag behavior
- **plan.md** - `/plan --spike` flag behavior
- **adr-workflow.md** - Creating ADRs from spike findings
