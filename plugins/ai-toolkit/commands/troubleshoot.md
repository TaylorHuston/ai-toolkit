---
tags: ["debugging", "investigation", "problem-solving"]
description: "Structured 5-step troubleshooting loop (Research → Hypothesize → Implement → Test → Document) to avoid shotgun debugging"
argument-hint: "[BUG-XXX | TASK-XXX]"
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep", "Glob", "WebSearch", "WebFetch", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/troubleshooting.md  # 5-step loop, debug logging, WORKLOG format
---

# /troubleshoot

**WHAT**: Systematic debugging with research-first approach, one hypothesis at a time, mandatory validation, and rollback on failure.
**WHY**: Use when encountering bugs or unexpected behavior during implementation to avoid shotgun debugging anti-pattern.
**HOW**: See troubleshooting.md for complete 5-step loop, debug logging practices, and troubleshooting WORKLOG format.

## Usage

```bash
/troubleshoot              # During task (uses existing WORKLOG)
/troubleshoot BUG-007      # Standalone bug investigation
/troubleshoot --continue   # Next iteration after failed attempt
```

## Pre-Execution Context

**Load development context** (read these files before proceeding):

```bash
Read: CLAUDE.md
Read: docs/development/workflows/troubleshooting.md
Read: docs/development/workflows/worklog-format.md
```

**If working on existing issue**:
```bash
Read: pm/issues/{ISSUE-ID}/WORKLOG.md
Read: pm/issues/{ISSUE-ID}/PLAN.md (if exists)
```

**Then execute troubleshooting loop below.**

## Execution Steps

### 1. Research (Don't Guess)

**Priority order: Context7 → Official docs → User guidance**

```bash
# If library/framework involved
Task: Use ai-llm-expert to get library docs from Context7

# If no Context7 docs
WebSearch: "[technology] [error message] official documentation"
WebFetch: [official docs URL]
```

**Ask user**:
- "Do you have links to relevant docs or examples?"
- "Are there canonical patterns in your codebase I should follow?"

**Reference**: See troubleshooting.md for research approach and Context7 priority.

### 2. Hypothesize

**Form ONE hypothesis** based on research:
- What's wrong, why, and what will fix it
- Debug plan (where to log, what to check)
- Reference docs/examples found

### 3. Implement

**Apply solution with liberal debug logging:**

```bash
Edit: [files from hypothesis]
```

**Add debug statements**:
- Function entry/exit
- Variable values at decisions
- Before/after state changes
- Use prefixes: `[ComponentName]`, `[functionName]`

**Reference**: See troubleshooting.md for debug logging examples and best practices.

**Debug/investigation scripts:**

If you need scripts for debugging, place them in the task directory:

```
pm/issues/BUG-007-session-timeout/
└── scripts/
    ├── reproduce-issue.sh       # Script to reproduce the bug
    ├── test-session-edge-cases.js  # Edge case testing
    └── debug-session-cleanup.js    # Debug session lifecycle
```

**Why task-specific scripts?**
- Keep debugging context with the bug investigation
- Easy to find when reviewing WORKLOG later
- Clean up when bug is resolved
- Don't pollute root scripts/ directory with temporary debug scripts

**Root scripts/** should only contain universal utilities, not bug-specific debug scripts.

### 4. Test & Confirm

**Validate thoroughly:**

```bash
Bash: npm test
# Check debug logs
# Reproduce original issue (should be gone)
Bash: npm run test:e2e (if applicable)
```

**CRITICAL**: Never claim "fixed" without confirmation
- If unsure → Ask user to verify
- Example: "Tests passing. Can you verify [behavior] in browser?"

### 5. Document

**Write WORKLOG entry** (5-10 lines):

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] (Loop N)

Hypothesis: [Theory from research]
Debug findings: [Key log insights]
Implementation: [What changed]
Result: ✅ Fixed / ❌ Not fixed - [evidence]

Files: src/file.ts
Manual verification: [Status]
```

**Reference**: See troubleshooting.md for complete troubleshooting WORKLOG format with hypothesis/debug findings structure.

### Loop Continuation

**If NOT fixed**:

```bash
# Rollback
Bash: git checkout -- [modified files]

# Return to Step 1
# Use debug findings to inform next research
```

**If fixed**:
- Ask user about debug cleanup (keep as comments / remove / convert to logger)

## Agent Coordination

**Primary**: project-manager or domain specialist
**Supporting**:
- ai-llm-expert (Context7 docs)
- Domain specialists (implementation)
- code-reviewer (validation)

**Reference**: See troubleshooting.md for loop continuation patterns and agent coordination.

## Error Handling

- **No Context7 docs**: Use WebSearch, ask user for links
- **Hypothesis fails**: Rollback, document, return to Step 1
- **Tests pass but issue persists**: Add more logging, ask user to verify
- **3+ failed loops**: Pause, ask user for additional context
- **User unavailable**: Explicitly state "Awaiting manual verification"

## Integration

**Workflow position**: During `/implement` or standalone bug investigation
**Uses**: Existing WORKLOG.md (task context) or creates BUG-XXX WORKLOG
**Next step**: Return to implementation or close bug issue
