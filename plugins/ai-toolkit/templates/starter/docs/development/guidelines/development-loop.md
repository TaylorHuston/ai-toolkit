---
# === Metadata ===
template_type: "guideline"
version: "1.0.0"
created: "2025-10-30"
last_updated: "2025-10-30"
status: "Active"
target_audience: ["AI Assistants", "Development Team"]
description: "AI-assisted development workflow with test-first approach, continuous code review, and agent coordination"

# === Development Loop Configuration (Machine-readable for AI agents) ===
loop_approach: "pragmatic-test-first" # pragmatic-test-first, strict-test-first, code-first
code_review_threshold: 90             # Minimum score to proceed (0-100)
test_coverage_target: 95              # Coverage percentage goal
review_frequency: "per-phase"         # per-phase, per-task, on-demand
worklog_required: true                # Require WORKLOG entries from agents
agent_handoff_protocol: "worklog"     # worklog, inline, none
quality_gate_enforcement: "strict"    # strict, flexible, advisory

# Quality Dimensions Configuration
quality_dimensions:
  enabled:
    - code_quality      # Complexity, maintainability, readability, duplication
    - security          # Vulnerabilities, compliance, secure coding practices
    - performance       # Speed, efficiency, resource usage, scalability
    - testing           # Coverage, effectiveness, reliability
    - documentation     # Completeness, accuracy, clarity
    - architecture      # Design patterns, separation of concerns, scalability

  # Dimension Details (what each dimension measures and which agent handles it)
  code_quality:
    metrics: [complexity, maintainability, readability, duplication]
    agent: code-reviewer
    approach: "Static analysis with AI-driven code quality assessment"

  security:
    metrics: [vulnerabilities, compliance, secure_coding, dependency_security]
    agent: security-auditor
    approach: "OWASP compliance checking with vulnerability scanning"

  performance:
    metrics: [speed, efficiency, resource_usage, scalability]
    agent: performance-optimizer
    approach: "Performance profiling with bottleneck analysis"

  testing:
    metrics: [coverage, effectiveness, reliability]
    agent: test-engineer
    approach: "AI-driven test analysis with coverage tool integration"

  documentation:
    metrics: [completeness, accuracy, clarity]
    agent: technical-writer
    approach: "Documentation completeness and accuracy validation"

  architecture:
    metrics: [design_patterns, separation_of_concerns, scalability, maintainability]
    agent: code-architect
    approach: "Architectural review with pattern compliance checking"

# Complexity Scoring Configuration (for task decomposition)
complexity_scoring:
  # Point values for complexity indicators
  indicators:
    multi_domain_integration: 3    # API + database, frontend + backend, UI + server
    security_implementation: 2     # Authentication, authorization, encryption, permissions
    database_schema_changes: 2     # Migrations, schema modifications, data transformations
    external_integrations: 2       # Third-party APIs, service connections, webhooks
    performance_optimization: 2    # Scaling, optimization, performance tuning
    ui_ux_implementation: 1        # Component creation, interface design, responsive work
    testing_requirements: 1        # Test creation, validation, quality assurance

  # Decomposition thresholds
  thresholds:
    high_complexity: 5      # ≥5 points: Suggest breaking into subtasks
    medium_complexity: 3    # 3-4 points: Consider decomposition based on timeline
    # ≤2 points: Task appropriately scoped
---

# Development Loop Guidelines

**Referenced by Commands:** `/implement`, `/plan`, `/quality`, `/test-fix`, `/comment`

## Quick Reference

This guideline defines our AI-assisted development workflow, emphasizing test-first development, continuous code review, and agent coordination through WORKLOG entries.

**Core Cycle**: Write Tests → Implement → Run Tests → Code Review → Iterate until 90+ score and tests pass

## Our Development Philosophy

**Pragmatic Test-First**: Test-first is the default when you know what to build (clear acceptance criteria, defined behavior). Code-first is appropriate when exploring unknowns (prototypes, unclear requirements, architecture scaffolding). The goal is confidence, not dogma.

**When You Know What to Build** → Write tests first (Red-Green-Refactor)
**When You're Discovering** → Code first, add tests once the approach is validated

**Quality Over Speed**: Code review threshold of 90+ ensures maintainable, well-structured code. Take time to get it right.

**Agent Coordination**: Specialists document their work in WORKLOG for context continuity. Each agent reads WORKLOG before starting to understand what's been accomplished.

**Continuous Improvement**: Each iteration through the loop improves both the code and our understanding of the problem.

## The Development Loop

### 1. Start with Tests (When Applicable)

**Pragmatic Test-First Approach**: Choose the right tool for the job.

**✅ Test-First (Preferred) - When you know what to build:**
- Feature has clear acceptance criteria from `/plan` output
- Phase objectives specify expected behavior
- Bug fix with reproducible failure scenario
- API endpoints or data transformations (clear inputs/outputs)
- Refactoring existing code (tests prevent regressions)

