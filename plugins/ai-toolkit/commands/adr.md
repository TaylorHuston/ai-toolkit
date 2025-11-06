---
tags: ["workflow", "architecture", "decisions", "adr"]
description: "Create Architecture Decision Records through interactive conversation"
argument-hint: "[\"optional context or topic\"]"
allowed-tools: ["Read", "Write", "Edit", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-opus-4-1
references_guidelines:
  - docs/development/guidelines/architectural-principles.md  # Design principles and patterns to consider
---

# /adr Command

**WHAT**: Create Architecture Decision Records through interactive conversation.

**WHY**: Document significant architectural decisions with context, alternatives, and rationale for future reference.

**HOW**: See architectural-principles.md for design principles and patterns. Interactive 8-step process gathers context and creates ADR.

**IMPORTANT**: Architecture decisions have long-term consequences. Take time to think thoroughly - quality > speed.

## Usage

```bash
/adr                                  # Start conversation
/adr "database selection"             # Provide context
/adr "authentication approach"
/adr "frontend framework"
```

## Process (8 Steps)

1. **Read Context** - Load best practices, template, existing ADRs, architecture overview
2. **Understand Decision** - Read project context, clarify topic
3. **Ask Questions** - ONE at a time, build understanding progressively
4. **Present Options** - 2-3 viable options with trade-offs
5. **Discuss Deeply** - Leverage specialist agents, explore implications
6. **Get Confirmation** - Ensure user understands trade-offs
7. **Create ADR** - Document decision following template
8. **Update Architecture Overview** - Reflect decision in `architecture-overview.md`

## Example Flow

```
User: /adr "database selection"

AI: [Reads best practices, template, existing ADRs, architecture overview]

Looking at your project brief - quotation management app, monolithic, no database yet.
Previous decisions: ADR-002 (auth), ADR-003 (API design).

Data structure needs? (relational/flexible/mix)
User: Primarily relational

Expected scale? (<10k/10k-100k/100k+)
User: <10k users first year

Team experience? (SQL/ORM/NoSQL)
User: SQL + ORM

Hosting? (managed/self-hosted)
User: Managed, minimize ops

Options:
- PostgreSQL (Supabase) - Managed, built-in auth, relational
- PostgreSQL (Self-hosted) - Full control, no vendor lock-in
- MongoDB Atlas - Flexible schema, horizontal scaling

[Consults database-specialist, security-auditor for expert analysis]

AI: Great! Creating ADR-001-use-postgresql-supabase.md
    Updating architecture-overview.md

✓ ADR-001 created and architecture overview updated
```

## Files Read Before Conversation

- `docs/project/adrs/README.md` - Best practices, quality standards
- `docs/project/adrs/adr-template.md` - Required sections (YAML frontmatter)
- `docs/project/adrs/ADR-*.md` - Existing ADRs (decision history, patterns, conflicts)
- `docs/project/architecture-overview.md` - Current architecture state
- `docs/project-brief.md`, `CLAUDE.md` - Project context

**Why read existing ADRs**: Understand history, avoid conflicts, reference related decisions, build on previous choices

## Agent Coordination

**Primary**: `code-architect` (leads conversation, creates ADR)

**Specialists** (consulted via Task tool during step 5):
- `database-specialist` - DB selection, data modeling, scaling
- `devops-engineer` - Infrastructure, deployment, CI/CD
- `security-auditor` - Security architecture, auth, compliance
- `frontend-specialist` - Frontend frameworks, state management
- `backend-specialist` - Backend frameworks, API design
- `performance-optimizer` - Performance, scalability, caching
- `ui-ux-designer` - UX architecture, design systems

**Example**: Database decision → consult database-specialist + security-auditor + performance-optimizer + devops-engineer

## Command Instructions

```
Task: "Create ADR for [topic] through interactive conversation.

CRITICAL MINDSET:
- Architecture decisions have long-term consequences
- Take time - quality > speed
- Consider implications 1/3/5 years out
- Explore edge cases before committing

PROCESS:
1. BEFORE: Read README.md, adr-template.md, ALL ADR-*.md, architecture-overview.md,
   project-brief.md, CLAUDE.md
2. DURING: Ask ONE question at a time, wait for answer, leverage specialist agents
   via Task tool
3. AFTER: Determine ADR number, create ADR-###-<kebab-case>.md, update
   architecture-overview.md, link from epic if relevant

ADR CONTENT:
- Context: Why matters, forces at play, honest problem framing
- Decision: Definitive ("We will use X"), clear rationale
- Alternatives: Options considered, why rejected
- Consequences: Honest trade-offs, negative consequences, long-term impact
- References: Related ADRs, epics, docs

Output ADR documenting conversation and decision."
```

## ADR Content Guidelines

- **Context**: Why this decision matters, forces at play, honest problem framing
- **Decision**: Definitive ("We will use X"), clear rationale, active voice
- **Alternatives**: Real options considered, why each was rejected
- **Consequences**: Honest trade-offs, negative consequences, long-term impact (1/3/5 years)
- **References**: Related ADRs, epics, documentation

## ADR Location & Naming

**Location**: `docs/project/adrs/ADR-###-<kebab-case-title>.md`
**Numbering**: Sequential (scan existing ADR-*.md files for next number)
**Architecture Overview**: Living document - read at start, updated at end

## Workflow Integration

```
/project-brief → /adr → /epic → /plan → /implement
                   ↓
          docs/project/adrs/
                   ↓
        Referenced by epics/tasks
```

**Use `/adr`**:
- After `/project-brief` - Foundational decisions
- Before `/epic` - Establish patterns
- During `/epic` - Epic-specific technical decisions
- Before `/plan` - Document technical approach

## ADR Maintenance

**Status Transitions**:
- Proposed → Accepted (finalized)
- Accepted → Deprecated (no longer recommended)
- Accepted → Superseded by ADR-### (replaced)

**Superseding**: Create new ADR (don't edit old), link bidirectionally, explain why changed

## Related Commands

- `/project-brief` - Project vision informing decisions
- `/epic` - References ADRs in Dependencies
- `/plan` - References ADRs for context
- `/docs` - Generate architecture docs from ADRs
