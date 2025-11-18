---
# === Metadata ===
template_type: "guideline"
created: "2025-10-30"
last_updated: "2025-11-13"
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

**Referenced by Commands:** `/implement`, `/plan`, `/quality`, `/troubleshoot`, `/comment`

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

**WORKLOG Documentation**: code-reviewer creates Review WORKLOG entries (see `worklog-format.md` for Review formats).

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

### 6. Review and Adapt Plans (After Phase Completion)

**After completing each phase, conduct review cycle to adapt plans**:

**Step 1: Run Quality Reviews**
- Code review (always - via code-reviewer agent)
- Security audit (if security-critical flag set)
- Architecture review (if high complexity)

**Step 2: Synthesize Findings**
- Identify immediate actions vs deferred improvements
- Determine impact on remaining phases
- Make decisions (with stakeholder input if needed)

**Step 3: Adapt TASK and PLAN Files**
- Update TASK.md if new requirements discovered
- Update PLAN.md phases based on learnings
- Remove obsolete approaches from PLAN
- Keep files clean and current

**Step 4: Document in WORKLOG**
- Brief entry: what changed and why
- Link to detailed reports if needed
- See [worklog-format.md](worklog-format.md) for "Plan Changes" entry format

**Step 5: Proceed to Next Phase**
- With updated understanding
- With adapted approach
- With cleaner, more accurate plan

**Example review cycle**:
```
Phase 2 Complete → Security Audit → Finds email verification needed
→ Add to TASK.md acceptance criteria
→ Insert Phase 3 in PLAN.md for email verification
→ Document decision in WORKLOG.md
→ Proceed to new Phase 3
```

**Key principle**: Inspect and adapt - plans evolve based on implementation learnings.

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
- **context-analyzer**: External research, documentation lookup, resource curation

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

Every issue directory (`pm/issues/TASK-###-name/` or `BUG-###-name/`) can contain up to four files:

**TASK.md / BUG.md** (WHAT to do):
- Primary issue file with acceptance criteria
- Created by `/spec` or `/plan` commands
- See `pm-guide.md` for complete format

**PLAN.md** (HOW to do it - phases):
- Phase-based implementation breakdown
- Created by `/plan` command
- See `pm-guide.md` for complete format

**WORKLOG.md** (WHAT was done and WHY):
- Reverse chronological narrative work history
- Documents work done, decisions made, and rationale
- Created automatically by `/implement` after each phase
- See `worklog-format.md` for entry formats (Standard, Troubleshooting, Investigation, Reviews)
- For complex architecture decisions, use `/adr` to create Architecture Decision Records

### WORKLOG Documentation

**For complete WORKLOG entry formats**, see `worklog-format.md` which documents:
- Standard format (HANDOFF, COMPLETE, and REVIEW entries)
- Troubleshooting format (hypothesis-based entries)
- Investigation format (external research findings)
- When to write entries vs when to skip
- Best practices and examples
- Entry length guidelines
- Cross-referencing RESEARCH.md

**Quick Reference:**
- Write entries at agent handoffs and phase completion
- Always maintain reverse chronological order (newest at TOP)
- Keep entries scannable (~500 chars ideal)
- Reference RESEARCH.md for complex decisions

### Troubleshooting

**When encountering bugs or unexpected behavior**, use the structured troubleshooting methodology.

**See**: `troubleshooting.md` for complete 5-step loop (Research → Hypothesize → Implement → Test → Document), debug logging practices, and troubleshooting-specific WORKLOG format.

**See**: `worklog-format.md` for troubleshooting WORKLOG entry format with hypothesis tracking.

**Command**: Use `/troubleshoot` to apply systematic debugging approach

### Architecture Decision Records

**For significant technical decisions**, use `/adr` command to create Architecture Decision Records:
- Architecture decisions affecting multiple components
- Technology selections with trade-offs
- Security approaches and threat models
- Performance strategies with benchmarks
- API design patterns and conventions