**📝 Code-First (Acceptable) - When discovering what to build:**
- Exploratory work or proof-of-concept (learning by doing)
- Architecture setup or scaffolding (establishing patterns)
- Initial project structure (no behavior to test yet)
- Unclear requirements that need discovery (spike first, then decide)
- Research tasks (document findings, then extract patterns)

**⚠️ Code-First Commitment**: If you code-first, **add tests before marking phase complete**. The development loop requires test coverage regardless of approach - the only difference is timing.

**Test-first process (Red-Green-Refactor):**
```bash
# Red: Write minimal failing tests that define success
# Verify tests fail for the right reason
# Green: Write minimal code to make tests pass
# Refactor: Improve code while tests stay green
```

### 2. Implement Minimal Code

Write only the code needed to pass the tests. Avoid:
- Gold-plating (features not in acceptance criteria)
- Premature optimization
- Speculative generalization
- Over-engineering

**Focus on:**
- Meeting acceptance criteria exactly
- Clear, readable code
- Proper error handling
- Following project coding standards

### 3. Run Tests

Execute full test suite appropriate for the phase:

```bash
npm test              # Run all tests
npm test:unit         # Unit tests only
npm test:integration  # Integration tests
npm test:e2e          # End-to-end tests
```

**Test execution rules:**
- All relevant tests must pass before proceeding
- No skipping or commenting out failing tests
- Address test failures immediately
- Verify coverage meets target (95% by default)

See `testing-standards.md` for framework-specific commands and test organization.

### 4. Code Review Process

**Invoke code-reviewer agent** after implementation:

```bash
# Code reviewer evaluates:
- Code quality and maintainability
- Adherence to coding-standards.md
- Best practices for the tech stack
- Security considerations
- Performance implications
- Test coverage and quality
```

**Scoring criteria:**
- **90-100**: Excellent - ready to proceed
- **80-89**: Good - minor improvements needed
- **70-79**: Acceptable - moderate refactoring needed
- **Below 70**: Requires significant rework

**Threshold**: 90+ required to proceed (configurable above)

See `coding-standards.md` for specific style and quality expectations.

### 5. Iterate Until Quality Gates Pass

**Quality gates** (all must pass):
- ✅ All tests pass
- ✅ Code review score ≥ 90
- ✅ Test coverage ≥ 95% (or project target)
- ✅ No critical security issues
- ✅ Acceptance criteria met

**Iteration process:**
1. Address code review feedback
2. Refactor based on suggestions
3. Re-run tests
4. Request re-review if major changes
5. Repeat until all gates pass

## Agent Coordination

### Specialist Agent Selection

**Always call the most qualified agent** for the task:

- **backend-specialist**: Server-side logic, APIs, middleware
- **frontend-specialist**: UI components, user interactions, responsive design
- **database-specialist**: Schema design, queries, migrations, performance
- **devops-engineer**: Infrastructure, deployment, CI/CD
- **test-engineer**: Test strategy, test creation, test maintenance
- **security-auditor**: Security review, vulnerability assessment
- **performance-optimizer**: Performance analysis, optimization
- **code-reviewer**: Code quality assessment, best practices review

### Agent Orchestration: Who Drives the Handoffs?

**The `/implement` command orchestrates all agent handoffs** following this pattern:

```
/implement TASK-### PHASE
  ↓
1. Command reads phase from PLAN.md
2. Command selects specialist based on phase domain
3. Specialist reads WORKLOG.md for context
4. Specialist executes phase following development loop
5. Specialist writes WORKLOG.md entry
6. Command invokes code-reviewer for quality check
7. If score < 90: Specialist iterates (back to step 4)
8. If score ≥ 90: Phase marked complete in PLAN.md
```

**Key points:**
- **Commands orchestrate, agents execute**: `/implement` decides which agent to invoke and when
- **Phase domain determines agent**: Backend phases → backend-specialist, frontend phases → frontend-specialist, etc.
- **WORKLOG.md enables handoffs**: Agents communicate through WORKLOG entries, not direct interaction
- **Iteration is command-driven**: `/implement` loops the specialist + code-reviewer cycle until quality gates pass

**Agent coordination metadata** (in agent YAML frontmatter):
```yaml
coordination:
  hands_off_to: [...]
  receives_from: [...]
  parallel_with: [...]
```

This metadata is **documentation only** - it helps humans understand typical agent relationships but is not read programmatically. The `/implement` command makes all orchestration decisions based on phase requirements, not this metadata.

### Agent Handoff Pattern in Practice

**Example: Implementing a backend phase**

```
User: /implement TASK-001 1.2

/implement command:
1. Reads TASK-001/PLAN.md → Phase 1.2: "Implement login endpoint"
2. Determines domain: backend → Selects backend-specialist
3. Invokes backend-specialist with phase context

backend-specialist:
4. Reads TASK-001/WORKLOG.md (sees phase 1.1 completed database setup)
5. Follows development loop: write tests → implement → run tests
6. Writes WORKLOG.md entry documenting login endpoint work (see Work Documentation section for format)

/implement command:
7. Invokes code-reviewer to assess implementation

code-reviewer:
8. Reviews code → Score: 85 ("Add input validation")

/implement command:
9. Score < 90 → Re-invokes backend-specialist with feedback

backend-specialist:
10. Addresses feedback, adds input validation
11. Re-runs tests
12. Updates WORKLOG.md entry

/implement command:
13. Re-invokes code-reviewer

code-reviewer:
14. Reviews code → Score: 92

/implement command:
15. Score ≥ 90 ✓ → Marks phase 1.2 complete
16. Updates TASK.md (checks off phase 1.2)
```

