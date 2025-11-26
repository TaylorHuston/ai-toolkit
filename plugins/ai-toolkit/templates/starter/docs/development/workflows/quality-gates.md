---
last_updated: "2025-11-18"
description: "Quality gate configuration and enforcement for per-phase, per-task, and per-spec validation"

# === Quality Configuration ===
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

# === Complexity Scoring Configuration ===
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

# Quality Gates

**Referenced by Commands:** `/implement`, `/quality`, `/branch merge`

**This is the single source of truth for quality gate configuration across all development levels.**

For the complete development workflow context, see `development-loop.md`.

---

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

**MANDATORY: Every phase with testable behavior follows TDD** (RED/GREEN/REFACTOR):

**Phase Execution with TDD Checkpoints:**

```
#### X.RED - Write Failing Tests
1. Write tests defining expected behavior
2. Run tests - MUST fail
3. [RED CHECKPOINT] Document failure in WORKLOG
   - If tests PASS: STOP - you're not testing new behavior
   - If tests ERROR: STOP - fix test bugs first

#### X.GREEN - Implement to Pass Tests
4. Write minimal code to pass tests
5. Run tests - MUST pass
6. [GREEN CHECKPOINT] All tests now passing

#### X.REFACTOR - Clean Up (loops until review >= 90)
7. Refactor for maintainability
8. Run tests - must still pass (no regressions)
9. Code review - if < 90, address feedback and repeat 7-9
10. [EXIT GATE] Review >= 90, commit phase
```

**RED Checkpoint Verification (BLOCKING):**

Before implementation begins, verify RED checkpoint:
- Tests exist and execute without errors
- Tests FAIL for the expected reason (missing implementation)
- Failure documented in WORKLOG: "RED: X tests failing - [reason]"

**BLOCKS implementation if:**
- Tests pass (not testing new behavior)
- Tests error (test code has bugs)
- No tests written

**WORKLOG Entry for RED Checkpoint:**
```
## RED CHECKPOINT: Phase X.RED
- Tests written: 8 test cases in src/__tests__/feature.test.ts
- Execution: All 8 tests FAILED as expected
- Failure reason: Missing implementation (FunctionNotFoundError)
- Ready for implementation: YES
```

**Quality gates** (ALL required):
1. ✅ **RED checkpoint passed** - Tests written, executed, and FAILED as expected
2. ✅ **GREEN checkpoint passed** - Tests now PASS after implementation
3. ✅ **Code review score ≥90** - Ensures maintainability and quality
4. ✅ **Code review WORKLOG entry** - code-reviewer writes detailed review entry (see worklog-format.md)
5. ✅ **Coding Standards** - Implementation follows coding-standards.md conventions
6. ✅ **Security approval** - Security-auditor review passes (if security-relevant phase)
7. ✅ **Security WORKLOG entry** - security-auditor writes review entry (if security-relevant)
8. ✅ **Test coverage ≥95%** - Ensures adequate test coverage
9. ✅ **Acceptance criteria met** - Phase objectives achieved
10. ✅ **Documentation** - Inline docs for public APIs

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


---

## Related Documentation

**For complete development workflow**: See `development-loop.md` for:
- Development philosophy and loop structure
- Agent coordination patterns
- Test-first strategy
- Work documentation requirements

**For specific quality aspects**:
- `coding-standards.md` - Code quality expectations
- `testing-standards.md` - Test coverage and quality standards
- `security-guidelines.md` - Security compliance requirements
- `git-workflow.md` - Branch and merge workflows with quality gates

**Commands using quality gates**:
- `/implement` - Enforces per-phase gates during implementation
- `/quality` - Runs comprehensive quality assessment
- `/branch merge` - Validates per-task gates before merging
