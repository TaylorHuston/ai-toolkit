---
tags: ["context", "workflow", "optimization"]
description: "Silently refresh AI context by reading project configuration, guidelines, and recent commits"
argument-hint: ""
allowed-tools: ["Read", "Bash", "Grep", "Glob"]
model: claude-sonnet-4-5
---

# /refresh Command

Silently refresh AI context by reading critical project files and recent work history. Use this when starting a new conversation or when AI seems to have lost important project context.

## Usage

```bash
/refresh    # Reads all context files silently, no output
```

## Purpose

The `/refresh` command efficiently reloads AI context by reading:

1. **Project configuration** - CLAUDE.md (tech stack, conventions, integrations)
2. **All guidelines** - Every file in `docs/development/guidelines/`
3. **Architecture overview** - `docs/development/architecture-overview.md`
4. **Design overview** - `docs/development/design-overview.md`
5. **Recent work** - Last 3 git commits with full messages

**Use this when:**
- Starting a new conversation and want full project context
- AI seems unaware of project conventions or architecture
- You want AI to be aware of recent work without explaining
- Switching between tasks and want fresh context
- After significant time away from the project

**This does NOT:**
- Make any changes to files
- Output anything to the user (operates silently)
- Read task-specific files (PLAN.md, WORKLOG.md)
- Replace specific context gathering for particular tasks

## What It Does

### Silent Context Refresh

When you run `/refresh`, AI will **silently** (without any output):

1. **Read CLAUDE.md**:
   - Project name, description, tech stack
   - API documentation links
   - Jira integration config
   - Team conventions and preferences

2. **Read all guidelines**:
   - Glob for `docs/development/guidelines/*.md`
   - Read every guideline file found
   - Includes: development-loop.md, naming-conventions.md, code-standards.md, etc.

3. **Read architecture overview**:
   - `docs/development/architecture-overview.md`
   - Current ADRs and technical decisions
   - System architecture patterns

4. **Read design overview**:
   - `docs/development/design-overview.md`
   - UI/UX patterns and design system
   - Component guidelines

5. **Read recent commits**:
   - `git log -3 --format="%h - %s%n%b%n---"`
   - Last 3 commits with full messages and bodies
   - Recent work context and changes

### After Refresh

Once all files are read:
- AI outputs only: `✓ Context refreshed`
- AI now has full project context loaded
- Ready to work with project-specific conventions
- Aware of recent changes and decisions

## Command Behavior

### File Reading Pattern

```bash
# 1. Read CLAUDE.md
Read: CLAUDE.md

# 2. Glob and read all guidelines
Glob: docs/development/guidelines/*.md
Read: docs/development/guidelines/development-loop.md
Read: docs/development/guidelines/naming-conventions.md
Read: docs/development/guidelines/code-standards.md
# ... (all found guidelines)

# 3. Read overviews
Read: docs/development/architecture-overview.md
Read: docs/development/design-overview.md

# 4. Get recent commits
Bash: git log -3 --format="%h - %s%n%b%n---"
```

### Silent Operation

**Critical**: This command operates **silently**. Do NOT:
- Summarize what was read
- List files that were read
- Explain what context was gathered
- Output any information from the files

**Only output**: `✓ Context refreshed`

### Error Handling

**If file not found**:
- Skip missing files silently
- Continue reading other files
- Only fail if CLAUDE.md is missing

**If no guidelines found**:
- Continue without error
- Still read overviews and commits

**If git not available**:
- Skip commit reading
- Continue with file context

## Workflow Examples

### Example 1: Starting New Conversation

```bash
# User starts new conversation, wants fresh context
/refresh

AI: ✓ Context refreshed

# AI now has full project context
# Ready to work with project conventions
```

### Example 2: After Context Loss

```bash
# User notices AI isn't following conventions
User: You're not using the naming conventions we have

/refresh

AI: ✓ Context refreshed

User: Now implement the user profile component

AI: [Now follows naming-conventions.md properly]
```

### Example 3: Combining with Other Commands

```bash
# Refresh before planning a new task
/refresh
/plan TASK-042

# Refresh before reviewing architecture
/refresh
/adr --review
```

## Integration with Workflow

**Position**: Beginning of conversation or before major tasks

```
New Session → /refresh → /plan → /implement
```

**When to use**:
- ✅ Start of new conversation
- ✅ After long pause in work
- ✅ When AI seems unaware of conventions
- ✅ Before planning or implementing
- ✅ After major context loss

**When NOT needed**:
- ❌ Middle of active work (context already loaded)
- ❌ After every command (wasteful)
- ❌ When context is already fresh

## Implementation Notes

When implementing `/refresh`:

1. **Read files silently**:
   - Use Read tool for each file
   - Do NOT output file contents or summaries
   - Load context into conversation history

2. **File reading order**:
   - CLAUDE.md (required)
   - All guidelines (glob pattern)
   - Architecture overview
   - Design overview
   - Recent git commits

3. **Error handling**:
   - If CLAUDE.md missing: Error "CLAUDE.md not found - run /toolkit-init first"
   - If other files missing: Skip silently
   - If git unavailable: Skip commits silently

4. **Final output**:
   - Single line: `✓ Context refreshed`
   - No summaries, no lists, no explanations

5. **Performance**:
   - Read files in sequence (avoid parallel to maintain order)
   - Limit to specified files only (don't read everything)
   - Keep under 30 seconds total

## Related Commands

- `/toolkit-init` - Initialize project structure (creates context files)
- `/plan TASK-###` - Create implementation plan (auto-reads context)
- `/implement TASK-### PHASE` - Execute phase (auto-reads context)
- `/project-status` - Project dashboard (different purpose)

## Notes

- **Silent operation** - Only outputs `✓ Context refreshed`
- **Quick refresh** - Focuses on critical context files only
- **No task-specific** - Doesn't read PLAN.md, WORKLOG.md (those are read by /plan, /implement)
- **Recent commits only** - Last 3 commits, not full history
- **Idempotent** - Safe to run multiple times
- **Read-only** - Never modifies files