**Key insight**: The `/implement` command is the conductor - it reads, decides, invokes, validates, and loops. Agents are specialists that execute when invoked.

## Work Documentation

### File Purposes

Every issue directory (`pm/issues/TASK-###-name/` or `BUG-###-name/`) can contain three files:

**TASK.md / BUG.md** (WHAT to do):
- Primary issue file with plan checklist
- Acceptance criteria and phase breakdown
- Updated by `/plan` and `/implement` commands
- Status: Required, created by `/epic` or manually

**WORKLOG.md** (HOW it was done):
- Reverse chronological narrative work history
- Created automatically by `/implement` after each phase
- Documents: what was accomplished, lessons learned, gotchas, files changed
- Used by: AI agents to understand previous work, humans to review history
- Status: Auto-created during implementation

**RESEARCH.md** (WHY decisions were made):
- Deep technical investigations requiring multi-page analysis
- Created manually when complex decisions need detailed rationale
- Documents: alternatives considered, trade-offs, benchmarks, root cause analysis
- Referenced from WORKLOG entries for deeper context
- Status: Optional, created as needed

### WORKLOG Entry Format

**Structure** (reverse chronological - newest entries first):

```markdown
## YYYY-MM-DD HH:MM - agent-name

Summary of what was implemented (~500 chars max for scannable entries).

Gotcha: [Any unexpected issues, edge cases, or important discoveries]
Lesson: [What worked well, what to avoid, better approaches found]
Files: [key/files/changed.js, other/modified/file.ts]

See RESEARCH.md #section-name  # Optional: reference deep analysis if created
```

**Required Elements:**
- **Timestamp**: Always run `date '+%Y-%m-%d %H:%M'` - never estimate
- **Agent identifier**: Name of the agent that did the work (or @username for humans via `/comment`)
- **Summary**: What was done, implementation approach (~500 chars ideal)
- **Gotchas**: Unexpected issues, edge cases found, important discoveries
- **Lessons**: What worked, what to avoid, alternative approaches
- **Files**: Key files modified (helps locate code changes)
- **RESEARCH reference**: Optional link to detailed analysis if created

### WORKLOG Best Practices

1. **Keep entries scannable**: ~500 chars is ideal, can be longer for critical gotchas
2. **Focus on insights**: Document WHY things were done certain ways, not just WHAT
3. **Capture alternatives**: "Tried X but Y worked better because..." helps future work
4. **Reference deep dives**: "See RESEARCH.md #caching-strategy for full rationale"
5. **Write for the future**: Developers reading weeks/months later need context

**Good WORKLOG entry example:**
```markdown
## 2025-01-15 14:30 - backend-specialist

Implemented user authentication endpoint with JWT tokens. Used bcrypt for password
hashing (12 rounds) and Redis for token storage (24hr expiry).

Gotcha: Redis connection pooling required - single connection caused bottleneck under load
Lesson: JWT secret rotation strategy needed - added to TASK-002
Files: src/auth/login.ts, src/middleware/jwt.ts, tests/auth.test.ts

See RESEARCH.md #jwt-vs-session for storage decision rationale
```

### When to Create RESEARCH.md

**Create RESEARCH.md when decisions involve:**

✅ **Complex analysis requiring detailed documentation:**
- Evaluated **3+ alternatives** with detailed trade-off analysis
- Performed **benchmarks or performance testing** with data
- **Deep root cause analysis** for non-obvious bugs
- **Architecture decisions** affecting multiple components
- **Technical spikes** exploring multiple approaches
- **Security decisions** with threat modeling

✅ **Examples warranting RESEARCH.md:**
- "Evaluated PostgreSQL vs MongoDB vs Redis for session storage (6 criteria, benchmarks)"
- "Root cause: Memory leak from unclosed database connections in connection pool"
- "API architecture: REST vs GraphQL vs gRPC (performance tests, ecosystem analysis)"
- "Caching strategy: Redis vs Memcached vs in-memory (load testing results)"

❌ **Keep in WORKLOG when:**
- Decision is **straightforward** (~500 chars explains it fully)
- Following **established patterns** from ADRs or guidelines
- **Implementation details** without alternative approaches
- Quick **gotchas or lessons** learned during coding

❌ **Examples NOT needing RESEARCH.md:**
- "Used React hooks instead of class components (team standard)"
- "Fixed off-by-one error in pagination logic"
- "Added input validation per security-guidelines.md"

**Rule of thumb:**
- **Can explain in ~500 chars?** → WORKLOG entry only
- **Need multiple pages with data?** → Create RESEARCH.md section, reference from WORKLOG