**Decision documentation guidelines:**
- **Simple decisions** → WORKLOG entry with brief rationale
- **External research** → Investigation format (invoke context-analyzer)
- **Architecture decisions** → ADR via `/adr` command
- **Complex debugging** → Extended WORKLOG entries with hypothesis tracking

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
Per-Spec Gates (spec completion)
```

**Enforcement**:
- **Per-Phase Gates**: `/implement` command validates before marking phase complete
- **Per-Task Gates**: `/branch merge develop` command validates before merging
- **Per-Spec Gates**: Manual validation (checklist in spec Definition of Done)

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

**MANDATORY: Every phase follows strict test-first loop** (see pm-guide.md "Test-First Phase Loop"):

**Phase Execution Loop:**
1. **Write tests** (must fail - red) - Proves tests are testing something real
2. **Write code** (make tests pass - green) - Minimal implementation
3. **Code review** (must pass ≥90) - Catches issues before they compound
4. **Only when ALL pass:**
   - Commit phase changes to git
   - Update WORKLOG.md entry
   - Mark phase complete in PLAN.md
   - Proceed to next phase

**Quality gates** (ALL required):
1. ✅ **Tests written and initially failing** (red) - Validates tests are legitimate
2. ✅ **Tests passing after implementation** (green) - Confirms functionality works
3. ✅ **Code review score ≥90** - Ensures maintainability and quality
4. ✅ **Coding Standards** - Implementation follows coding-standards.md conventions
5. ✅ **Security approval** - Security-auditor review passes (if security-relevant phase)
6. ✅ **Test coverage ≥95%** - Ensures adequate test coverage
7. ✅ **Acceptance criteria met** - Phase objectives achieved
8. ✅ **Documentation** - Inline docs for public APIs

**No shortcuts**: Tests cannot be skipped, code review cannot be skipped, phases cannot be combined.

**Enforcement**: `/implement` command enforces this loop. If any quality gate fails, phase is not considered complete.

**Coding Standards Gate** (enforced before phase completion):
- Implementation agents read `coding-standards.md` BEFORE writing code
- Auto-detectable violations (file naming, import order, line length) BLOCK phase completion
- Agent self-validates before marking phase complete
- Rationale for necessary deviations documented in WORKLOG
- See `coding-standards.md` "Automated Quality Checks" for complete validation checklist

**Security-Relevant Phase Detection**:

See `agent-coordination.md` "Security Governance" section for complete detection criteria (security keywords and file patterns). A phase is security-relevant if it involves security keywords, file patterns, or security-specific phases.

**Security-Auditor Phase Review** (automatic for security-relevant phases):
- Reviews implementation for vulnerabilities (injection, XSS, CSRF, etc.)
- Validates cryptographic usage (proper algorithms, key management)
- Checks input validation and sanitization
- Verifies authorization logic and permission checks
- Confirms secure data handling (PII, credentials, sensitive info)
- Ensures OWASP compliance
- **BLOCKS phase completion if critical vulnerabilities found**

**WORKLOG Documentation**: security-auditor creates Review WORKLOG entries with OWASP classifications (see `worklog-format.md` for Security Review formats).

**When invoked**: Automatically by `/implement` command before marking security-relevant phase complete

### Per-Task Gates

**Required before `/branch merge develop`:**

1. **All phases complete**: Every phase passed its per-phase gates
2. **Integration tests**: Full test suite passes
3. **WORKLOG complete**: All work documented
4. **No regressions**: Existing tests still pass
5. **Branch up-to-date**: Merged latest from develop
6. **Project documentation synchronized**: architecture-overview.md, design-overview.md, README.md, CLAUDE.md, and guidelines reflect task changes (see Documentation Synchronization Checklist below)

**Enforcement**: The `/branch merge develop` command (see `git-workflow.md`) enforces these gates:
- Runs full test suite before merge
- Blocks merge if tests fail
- Validates branch status and changes

**This is the primary quality gate for task completion** - if you can merge to develop, your task meets quality standards.

### Per-Spec Gates

**Required before closing spec:**

1. **All tasks complete**: Every task in spec finished
2. **Acceptance criteria**: Epic-level definition of done met
3. **Documentation**: User-facing docs updated if needed
4. **Deployment**: Changes successfully deployed to staging
5. **Validation**: Stakeholder sign-off (if required)

### Documentation Synchronization Checklist

**Before merging to develop, validate these files are current:**

#### architecture-overview.md
- [ ] New components/modules documented in system structure
- [ ] Technology decisions reflected in tech stack
- [ ] Integration patterns updated if new integrations added
- [ ] ADRs referenced if significant architectural decisions made

#### design-overview.md
- [ ] New UI components documented
- [ ] Design tokens updated if colors/typography/spacing changed
- [ ] Design patterns updated if new patterns established
- [ ] Screenshots/mockups updated if UI changed significantly

#### Root README.md
- [ ] Features list reflects new capabilities
- [ ] Setup instructions updated if dependencies changed
- [ ] Project status reflects current state
- [ ] Links and references still valid

#### CLAUDE.md
- [ ] Workflow changes reflected in instructions
- [ ] New commands documented if added
- [ ] Guidelines references updated if structure changed
- [ ] Project context reflects current architecture

#### Guidelines (docs/development/guidelines/)
- [ ] Process improvements documented
- [ ] New patterns added to relevant guidelines
- [ ] Examples updated with real implementations
- [ ] Anti-patterns documented if discovered

**Note**: Not every task requires updates to all files. Use judgment - only update files affected by your changes.

## Planning and Implementation Structure

### Plan Creation and Review

**For complete planning details**, see `pm-guide.md` which documents:
- Default phase structures by task type (frontend, backend, bug fixes, etc.)
- Mandatory code-architect review requirements
- Conditional security-auditor review for security-relevant tasks
- Auto-detection criteria for security relevance
- Alternative test-first patterns

**Referenced by**: `/plan` command when creating PLAN.md files

### Progress Tracking

**For progress tracking protocol**, see `pm-guide.md` which documents:
- Dual tracking system (PLAN.md phases vs TASK.md acceptance criteria)
- After-phase-completion checklist (verify, update PLAN, update TASK, write WORKLOG, consider RESEARCH)
- Task completion validation criteria
- When to mark items complete vs incomplete

**Referenced by**: `/implement` command after each phase completion

### Test-First Guidance

**For test-first approach**, see `pm-guide.md` which documents:
- Pragmatic test-first philosophy (test-first when you know, code-first when discovering)
- Pre-implementation check for test phases
- AI-powered test generation messaging
- When to prompt for test generation vs allowing code-first

**Referenced by**: `/implement` command before implementation phases

### Agent Context Preparation

**For agent briefing patterns**, see `pm-guide.md` which documents:
- Context filtering by agent type (backend, frontend, test, security, database, performance)
- What each agent receives (domain-specific vs full context)
- Dynamic context loading process
- How to avoid context overload

**Referenced by**: `/implement` command when invoking specialist agents

## Command Integration

### Commands That Use This Loop

- **`/implement TASK-### PHASE`**: Executes development loop for specific phase
- **`/plan TASK-###`**: Defines phases and acceptance criteria that drive the loop
- **`/quality`**: Comprehensive quality assessment (superset of code review)
- **`/troubleshoot`**: Systematic debugging with research-first approach

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
TASK-001 Phase 1.2: Implement login endpoint
→ Test-Engineer writes 4 tests (valid creds, invalid creds, token claims, rate limit)
→ Backend-Specialist implements endpoint + JWT + rate limiting
→ Tests pass, Code-Reviewer scores 88 (feedback: timing attack protection needed)
→ Refactor with constant-time comparison, re-test, score 93 ✅
→ WORKLOG entry, mark phase complete
```

### Example 2: Bug Fix

```
BUG-003: Cart total calculation incorrect
→ Test-Engineer writes failing test (expects $90, shows $100)
→ Backend-Specialist investigates, finds discount not applied to subtotal
→ Fix calculateSubtotal(), add validation
→ Tests pass, Code-Reviewer scores 95 ✅
→ WORKLOG entry, mark bug resolved
```

## CHANGELOG Updates

**After completing work, always update CHANGELOG.md:**

- Features: Add to `[Unreleased] > Added`
- Bug fixes: Add to `[Unreleased] > Fixed`
- Breaking changes: Add to `[Unreleased] > Changed` with BREAKING note

See [Versioning and Releases](./versioning-and-releases.md) for complete CHANGELOG guidelines and semantic versioning strategy.

## Related Documentation

**Core Workflow Guidelines:**
- [Plan Structure](./pm-guide.md) - Phase patterns, reviews, progress tracking, test-first guidance
- [WORKLOG Format](./worklog-format.md) - Standard and troubleshooting WORKLOG entry formats
- [Issue Management](./pm-guide.md) - TASK.md, BUG.md, SPEC.md file formats
- [Troubleshooting](./troubleshooting.md) - 5-step debug loop methodology

**Supporting Guidelines:**
- [Versioning and Releases](./versioning-and-releases.md) - Semantic versioning, release process, CHANGELOG maintenance
- [Git Workflow](./git-workflow.md) - Branching and merge requirements
- [Testing Standards](./testing-standards.md) - Test coverage and quality thresholds

## General Development Loop Knowledge

For development workflow best practices, Claude has extensive knowledge of:
- Test-Driven Development (TDD) methodology
- Red-Green-Refactor cycle
- Continuous Integration and Continuous Deployment (CI/CD)
- Code review best practices
- Quality metrics and thresholds
- Agile and iterative development processes

Ask questions like "What's the best approach for [X] in a development loop?" and Claude will provide guidance based on industry standards and your configured workflow.
