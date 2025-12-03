---
tags: ["workflow", "validation", "quality", "reflection"]
description: "Step back, reflect on current work, validate direction, and assess alignment with plan and architecture"
argument-hint: ""
allowed-tools: ["Read", "Grep", "Glob", "Bash", "mcp__plugin_ai-toolkit_sequential-thinking__sequentialthinking"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/task-workflow.md  # Work standards, quality gates, agent coordination
---

# /sanity-check Command

**WHAT**: Mid-work validation using deep reflection to catch drift early.

**WHY**: Prevent expensive course corrections by validating direction while in progress.

**HOW**: Sequential thinking analysis + context validation + alignment check.

## Usage

```bash
/sanity-check    # Pause, reflect, validate direction
```

**When**: Complexity increasing, feeling uncertain, before major decisions, after 30+ minutes work, something feels off.

**Not for**: Start of work (`/plan`), after completion (`/quality`), loading context (`/refresh`).

## Execution Steps

### 1. Sequential Thinking Reflection

**Use sequential thinking tool:**
- What are we trying to accomplish? (TASK.md/BUG.md goal)
- What have we done? (WORKLOG.md, completed phases)
- Current approach? (technical solution, assumptions)
- Architecture alignment? (ADRs, architecture-overview.md)
- Standards alignment? (task-workflow.md, test-first, quality gates)
- Concerns? (what feels wrong, risks, drift)
- **Decision**: Green (continue), Yellow (adjust), Red (course correct)

### 2. Read Context Files

```bash
# Work context
Read: pm/issues/TASK-###-*/[TASK|BUG].md
Read: PLAN.md
Read: WORKLOG.md

# Standards and architecture
Read: CLAUDE.md
Read: docs/development/workflows/task-workflow.md
Read: docs/project/architecture-overview.md
Read: docs/development/conventions/ui-design-guidelines.md

# Recent history
Bash: git log -5 --format="%h - %s"
```

Skip missing files gracefully.

### 3. Analyze Alignment

**Compare reflection to reality:**
- **Plan**: Following PLAN.md phases? Deviations?
- **Standards**: Test-first? Quality gates per task-workflow.md?
- **Architecture**: ADR consistency? Approved patterns?
- **Design**: Design system usage? Accessibility?

**Categorize concerns:**
- ✅ Green: On track, continue
- ⚠️ Yellow: Minor issues, easy fixes
- 🚩 Red: Major drift, course correction needed

### 4. Provide Assessment

```markdown
## Sanity Check - TASK-###

### Current State
[What's done, current approach]

### Alignment
**Plan**: ✅ | ⚠️ | 🚩 [details]
**Standards**: ✅ | ⚠️ | 🚩 [details]
**Architecture**: ✅ | ⚠️ | 🚩 [details]

### Concerns
✅ What's Working: [positives]
⚠️ Minor Issues: [yellow flags + fixes]
🚩 Critical Issues: [red flags + actions]

### Recommendation
[Continue as-is | Minor adjustment | Course correction | Update plan]

### Next Steps
[Specific actions]
```

## Error Handling

- **Missing files**: Skip gracefully, note if critical file (PLAN.md) missing
- **No concerns**: Provide positive feedback, confirm alignment
- **Multiple red flags**: Prioritize by severity, clear action items

## Integration

**Workflow position**: Mid-work validation

```
/plan → /implement 1.1 → /implement 1.2 → /sanity-check → [adjust if needed] → /implement 1.3 → /quality
```

**Comparison**:
- `/refresh` - Conversation start (load context silently, no analysis)
- `/plan` - Before work (create execution plan, strategic thinking)
- `/sanity-check` - Mid-work (validate direction, **deep reflection**)
- `/implement` - During work (execute phases, tactical)
- `/quality` - After work (assess code quality, review)

## Notes

- **Sequential thinking required** - Key differentiator from `/refresh`
- **Mid-work focus** - For the messy middle, not start or end
- **Permission to pause** - Makes stepping back a workflow step
- **Catch drift early** - Course correction cheap at 45 min, expensive at 4 hours
- **Trust your gut** - If something feels off, run this command
