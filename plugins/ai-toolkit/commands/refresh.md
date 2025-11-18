---
tags: ["context", "workflow", "optimization"]
description: "Silently refresh AI context by reading project configuration, guidelines, and recent commits"
argument-hint: ""
allowed-tools: ["Read", "Bash", "Grep", "Glob"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/development-loop.md  # Context refresh patterns in development workflow
---

# /refresh Command

**WHAT**: Silently reload project context by reading critical configuration files.

**WHY**: Use when starting a new conversation, after context loss, or before major tasks.

**HOW**: See workflow details in project guidelines (files are read, not explained).

## Usage

```bash
/refresh    # Silent context reload
```

## Execution Steps

### 1. Read Project Configuration

```bash
Read: CLAUDE.md
```

If missing: Error "CLAUDE.md not found - run /toolkit-init first"

### 2. Read All Guidelines

```bash
Glob: docs/development/{conventions,workflows,misc,templates}/*.md
# Then read each file found
```

If no guidelines found: Skip silently, continue.

### 3. Read Project Documentation

```bash
Read: docs/project/*.md
```

If files missing: Skip silently, continue.

### 4. Get Recent Work Context

```bash
Bash: git log -3 --format="%h - %s%n%b%n---"
```

If git unavailable: Skip silently, continue.

### 5. Output

```
✓ Context refreshed
```

**Silent operation**: Do NOT summarize files, list what was read, or explain context.

## Error Handling

- **CLAUDE.md missing**: Fail with error message
- **Other files missing**: Skip silently
- **Git unavailable**: Skip git log, continue with files

## Notes

- **Read-only**: Never modifies files
- **Idempotent**: Safe to run multiple times
- **Focused**: Only reads critical context files, not task-specific files (PLAN.md, WORKLOG.md)
