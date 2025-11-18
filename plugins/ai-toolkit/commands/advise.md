---
tags: ["workflow", "development", "collaborative"]
description: "Get implementation guidance for a phase without automated code generation - user implements manually"
argument-hint: "TASK-### PHASE | TASK-### --next | --next"
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep", "Glob", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/pm-workflows.md  # Phase structure, quality gates, completion protocol
  - docs/development/workflows/worklog-format.md  # ADVICE entry format
  - docs/development/workflows/development-loop.md  # Quality gates, collaborative mode
  - docs/development/workflows/agent-coordination.md  # Advisory mode agent coordination
---

# /advise Command

**WHAT**: Provide structured implementation guidance for a phase WITHOUT writing code - user implements manually following advice.

**WHY**: Collaborative mode enables hands-on learning, full control over implementation, and balance between automation and manual coding.

**HOW**: See pm-guide.md for phase requirements and completion protocol. See development-loop.md for collaborative implementation mode. See agent-coordination.md for advisory mode patterns.

## Usage

```bash
/advise TASK-001 1.2        # Get guidance for Phase 1.2
/advise TASK-001 --next     # Smart: find and advise on next uncompleted phase
/advise --next              # Auto-detect current task, advise next phase
```

## Execution Flow

**Before you start**: Read pm-guide.md for phase requirements and completion protocol. Read development-loop.md for collaborative implementation mode. Read agent-coordination.md for advisory mode patterns.

### High-Level Steps

1. **Parse & Locate**
   - Parse issue ID and phase number (or auto-detect next)
   - Locate issue directory: pm/issues/{ISSUE-ID}-*/
   - Read TASK.md/BUG.md, PLAN.md

2. **Analyze Context**
   - Read CLAUDE.md, coding-standards.md, architecture-overview.md
   - Grep existing code patterns in codebase
   - Understand phase requirements and project constraints

3. **Invoke Advisory Agents**
   - code-architect: Architectural approach, patterns, integration points
   - security-auditor: Security considerations (if applicable)
   - test-engineer: Test strategy and scenarios
   - **All agents in advisory mode**: No code generation, guidance only

4. **Compile Guidance Document**
   - Suggested approach (high-level strategy)
   - Implementation details (signatures, types, patterns)
   - Files to create/modify
   - Security considerations (if applicable)
   - Testing strategy
   - Code examples (minimal scaffolding)

5. **Create WORKLOG Entry**
   - Write ADVICE entry per worklog-format.md
   - Document guidance provided
   - Mark phase as advised

6. **Present to User**
   - Output structured advice for manual implementation
   - User implements following guidance
   - User marks phase complete in PLAN.md
   - Same quality gates apply as /implement

**See development-loop.md "Collaborative Implementation Mode" for complete workflow and examples.**

## Mode Comparison

**`/implement`** (Automated): AI writes code, fast, best for boilerplate/standard patterns
**`/advise`** (Collaborative): User writes code with AI guidance, hands-on learning, best for complex logic

**Hybrid approach** (recommended): `/implement` for boilerplate → `/advise` for complex logic → `/implement` for tests

See development-loop.md for complete workflow patterns and examples.
