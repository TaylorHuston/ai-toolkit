---
tags: ["workflow", "issue", "task", "bug", "spike", "conversational"]
description: "Create standalone work items (TASK, BUG, or SPIKE) with AI-assisted type detection"
argument-hint: "[\"description\"]"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "TodoWrite"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/task-workflow.md   # Task implementation workflow
  - docs/development/workflows/bug-workflow.md    # Bug fix workflow
  - docs/development/workflows/spike-workflow.md  # Spike exploration workflow
  - docs/development/workflows/pm-workflows.md    # PM artifact hierarchy
---

# /issue Command

**WHAT**: Create standalone work items (TASK, BUG, or SPIKE) through natural conversation.

**WHY**: Single entry point for all standalone work items with AI-assisted type detection and optional spec linkage.

**HOW**: Describe what you need, AI detects type, confirms with you, optionally links to spec, creates issue directory and skeleton file.

## Usage

```bash
/issue                              # Start conversation
/issue "Login crashes on Safari"    # AI detects: BUG
/issue "Add CSV export feature"     # AI detects: TASK
/issue "GraphQL vs REST?"           # AI detects: SPIKE
```

## ID Format

**All issues use numeric IDs.** Type is determined by which file exists in the directory:

```
pm/issues/001-feature-name/TASK.md   → Task (type from file)
pm/issues/002-bug-name/BUG.md        → Bug (type from file)
pm/issues/003-spike-name/SPIKE.md    → Spike (type from file)
pm/issues/PROJ-123-name/TASK.md      → External (Jira) task
```

This unified scheme:
- Single counter for all local issues
- External IDs (PROJ-123) work seamlessly
- Type always determined from file, not ID prefix

## Type Detection

AI detects issue type from description keywords:

| Type | Keywords | Purpose |
|------|----------|---------|
| **BUG** | fix, broken, crash, error, regression, doesn't work, fails | Something is broken |
| **SPIKE** | should we, which, can we, explore, vs, compare, evaluate, feasibility | Technical exploration |
| **TASK** | add, implement, create, build, enhance (default) | New feature or enhancement |

User can always override the detected type.

## Execution Flow

### 1. Gather Description

If no description provided, ask:
> "What do you need to do? Describe the work item."

### 2. Detect Type

Analyze description for keywords:
- BUG indicators: "fix", "broken", "crash", "error", "regression", "fails", "doesn't work"
- SPIKE indicators: "should we", "which", "can we", "explore", "vs", "compare", "feasibility"
- TASK: default if no BUG/SPIKE indicators

Present detection:
> "This sounds like a **BUG**. Create as issue 001? (yes / task / spike)"

### 3. Determine Next Issue Number

```bash
# Count ALL existing issues (single counter)
ls pm/issues/ | grep -E '^[0-9]{3}-' | sort -n | tail -1
# Extract number, increment for next ID
# Format: 001, 002, 003, ...
```

### 4. Ask About Spec Linkage

> "Link to an existing spec? (no / SPEC-001 / SPEC-002 / ...)"

If specs exist, list them. If user selects one, add `spec: SPEC-###` to frontmatter.

### 5. Conversational Details

Based on type, gather required information using the appropriate template:

**For TASK** (templates/task-template.md):
- Description (already have from initial input)
- Acceptance criteria
- Technical notes (optional)

**For BUG** (templates/bug-template.md):
- Description (already have)
- Steps to reproduce
- Expected vs actual behavior
- Severity (critical/high/medium/low)

**For SPIKE** (templates/spike-template.md):
- Questions to answer
- Time box (max hours)
- Deadline (optional)
- Number of approaches to explore

### 6. Create Issue

Create directory and file:

```
pm/issues/###-{slug}/
└── {TYPE}.md    # TASK.md, BUG.md, or SPIKE.md (determines type)
```

**ID format**: 3-digit zero-padded number (001, 002, ...)
**Slug**: kebab-case from description (max 50 chars)

### 7. Next Steps

**For TASK/BUG:**
> "Created issue 001 (task). Next: `/plan 001` to create implementation plan."

**For SPIKE:**
> "Created issue 001 (spike). Next: `/plan 001` to create exploration plans."

If linked to spec, also update SPEC.md Tasks section.

## Example

```
User: /issue "Login page crashes when clicking submit on Safari mobile"

AI: This sounds like a **BUG** - a crash that needs fixing.
    Create as issue 001? (yes / task / spike)

User: yes

AI: Link to an existing spec?
    - SPEC-001: User Authentication
    - SPEC-002: Mobile Experience
    - no (standalone)

User: SPEC-001

AI: Let me gather a few more details for the bug report.

    Steps to reproduce?

User: 1. Open login page on Safari iOS
       2. Enter valid credentials
       3. Click Submit button
       4. Page crashes

AI: Expected vs actual behavior?

User: Expected: Login succeeds. Actual: White screen crash.

AI: Severity? (critical / high / medium / low)

User: high

AI: Created issue 001 (bug): Login Safari Crash

    📁 pm/issues/001-login-safari-crash/BUG.md
    🔗 Linked to SPEC-001: User Authentication

    Next: /plan 001
```

## File Structure

```
pm/issues/
├── 001-csv-export/
│   └── TASK.md          # Type = task (from file)
├── 002-login-safari-crash/
│   └── BUG.md           # Type = bug (from file)
├── 003-graphql-vs-rest/
│   └── SPIKE.md         # Type = spike (from file)
└── PROJ-123-oauth/
    └── TASK.md          # External Jira task
```

## Type Detection from Files

Commands detect issue type by which file exists:

```bash
# In pm/issues/###-name/ directory:
if [ -f "TASK.md" ]; then type="task"
elif [ -f "BUG.md" ]; then type="bug"
elif [ -f "SPIKE.md" ]; then type="spike"
fi
```

This enables:
- Numeric IDs for all local issues
- External IDs (PROJ-123) work without modification
- Type always authoritative from file content

## Integration

**Workflow position:**
```
/issue → /plan {ID} → /implement {ID} → /complete {ID}
```

**Spec linkage:**
- Standalone issues work without a spec
- Linked issues appear in SPEC.md Tasks section
- `spec: SPEC-###` in issue frontmatter enables traceability

**Related commands:**
- `/spec` - Create feature specs with multiple tasks (top-down)
- `/plan` - Create implementation plan for any issue type
- `/implement` - Execute plan phases
- `/complete` - Complete issue per workflow requirements
