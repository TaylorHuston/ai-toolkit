---
tags: ["workflow", "development", "collaborative"]
description: "Get implementation guidance for a phase without automated code generation - user implements manually"
argument-hint: "TASK-### PHASE | TASK-### --next | --next"
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep", "Glob", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/pm-guide.md  # Phase structure, quality gates, completion protocol
  - docs/development/guidelines/worklog-format.md  # ADVICE entry format
  - docs/development/guidelines/development-loop.md  # Quality gates, collaborative mode
  - docs/development/guidelines/agent-coordination.md  # Advisory mode agent coordination
---

# /advise Command

**WHAT**: Provide structured implementation guidance for a phase WITHOUT writing code - user implements manually following advice.

**WHY**: Collaborative mode enables hands-on learning, full control over implementation, and balance between automation and manual coding.

**HOW**: See pm-guide.md for phase requirements and completion protocol. See development-loop.md for collaborative implementation mode. See agent-coordination.md for advisory mode patterns.

## Usage

```bash
/advise TASK-001 1.2        # Get guidance for Phase 1.2
/advise BUG-003 2.1         # Get guidance for bug fix phase
/advise PROJ-123 1.3        # Get guidance for Jira issue phase
/advise TASK-001 --next     # Smart: find and advise on next uncompleted phase
/advise --next              # Auto-detect current task, advise next phase
```

**Modes**:
- **Specific phase**: Provide guidance for exact phase number
- **Next phase**: Auto-detect next uncompleted phase from PLAN.md
- **Auto next**: Detect current task from git branch, advise next phase

**Related commands**:
- `/implement` - Automated mode (AI writes code)
- `/advise` - Collaborative mode (you write code, AI guides)
- `/plan` - Create phase breakdown first

---

## Pre-Execution Context

**Load workflow rules** (read before proceeding):

```bash
Read: docs/development/guidelines/pm-guide.md
Read: docs/development/guidelines/worklog-format.md
Read: docs/development/guidelines/development-loop.md
Read: docs/development/guidelines/coding-standards.md
```

**pm-guide.md contains**:
- Phase structure patterns
- Quality gate requirements
- Completion protocol (applies to manual implementation too)

**development-loop.md contains**:
- Collaborative implementation mode workflow
- Quality gates for manual implementation

**worklog-format.md contains**:
- ADVICE entry format for documenting guidance

---

## Execution Steps

### 1. Parse Parameters and Locate Phase

```bash
# Parse issue ID and phase number (or detect next phase)
# Locate issue directory: pm/issues/{ISSUE-ID}-*/
Read: TASK.md/BUG.md
Read: PLAN.md
```

**Smart next mode** (TASK-001 --next):
- Read PLAN.md, find first uncompleted phase
- Show phase to user, ask confirmation
- Continue to step 2

**Auto next mode** (--next only):
- Detect current task from git branch or recent WORKLOG
- Read PLAN.md, find first uncompleted phase
- Show phase to user, ask confirmation
- Continue to step 2

### 2. Analyze Context

```bash
Read: CLAUDE.md
Read: docs/development/guidelines/coding-standards.md
Read: docs/project/architecture-overview.md
Grep: Existing code patterns in codebase
```

**Analyze**:
- Phase requirements from PLAN.md
- Existing code patterns and conventions
- Related implementations in codebase
- Project architectural constraints

### 3. Invoke Agents in Advisory Mode

**Use Task tool** to invoke agents in advisory mode (no code generation):

**code-architect** (always):
- Review phase requirements
- Suggest architectural approach
- Identify integration points
- Recommend patterns
- **Output**: Structural guidance only

**security-auditor** (if phase matches security keywords/patterns):
- Identify security considerations
- Suggest secure patterns
- List vulnerabilities to avoid
- Provide security testing guidance
- **Output**: Security recommendations only

**test-engineer** (always):
- Suggest test strategy
- Propose test cases
- Recommend test structure
- **Output**: Testing guidance only

### 4. Compile Structured Guidance

**Generate advice document** with sections:
1. **Suggested Approach** - High-level strategy
2. **Implementation Details** - Technical specifics (signatures, types, patterns)
3. **Files to Create/Modify** - Explicit file-level guidance
4. **Security Considerations** - OWASP vulnerabilities to avoid (if applicable)
5. **Testing Strategy** - Test scenarios and edge cases
6. **Code Examples** - Minimal scaffolding (interfaces, signatures)
7. **Related Documentation** - Links to guidelines

**Keep it actionable**: Specific enough to guide, general enough to allow creativity.

### 5. Create WORKLOG Entry

```bash
Write: pm/issues/{ISSUE-ID}-*/WORKLOG.md
```

**ADVICE entry format**:
```markdown
## YYYY-MM-DD HH:MM - [ADVICE: Phase X.Y]

Provided implementation guidance for [brief phase description].

**Approach**: [High-level strategy]
**Files**: [Key files to create/modify]
**Security**: [Security considerations if applicable]
**Testing**: [Test strategy summary]

→ User will implement phase manually
```

### 6. Present Guidance to User

Output structured advice document for user to follow.

---

## After Receiving Guidance

User follows this flow (see development-loop.md for collaborative mode details):

1. **Code** - Implement following guidance, ask questions naturally as needed
2. **Document** - Use `/comment` to log work and AI assistance received
3. **Complete** - Mark phase checkbox in PLAN.md manually
4. **Commit** - Follow phase commit workflow (see pm-guide.md)
5. **Review** (optional) - Ask naturally: "Can you review my implementation in [file]?"

**Quality gates apply**: Same per-phase gates as `/implement` (see development-loop.md).

---

## Mode Comparison

| Aspect | /implement (Automated) | /advise (Collaborative) |
|--------|----------------------|------------------------|
| Code writing | AI writes all code | User writes code |
| Speed | Fast | Slower (manual) |
| Learning | Less hands-on | More hands-on |
| Control | AI controls details | User controls details |
| Best for | Boilerplate, standard patterns | Complex logic, learning |

**Hybrid approach** (recommended):
```bash
/implement TASK-001 1.1  # AI: boilerplate
/advise TASK-001 1.2     # User: complex logic
/implement TASK-001 1.3  # AI: testing
```

---

## Examples

See development-loop.md "Collaborative Implementation Mode" for complete examples and workflow patterns.