### RESEARCH.md Structure

When creating RESEARCH.md, use clear sections with anchor-friendly IDs:

```markdown
# Technical Research

## #caching-strategy - Redis vs Memcached Selection

### Problem
Need sub-10ms cache response times for user session data at 10K req/sec.

### Alternatives Considered

**Option 1: Redis**
- Pros: Persistence, pub/sub, data structures
- Cons: Slightly slower, more memory
- Benchmark: 8ms avg latency

**Option 2: Memcached**
- Pros: Fastest, simple, less memory
- Cons: No persistence, cache-only
- Benchmark: 5ms avg latency

**Option 3: In-memory (Node.js)**
- Pros: Fastest, no network
- Cons: Not shared across instances, memory limits
- Benchmark: 1ms avg latency

### Decision
Selected **Redis** despite slower benchmarks because:
1. Persistence protects against cold-start issues (10K sessions lost = bad UX)
2. 8ms still well under 10ms SLA requirement
3. Pub/sub enables real-time features later (roadmap: EPIC-005)

### Implementation
- Redis Cluster (3 nodes, replication factor 2)
- Connection pooling (min: 10, max: 50)
- Eviction policy: allkeys-lru

### References
- Benchmark code: `/benchmarks/cache-comparison/`
- Architecture discussion: docs/project/adrs/ADR-003-caching-strategy.md
```

**Key elements:**
- **Anchor IDs**: `##` headings like `#caching-strategy` for easy WORKLOG references
- **Problem statement**: What decision needed to be made
- **Alternatives**: Each option with pros/cons and data
- **Decision rationale**: Why this choice, with justification
- **Implementation details**: How the decision was implemented
- **References**: Links to benchmarks, ADRs, related docs

### When WORKLOG Entries Are Created

**Automatically by `/implement`:**
- After each phase completion
- Documents phase implementation work
- Prepended to top (reverse chronological)

**Manually by humans via `/comment`:**
- When developers make changes outside `/implement` workflow
- Manual fixes, refactoring, debugging
- Identified by @username instead of agent-name

**At task completion:**
- Final WORKLOG entry summarizing overall results
- Documents completion of all acceptance criteria

### WORKLOG Completeness Criteria

Before marking a task complete, verify WORKLOG.md has:

✅ **Narrative continuity**: WORKLOG tells coherent story from start to finish
✅ **Lessons captured**: Key gotchas and discoveries documented
✅ **File coverage**: Major implementation files referenced in entries
✅ **Human contributions**: Any manual work documented via `/comment`
✅ **RESEARCH references**: Complex decisions link to detailed analysis

**Example complete WORKLOG:**
```markdown
# WORKLOG

## 2025-01-15 16:00 - backend-specialist

Task TASK-001 complete. All 4 phases finished, tests passing (97% coverage),
code review score: 94. Authentication system ready for staging deployment.

Files: See entries below for complete file list

---

## 2025-01-15 14:30 - backend-specialist
[Phase 1.2 entry - shown earlier]

---

## 2025-01-15 10:15 - @alice

Manual fix: Added rate limiting to prevent brute force attacks. Cloudflare wasn't
blocking fast enough, added Express middleware (10 attempts/15min).

Gotcha: Redis needed for distributed rate limit state
Lesson: Should be in security requirements from start
Files: src/middleware/rate-limit.ts

---

## 2025-01-14 15:20 - database-specialist
[Phase 1.1 entry]
```

## Test-First Strategy

### Benefits

- **Clarity**: Forces clear understanding of requirements before coding
- **Coverage**: Ensures all code is tested (no "I'll add tests later")
- **Design**: Better API design from thinking about usage first
- **Confidence**: Know immediately when code meets requirements
- **Regression**: Prevents future changes from breaking functionality

### When Test-First Applies

**Always for:**
- New features with defined acceptance criteria
- Bug fixes (write test that reproduces bug first)
- API endpoints and services
- Data transformations and business logic
- Critical user workflows

**Optional for:**
- Exploratory prototypes (add tests once approach is validated)
- Infrastructure setup (integration tests may be more appropriate)
- Configuration changes
- Documentation updates

### Test-First Example

```
Phase 1.2: Implement user registration endpoint

1. Write test:
   ✓ POST /api/register with valid data returns 201
   ✓ POST /api/register with duplicate email returns 409
   ✓ POST /api/register with invalid data returns 400
   ✓ POST /api/register creates user in database

2. Verify tests fail (red)

3. Implement endpoint (minimal code to pass)

4. Run tests (green)

5. Code review → Score: 85 (needs error handling improvement)

6. Refactor error handling

7. Re-run tests (still green)

8. Code review → Score: 92 (ready to proceed)

9. Write WORKLOG entry, proceed to next phase
```

## Quality Gates

**Quality gates are validation checkpoints** that ensure code quality at different levels of development. This guideline is the **single source of truth** for quality gate configuration.

### Quality Gate Hierarchy

