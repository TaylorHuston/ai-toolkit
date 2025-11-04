---
tags: ["workflow", "validation", "quality", "reflection"]
description: "Step back, reflect on current work, validate direction, and assess alignment with plan and architecture"
argument-hint: ""
allowed-tools: ["Read", "Grep", "Glob", "Bash", "mcp__plugin_ai-toolkit_sequential-thinking__sequentialthinking"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/development-loop.md  # Work standards, quality gates, agent coordination
---

# /sanity-check Command

**Purpose**: Mid-work validation to ensure you're still on the right path. Uses deep reflection and sequential thinking to catch drift before it becomes expensive.

## Usage

```bash
/sanity-check    # Pause, reflect, validate direction
```

**When to use:**
- Things are getting more complicated than expected
- You're unsure if your approach is still correct
- Before making a significant architectural decision
- You've been working for 30+ minutes without checking in
- Something feels off but you can't articulate why
- About to commit to a direction that will be hard to reverse

**NOT for:**
- Start of work (use `/plan` instead)
- After work completion (use `/quality` instead)
- Just loading context (use `/refresh` instead)

## Purpose

The `/sanity-check` command provides **mid-work course correction** by:

1. **Stepping back mentally** - Using sequential thinking to reflect on current work
2. **Refreshing critical context** - Re-reading plan, guidelines, ADRs, work log
3. **Validating alignment** - Checking if work matches plan and follows standards
4. **Flagging concerns** - Identifying issues early when they're cheap to fix
5. **Recommending action** - Clear guidance on whether to continue or adjust

**The gap it fills:**

```
/plan          → Planning (before work)
/implement     → Execution (during work)
/sanity-check  → Validation (mid-work pause) ← THIS
/quality       → Assessment (after work)
```

## How It Works

### 1. Use Sequential Thinking for Reflection

**First, reflect deeply using the sequential thinking tool:**

Think through these questions systematically:

**What are we trying to accomplish?**
- Read the original issue (TASK.md or BUG.md)
- What was the stated goal?
- What problem are we solving?
- Who is this for and why?

**What have we done so far?**
- Read WORKLOG.md for work history
- What phases have been completed?
- What decisions have been made?
- What gotchas or lessons learned?

**What's our current approach?**
- What technical solution are we implementing?
- Why did we choose this approach?
- What assumptions are we making?
- Are we following the plan?

**How does this align with architecture?**
- Does this match existing ADRs?
- Are we consistent with architecture-overview.md?
- Are we following approved design patterns?
- Any architectural concerns?

**How does this align with standards?**
- Are we following development-loop.md?
- Test-first approach being used?
- Quality gates being met?
- Agent coordination appropriate?

**What concerns exist?**
- What feels wrong or complicated?
- Where might we be drifting?
- What risks do we face?
- What could go badly?

**Continue as-is or course correct?**
- Green: On track, continue
- Yellow: Minor concerns, small adjustments
- Red: Significant issues, major correction needed

### 2. Read Critical Context Files

**After reflection, validate with actual files:**

```bash
# Current work context
Read: pm/issues/TASK-###-*/TASK.md (or BUG.md)
Read: pm/issues/TASK-###-*/PLAN.md
Read: pm/issues/TASK-###-*/WORKLOG.md

# Project standards
Read: CLAUDE.md
Read: docs/development/guidelines/development-loop.md
Glob + Read: docs/development/guidelines/*.md (other guidelines)

# Architecture & design
Read: docs/project/architecture-overview.md
Read: docs/project/design-overview.md

# Recent commits
Bash: git log -5 --format="%h - %s"
```

**Skip missing files gracefully** - if a file doesn't exist, continue without error.

### 3. Analyze Alignment

Compare your sequential thinking reflection against actual files:

**Plan alignment:**
- Are we executing phases as written in PLAN.md?
- Have we deviated from planned approach?
- Are phases still relevant or need updating?

**Standard alignment:**
- Following test-first pattern from development-loop.md?
- Using correct complexity scoring?
- Appropriate agent coordination?
- Quality gates being met?

**Architecture alignment:**
- Consistent with ADRs in architecture-overview.md?
- Following approved patterns?
- No architectural anti-patterns introduced?

**Design alignment:**
- Following design patterns from design-overview.md?
- UI components match design system?
- Accessibility and UX standards met?

### 4. Flag Concerns

Categorize issues found:

**✅ Green (Continue):**
- Work matches plan
- Standards being followed
- Architecture respected
- On schedule
- No significant concerns

**⚠️ Yellow (Minor Adjustment):**
- Small deviation from plan (explainable)
- One or two standards violations (easily fixed)
- Minor refactoring opportunity
- Slightly behind schedule
- Fixable without major rework

**🚩 Red (Course Correction Needed):**
- Major deviation from plan
- Multiple standard violations
- Architectural concerns
- Significantly behind schedule
- Current approach won't work
- Need to revisit plan

### 5. Provide Assessment

**Output format:**

```markdown
## Sanity Check - TASK-###

### Current State
[Brief summary of what's been done and current approach]

### Alignment Analysis

**Plan:** ✅ | ⚠️ | 🚩
[Details on plan alignment]

**Standards:** ✅ | ⚠️ | 🚩
[Details on development-loop.md compliance]

**Architecture:** ✅ | ⚠️ | 🚩
[Details on ADR/architecture-overview.md consistency]

**Design:** ✅ | ⚠️ | 🚩
[Details on design-overview.md compliance]

### Concerns

**✅ What's Working:**
- [List positives]

**⚠️ Minor Issues:**
- [List yellow flags with suggested fixes]

**🚩 Critical Issues:**
- [List red flags with required actions]

### Recommendation

[One of:]
- **Continue as-is**: No significant concerns, keep executing
- **Minor adjustment**: Small corrections needed, then continue
- **Course correction**: Stop and fix critical issues before proceeding
- **Update plan**: Work is fine but plan needs revision to reflect reality

### Next Steps
[Specific actionable guidance]
```

## Workflow Examples

### Example 1: Green Light (Continue)

```bash
# Working on TASK-042, 45 minutes in, feeling uncertain
/sanity-check

AI: [Uses sequential thinking to reflect...]
    [Reads PLAN.md, WORKLOG.md, guidelines...]

    ## Sanity Check - TASK-042

    ### Current State
    Implementing rate limiting middleware. Completed phase 1.1 (tests)
    and 1.2 (Redis integration). Currently in 1.3 (HTTP 429 responses).

    ### Alignment Analysis

    **Plan:** ✅ Following PLAN.md phases exactly

    **Standards:** ✅ Test-first approach used throughout

    **Architecture:** ✅ Consistent with ADR-008 (caching strategy)

    **Design:** N/A (backend-only work)

    ### Concerns

    **✅ What's Working:**
    - Test coverage at 98%
    - Redis integration clean
    - Following development-loop.md patterns
    - On schedule per plan

    **⚠️ Minor Issues:**
    - None

    **🚩 Critical Issues:**
    - None

    ### Recommendation

    **Continue as-is**: Work is aligned with plan and standards.
    Current approach is solid. Proceed with phase 1.3.

    ### Next Steps
    Complete HTTP 429 response implementation, then move to phase 2.1.

User: Great! [continues working with confidence]
```

### Example 2: Yellow Warning (Minor Adjustment)

```bash
/sanity-check

AI: [Sequential thinking reveals some drift...]

    ## Sanity Check - TASK-087

    ### Current State
    Building user profile component. Added state management,
    form validation, and avatar upload. Working on edit mode.

    ### Alignment Analysis

    **Plan:** ⚠️ Minor deviation - added avatar upload not in original plan

    **Standards:** ✅ Test-first approach maintained

    **Architecture:** ✅ Follows component architecture

    **Design:** ⚠️ Avatar component doesn't match design system

    ### Concerns

    **✅ What's Working:**
    - Core profile functionality solid
    - Good test coverage
    - Clean component structure

    **⚠️ Minor Issues:**
    - Avatar upload was scope creep (not in TASK.md requirements)
    - Avatar component uses custom styling instead of design system
    - Missing accessibility attributes on avatar upload

    **🚩 Critical Issues:**
    - None

    ### Recommendation

    **Minor adjustment**: Small corrections needed before proceeding.

    1. Update PLAN.md to reflect avatar upload addition (document decision)
    2. Refactor avatar component to use design system components
    3. Add accessibility attributes (aria-label, role)

    Then continue with edit mode implementation.

    ### Next Steps
    Make the 3 corrections above (15 min), then proceed with phase 2.2.

User: Good catch! Let me fix those issues.
```

### Example 3: Red Flag (Course Correction)

```bash
/sanity-check

AI: [Sequential thinking identifies major drift...]

    ## Sanity Check - TASK-112

    ### Current State
    Implementing authentication system. Built custom JWT solution
    with Redis session storage, bcrypt password hashing, and
    refresh token rotation.

    ### Alignment Analysis

    **Plan:** 🚩 Major deviation - plan called for using NextAuth.js

    **Standards:** 🚩 Skipped test-first approach (no tests yet)

    **Architecture:** 🚩 Contradicts ADR-012 (use established auth libs)

    **Design:** ⚠️ Login UI not following design-overview.md patterns

    ### Concerns

    **✅ What's Working:**
    - Code quality is decent
    - Security practices (bcrypt, token rotation) are good

    **⚠️ Minor Issues:**
    - Login UI styling inconsistent

    **🚩 Critical Issues:**
    - **Reinventing the wheel**: PLAN.md explicitly called for NextAuth.js
    - **No tests**: 300+ lines of security-critical code without tests
    - **Ignoring ADR**: ADR-012 states "Use established auth libraries, don't build custom auth"
    - **Security risk**: Custom auth is high-risk without security audit
    - **Time**: Spent 2 hours on something that should take 30 min with NextAuth.js

    ### Recommendation

    **Course correction required**: Stop current approach.

    **Critical decision**: Continue with custom auth OR switch to NextAuth.js?

    **If custom auth** (not recommended):
    - Get security-auditor review immediately
    - Write comprehensive tests (TDD retroactively)
    - Update PLAN.md and get approval for scope change
    - Expect additional 4-6 hours work

    **If NextAuth.js** (recommended):
    - Revert custom auth implementation
    - Follow original PLAN.md approach
    - Implement with NextAuth.js (test-first)
    - Back on track in 1-2 hours

    ### Next Steps

    1. Review ADR-012 rationale (why we chose established libs)
    2. Discuss with team/stakeholder: custom vs NextAuth.js
    3. If NextAuth.js: revert and restart with correct approach
    4. If custom: get security review and write tests first

User: Oh wow, I completely forgot about ADR-012. Let me revert and use NextAuth.js.
```

## Integration with Workflow

**Position**: Mid-work validation point

```
/plan TASK-042
  ↓
/implement TASK-042 1.1
  ↓
/implement TASK-042 1.2
  ↓
[working... complexity increasing... feels off]
  ↓
/sanity-check  ← PAUSE AND REFLECT
  ↓
[assessment: green/yellow/red]
  ↓
[adjust if needed]
  ↓
/implement TASK-042 1.3
  ↓
/quality
```

**Comparison to related commands:**

| Command | When | Purpose | Thinking |
|---------|------|---------|----------|
| `/refresh` | Conversation start | Load context silently | None |
| `/plan` | Before work | Create execution plan | Strategic |
| `/sanity-check` | Mid-work | Validate direction | **Deep reflection** |
| `/implement` | During work | Execute phases | Tactical |
| `/quality` | After work | Assess code quality | Review |

## Command Behavior

### Sequential Thinking Usage

**CRITICAL**: This command MUST use sequential thinking tool for reflection.

**Why:**
- Provides meta-cognitive analysis
- Catches assumptions and blind spots
- Forces structured thinking about direction
- Creates transparency in decision-making
- Allows course correction at thought level

**Pattern:**
1. Use sequential thinking tool first (reflection phase)
2. Read context files second (validation phase)
3. Compare thinking to reality (alignment phase)
4. Output assessment (recommendation phase)

### Silent Reading vs Output

**Silent (don't show user):**
- File contents being read
- Individual file summaries
- Technical details from files

**Show user:**
- Sequential thinking process (via tool output)
- Alignment analysis summary
- Flagged concerns
- Recommendation and next steps

### Error Handling

**Missing files:**
- Skip gracefully, continue with available files
- Note in output if critical file missing (e.g., PLAN.md)

**No concerns found:**
- Still provide positive feedback
- Confirm alignment
- Encourage continuation

**Multiple red flags:**
- Prioritize by severity
- Provide clear action items
- Don't overwhelm with details

## Implementation Notes

When implementing `/sanity-check`:

1. **Use sequential thinking first**:
   - Reflect on current work
   - Question assumptions
   - Identify concerns
   - Formulate hypotheses

2. **Read context files**:
   - TASK.md/BUG.md (requirements)
   - PLAN.md (intended approach)
   - WORKLOG.md (work history)
   - CLAUDE.md (project config)
   - Guidelines (standards)
   - Overviews (architecture/design)
   - Recent commits (recent changes)

3. **Compare and analyze**:
   - Does reality match reflection?
   - Does work match plan?
   - Are standards being followed?
   - Is architecture respected?

4. **Flag concerns by severity**:
   - ✅ Green: Continue
   - ⚠️ Yellow: Minor fix needed
   - 🚩 Red: Stop and correct

5. **Provide clear recommendation**:
   - One of: continue, adjust, correct, update plan
   - Specific next steps
   - Time estimate for adjustments

## Related Commands

- `/refresh` - Silent context loading (no analysis)
- `/plan TASK-###` - Create implementation plan (before work)
- `/implement TASK-### PHASE` - Execute phase (during work)
- `/quality` - Code quality assessment (after work)
- `/adr` - Architecture decisions (design phase)

## Notes

- **Use sequential thinking** - This is the key differentiator from `/refresh`
- **Mid-work focus** - Not for start or end, for the messy middle
- **Permission to pause** - Makes "stepping back" a workflow step
- **Catch drift early** - Course correction is cheap at 45 minutes, expensive at 4 hours
- **Not a failure** - Using this command is smart, not a sign of weakness
- **Trust your gut** - If something feels off, run this command