```
Per-Phase Gates (finest granularity)
  ↓ Multiple phases combine into...
Per-Task Gates (task completion)
  ↓ Multiple tasks combine into...
Per-Epic Gates (epic completion)
```

**Enforcement**:
- **Per-Phase Gates**: `/implement` command validates before marking phase complete
- **Per-Task Gates**: `/branch merge develop` command validates before merging
- **Per-Epic Gates**: Manual validation (checklist in epic Definition of Done)

**Related Guidelines**:
- `git-workflow.md`: Describes how `/branch merge` enforces per-task gates
- `testing-standards.md`: References coverage targets from this file

### Customizing Quality Dimensions

**Quality dimensions are configured in the YAML frontmatter** at the top of this file. The `/quality` command reads this configuration to determine which quality aspects to assess.

**Enabled Dimensions** (customize based on your team's priorities):
- **code_quality**: Always recommended - code maintainability foundation
- **security**: Critical for production systems
- **performance**: Essential for high-traffic or resource-constrained apps
- **testing**: Recommended - confidence in code correctness
- **documentation**: Important for team collaboration (optional for MVPs)
- **architecture**: Important for long-term maintainability

**Customization Examples:**

**Startup MVP** (speed over perfection):
```yaml
quality_dimensions:
  enabled: [code_quality, testing, security]
  # Dropped: documentation, performance, architecture (add later)
```

**Enterprise Product** (comprehensive quality):
```yaml
quality_dimensions:
  enabled: [code_quality, security, performance, testing, documentation, architecture]
  # All dimensions enabled for production-grade quality
```

**Performance-Critical App** (focus on speed):
```yaml
quality_dimensions:
  enabled: [code_quality, performance, testing]
  # Heavy focus on performance, security added when needed
```

**How It Works:**
- The `/quality assess` command reads this configuration
- Only enabled dimensions are assessed by their respective agents
- Quality reports include only the dimensions you care about
- You can add/remove dimensions as your project matures

### Complexity Scoring

**Complexity scoring helps the `/plan` command** determine if tasks should be broken down into smaller subtasks. Configure scoring rules in the YAML frontmatter at the top of this file.

**How It Works:**
1. The `/plan` command analyzes task requirements
2. Assigns points based on complexity indicators (multi-domain integration, security, etc.)
3. Recommends decomposition based on total complexity score
4. Teams can customize point values and thresholds

**Default Indicators** (customize based on your team's experience):
- **Multi-domain integration** (+3 points): API + database, frontend + backend, UI + server
- **Security implementation** (+2 points): Authentication, authorization, encryption, permissions
- **Database schema changes** (+2 points): Migrations, schema modifications, data transformations
- **External integrations** (+2 points): Third-party APIs, service connections, webhooks
- **Performance optimization** (+2 points): Scaling, optimization, performance tuning
- **UI/UX implementation** (+1 point): Component creation, interface design, responsive work
- **Testing requirements** (+1 point): Test creation, validation, quality assurance

**Thresholds:**
- **High complexity (≥5 points)**: Suggest breaking into subtasks with focused responsibilities
- **Medium complexity (3-4 points)**: Consider decomposition based on timeline
- **Low complexity (≤2 points)**: Task appropriately scoped

**Customization Examples:**

**Senior Team** (higher threshold):
```yaml
complexity_scoring:
  thresholds:
    high_complexity: 8      # More confident handling complex tasks
    medium_complexity: 5
```

**Junior Team** (lower threshold):
```yaml
complexity_scoring:
  thresholds:
    high_complexity: 4      # Break down tasks earlier
    medium_complexity: 2
```

**Different Point Values** (team-specific challenges):
```yaml
complexity_scoring:
  indicators:
    multi_domain_integration: 5   # Team struggles with integration
    security_implementation: 1    # Team has strong security expertise
```

### Per-Phase Gates

**Required before marking phase complete:**

1. **Functional**: All acceptance criteria met
2. **Tests**: All tests pass, coverage ≥ 95%
3. **Quality**: Code review score ≥ 90
4. **Security**: Security-auditor review passes (if security-relevant phase)
5. **Documentation**: Inline docs for public APIs

**Security-Relevant Phase Detection** (same criteria as task-level detection):

A phase is security-relevant if it involves ANY of:
- **Keywords in phase description**: `auth`, `login`, `password`, `token`, `encrypt`, `decrypt`, `hash`, `crypto`, `permission`, `role`, `admin`, `validate input`, `sanitize`, `API key`, `secret`, `credential`, `OAuth`, `PII`, `sensitive data`
- **File patterns being modified**: `**/auth/**`, `**/security/**`, `**/crypto/**`, `**/*Auth*`, `**/*Security*`, `**/*Validation*`, `**/middleware/auth*`, `**/guards/**`, `**/policies/**`
- **Security-specific phases**: Threat modeling, security testing, penetration testing, security audit

**Security-Auditor Phase Review** (automatic for security-relevant phases):
- Reviews implementation for vulnerabilities (injection, XSS, CSRF, etc.)
- Validates cryptographic usage (proper algorithms, key management)
- Checks input validation and sanitization
- Verifies authorization logic and permission checks
- Confirms secure data handling (PII, credentials, sensitive info)
- Ensures OWASP compliance
- **BLOCKS phase completion if critical vulnerabilities found**

**When invoked**: Automatically by `/implement` command before marking security-relevant phase complete

### Per-Task Gates

**Required before `/branch merge develop`:**

1. **All phases complete**: Every phase passed its per-phase gates
2. **Integration tests**: Full test suite passes
3. **WORKLOG complete**: All work documented
4. **No regressions**: Existing tests still pass
5. **Branch up-to-date**: Merged latest from develop

**Enforcement**: The `/branch merge develop` command (see `git-workflow.md`) enforces these gates:
- Runs full test suite before merge
- Blocks merge if tests fail
- Validates branch status and changes

**This is the primary quality gate for task completion** - if you can merge to develop, your task meets quality standards.

### Per-Epic Gates

**Required before closing epic:**

1. **All tasks complete**: Every task in epic finished
2. **Acceptance criteria**: Epic-level definition of done met
3. **Documentation**: User-facing docs updated if needed
4. **Deployment**: Changes successfully deployed to staging
5. **Validation**: Stakeholder sign-off (if required)

## Implementation Plan Structure

**Referenced by:** `/plan` command when creating PLAN.md files

### Default Phase Structures by Task Type

**Standard Implementation:**
1. Design → Test-Driven Implementation → Integration → Documentation

**Frontend Tasks:**
1. Design → Component Tests → Implementation → Responsive/E2E

**Backend Tasks:**
1. API Design → Unit Tests → Implementation → Integration Tests

**Bug Fixes:**
1. Investigation → Root Cause → Fix → Regression Tests

**Database Tasks:**
1. Schema Design → Migration Script → Unit Tests → Integration Tests

**Security Tasks:**
1. Threat Modeling → Security Tests → Implementation → Security Audit

### Alternative Test-First Patterns

Teams can choose different testing approaches based on context:

- **Strict TDD**: Red-Green-Refactor cycle visible in every step
- **BDD Scenarios**: Given/When/Then scenarios → Implement tests → Build features
- **Test Pyramid**: Heavy unit tests, moderate integration, light E2E
- **Pragmatic**: Spike/explore → Write tests → Implement production code

**Note**: Phases are suggestions. Modify to fit your workflow and team preferences.

## Plan Review Requirements

**Referenced by:** `/plan` command before presenting plan to user

### Mandatory Code-Architect Review

**BEFORE presenting any plan to the user**, the `/plan` command must invoke the code-architect agent for review.

**Code-Architect Reviews:**
- Architectural soundness and consistency with existing ADRs
- Phase structure and logical breakdown
- Technology choices and patterns
- Scalability and maintainability considerations
- Cross-cutting concerns (security, performance, observability)
- Integration with existing system architecture

**Code-Architect May:**
- Approve plan as-is (proceed to present to user)
- Suggest modifications to phases or approach
- Request additional phases for technical debt or infrastructure
- Recommend creating ADR for significant architectural decisions
- Identify potential architectural risks or anti-patterns

### Conditional Security-Auditor Review

**AFTER code-architect approval**, the `/plan` command must detect if the task is security-relevant and invoke security-auditor for review.

**Auto-Detection Criteria** (task is security-relevant if ANY match):

**Keywords in task description or acceptance criteria:**
- Authentication/authorization: `auth`, `login`, `password`, `token`, `session`, `permission`, `role`, `access control`
- Data security: `encrypt`, `decrypt`, `hash`, `salt`, `crypto`, `PII`, `sensitive data`, `personal information`
- Security features: `security`, `vulnerability`, `threat`, `OWASP`, `XSS`, `CSRF`, `SQL injection`, `sanitize`, `validate input`
- Admin features: `admin`, `privilege`, `elevation`, `sudo`, `root`
- External integrations: `API key`, `secret`, `credential`, `OAuth`, `SAML`, `SSO`, `third-party`

**File patterns in affected files:**
- `**/auth/**`, `**/authentication/**`, `**/authorization/**`
- `**/security/**`, `**/crypto/**`, `**/encryption/**`
- `**/*Auth*`, `**/*Security*`, `**/*Crypto*`, `**/*Validation*`
- `**/middleware/auth*`, `**/guards/**`, `**/policies/**`

**Security-Auditor Reviews:**
- Threat modeling for authentication/authorization flows
- Input validation and sanitization approach
- Cryptographic implementation (algorithms, key management)
- Data protection for sensitive information (PII, credentials)
- Authorization logic and permission models
- Security best practices (OWASP compliance)
- Vulnerability risks (injection, XSS, CSRF, etc.)

**Security-Auditor May:**
- Approve security approach as-is
- Suggest security-specific phases (threat modeling, penetration testing)
- Request additional security controls or validation
- Recommend creating security-focused ADR
- Identify potential security vulnerabilities in plan
- Require security testing phases before implementation

**Only after BOTH code-architect AND security-auditor approval (if security-relevant) should the plan be presented to the user.**

## Progress Tracking Protocol

**Referenced by:** `/implement` command after each phase completion

### Dual Tracking System

**PLAN.md**: Tracks implementation phases (what work needs to be done)
**TASK.md/BUG.md**: Tracks acceptance criteria (what requirements must be satisfied)

### After Each Phase Completion

**Execute in this order:**

**1. Verify Completion Thoroughly**
   - All tests pass (run test suite)
   - Code works as intended (manual verification if needed)
   - Requirements from phase description fully met
   - No errors, warnings, or broken functionality

**2. Update PLAN.md (Phase Tracking)**
   - Change `- [ ] 1.1 Task description` to `- [x] 1.1 Task description`
   - ONLY mark complete when verified - never mark prematurely
   - Use Edit tool to update the specific checkbox
   - Update IMMEDIATELY after completion, not in batches

**3. Update TASK.md/BUG.md (Acceptance Criteria Tracking)**
   - **For local issues (TASK-###, BUG-###)**:
     - Review acceptance criteria checkboxes in TASK.md/BUG.md
     - If phase satisfies any criterion, mark complete: `- [ ] criterion` → `- [x] criterion`
     - Example: Phase "1.2 Implement login form" satisfies "User can log in with email/password"
     - ONLY check off when criterion fully satisfied and verified
   - **For Jira issues (PROJ-###)**:
     - Note satisfied criteria in WORKLOG entry (Jira is source of truth)
     - Example: "✓ Satisfies Jira AC: User can log in with email/password"

**4. Write WORKLOG Entry**
   - Get timestamp: Run `date '+%Y-%m-%d %H:%M'` (never estimate)
   - Document what was done, lessons learned
   - Include which PLAN steps and TASK criteria were completed
   - Prepend to top (reverse chronological order)
   - See "WORKLOG Entry Format" section above for complete structure

**5. Consider RESEARCH.md**
   - If complex technical decisions were made, create RESEARCH.md section
   - See "When to Create RESEARCH.md" section above for criteria

**Critical**: Never mark items complete until verified working. Premature checkoffs lead to incomplete work and confusion.

## Task Completion Validation

**Referenced by:** `/implement` command when all PLAN.md phases are checked off

### Completion Checklist

**Required before marking any task complete:**

**1. All PLAN.md Phases Checked Off**
   - Verify EVERY phase checkbox is marked: `- [x] All phases`
   - If any phases remain unchecked, task is NOT complete

**2. All Acceptance Criteria Verified and Checked Off**
   - **For local issues (TASK-###, BUG-###)**:
     - EVERY checkbox in TASK.md/BUG.md acceptance criteria marked: `- [x] All criteria`
   - **For Jira issues (PROJ-###)**:
     - EVERY Jira acceptance criterion verified (documented in WORKLOG)
   - If any criteria remain unsatisfied, task is NOT complete

**3. Tests Passing**
   - All test suites pass with 95%+ coverage (or configured target)
   - Run full test suite before marking complete
   - No failing tests, no errors, no warnings

**4. WORKLOG Documented**
   - Final entry summarizing overall task completion
   - Lists all completed phases and satisfied criteria
   - See "WORKLOG Completeness Criteria" section above

**5. Epic Consistency (if epic exists)**
   - Task marked complete in epic task list: `- [x] TASK-001`
   - Epic progress updated

**Final Checkpoint**: Can you honestly say this task is 100% complete with all requirements met? If no, keep working. If yes, mark complete.

## Test-First Guidance Protocol

**Referenced by:** `/implement` command before implementation phases

### Pragmatic Test-First Philosophy

**Test-first when you know what to build, code-first when discovering unknowns.**

### Pre-Implementation Check

**Before executing implementation phases (e.g., "2.2 Implement X"):**

**1. Check for Test Phases**
   - Look for preceding test phase (e.g., "2.1 Write tests for X")
   - If test phase exists but is unchecked:
     - **Prompt**: "Pragmatic test-first: Phase '2.1 Write tests' should typically come first. Execute 2.1 first? (yes/skip)"
     - **NEVER BLOCK**: Always allow skip - user may be in discovery mode

**2. If No Tests Found**
   - **Prompt with options**:
     1. Auto-generate comprehensive test suite from acceptance criteria (RECOMMENDED if requirements are clear)
     2. I'll write tests manually first (good for learning/experimentation)
     3. Skip tests for now - I'm in discovery mode (must add tests before phase completion)
   - **Default**: Option 1 if no user input
   - **Track choice**: Document in WORKLOG entry

### AI-Powered Test Generation Messaging

**Frame test generation as AI superpower, not chore:**
- "✅ Generated comprehensive test suite with X unit tests, Y integration tests, Z edge cases. All tests documented for team reference."
- Highlight: Create comprehensive test suites in seconds with explanatory comments
- Benefit: Makes TDD/BDD easier than skipping tests!

## Agent Context Briefing

**Referenced by:** `/implement` command when invoking specialist agents

### Principle

**Provide domain-specific context to agents, not full epic context dump.**

Filter and prepare only relevant information for each specialist to optimize performance and reduce context overload.

### Context Filtering Patterns by Agent

**Backend Specialists** receive:
- API contracts, database schemas
- Security requirements, performance targets
- Relevant ADR decisions for backend architecture
- Previous backend work from WORKLOG

**Frontend Specialists** receive:
- Component specifications, state management patterns
- UI/UX requirements, responsive design needs
- Relevant ADR decisions for frontend architecture
- Previous frontend work from WORKLOG

**Test Engineers** receive:
- Coverage targets, validation patterns
- Quality gates, existing test structure
- Test-first approach configuration
- Previous testing work from WORKLOG

**Security Auditors** receive:
- Threat models, authentication flows
- Authorization requirements, compliance needs
- Security-related ADR decisions
- Previous security work from WORKLOG

**Database Specialists** receive:
- Schema requirements, migration patterns
- Performance constraints, data validation
- Database-related ADR decisions
- Previous database work from WORKLOG

**Performance Optimizers** receive:
- Performance targets, current bottlenecks
- Scaling requirements, optimization opportunities
- Performance-related ADR decisions
- Previous optimization work from WORKLOG

### Dynamic Context Loading

**Process:**
1. Parse WORKLOG.md, RESEARCH.md, ADR files in real-time
2. Extract only domain-relevant sections for selected agent
3. Combine with phase-specific requirements from PLAN.md
4. Include lessons learned from previous phases to avoid repeating mistakes
5. Present concise, actionable context that eliminates noise

**Benefit**: Agents focus on relevant information without context overload, improving decision quality and execution speed.

## Command Integration

### Commands That Use This Loop

- **`/implement TASK-### PHASE`**: Executes development loop for specific phase
- **`/plan TASK-###`**: Defines phases and acceptance criteria that drive the loop
- **`/quality`**: Comprehensive quality assessment (superset of code review)
- **`/test-fix`**: Automated test failure detection and resolution

### How Commands Enforce the Loop

**`/implement` command:**
```
1. Read phase acceptance criteria
2. Invoke appropriate specialist agent
3. Agent follows development loop (test → code → review)
4. Validate quality gates before marking phase complete
5. Update WORKLOG and task status
```

**`/quality` command:**
```
1. Assess code quality across entire codebase
2. Run all quality gates (tests, coverage, review, security)
3. Generate quality report with scores
4. Recommend improvements if below thresholds
```

See individual command documentation for complete workflows.

## Examples

### Example 1: Feature Implementation

```
Task: TASK-001 - User Authentication
Phase: 1.2 - Implement login endpoint

1. Test-Engineer writes tests:
   ✓ POST /api/login with valid credentials returns token
   ✓ POST /api/login with invalid credentials returns 401
   ✓ Token includes correct user claims
   ✓ Login attempts are rate-limited

2. Backend-Specialist implements:
   - POST /api/login route
   - Credential validation
   - JWT token generation
   - Rate limiting middleware

3. Run tests: All pass ✅

4. Code-Reviewer assesses:
   - Score: 88
   - Feedback: "Add password hashing timing attack protection"

5. Backend-Specialist refactors:
   - Implement constant-time comparison
   - Update tests for timing safety

6. Re-run tests: All pass ✅

7. Code-Reviewer re-assesses:
   - Score: 93 ✅

8. Backend-Specialist writes WORKLOG:
   "/comment Implemented login endpoint with JWT tokens and rate limiting.
   Key decision: Use bcrypt.compare for timing-safe password verification.
   Challenge: Initial implementation vulnerable to timing attacks.
   Next: Implement token refresh mechanism in phase 1.3."

9. Phase marked complete, proceed to phase 1.3
```

### Example 2: Bug Fix

```
Bug: BUG-003 - Cart total calculation incorrect for discounted items

1. Test-Engineer writes failing test:
   ✓ Cart with 10% discount shows correct total
   (Currently fails - shows $100 instead of $90)

2. Backend-Specialist investigates:
   - Reads WORKLOG from previous cart work
   - Identifies discount not applied in subtotal calculation

3. Implement fix:
   - Update calculateSubtotal() to apply discounts
   - Add discount validation

4. Run tests: All pass ✅ (including new test)

5. Code-Reviewer assesses:
   - Score: 95 ✅

6. Backend-Specialist writes WORKLOG:
   "/comment Fixed cart total calculation bug.
   Root cause: Discount multiplier applied to total, not subtotal.
   Also added validation to prevent negative discounts.
   All existing cart tests still pass."

7. Bug marked resolved, create PR
```

## General Development Loop Knowledge

For development workflow best practices, Claude has extensive knowledge of:
- Test-Driven Development (TDD) methodology
- Red-Green-Refactor cycle
- Continuous Integration and Continuous Deployment (CI/CD)
- Code review best practices
- Quality metrics and thresholds
- Agile and iterative development processes

Ask questions like "What's the best approach for [X] in a development loop?" and Claude will provide guidance based on industry standards and your configured workflow.
