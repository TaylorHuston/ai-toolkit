---
tags: ["workflow", "strategy", "project-brief", "vision", "planning", "collaboration"]
description: "Fill and improve project brief through gap-driven conversation"
argument-hint: "[--review] [--force]"
allowed-tools: ["Read", "Write", "Edit", "Task", "TodoWrite"]
model: claude-opus-4-1
references_guidelines:
  - docs/development/templates/project-brief.md  # Template structure
---

# /project-brief Command

## WHAT
Fill in and improve project brief through natural, gap-driven conversation - one section at a time, focusing on product vision.

## WHY
Establishes clear product vision and strategy as foundation for feature planning, using conversational approach instead of upfront interrogation.

## HOW

### Usage
```bash
/project-brief           # Gap-driven conversation to fill missing sections
/project-brief --review  # Analyze brief, suggest improvements (no edits)
/project-brief --force   # Start from scratch (recreate brief)
```

### Pre-Execution Context

**Read existing brief:**
- Project brief lives at: `docs/project-brief.md`
- Template structure at: `docs/development/templates/project-brief.md`
- 6 sections: Overview, Problem, Solution, Target Audience, Key Features, Success Metrics
- Analyze completeness: empty (<10 chars), weak (<50 chars), needs_detail (vague), complete
- Identify gaps to fill

**Modes:**
- Default: Fill gaps conversationally
- `--review`: Analysis only, no edits
- `--force`: Recreate from scratch

### Execution Steps

**1. Gap analysis:**
```bash
# Parse sections
# Categorize each: empty, weak, needs_detail, complete
# Section order: Problem → Solution → Target Audience → Key Features → Success Metrics
# Show status summary
```

**2. Invoke brief-strategist agent:**
```
Task: "Complete project brief through gap-driven conversation.

1. Read project brief at docs/project-brief.md
   - Reference template structure from docs/development/templates/project-brief.md if needed
2. For each incomplete section (in order):
   - Ask open question about topic
   - Listen, ask clarifying question
   - Generate section content from answers
   - Show proposed content
   - Get confirmation (yes/edit/skip)
   - Update docs/project-brief.md if approved
   - Ask: Continue to next? (yes/no)
3. Provide progress summary

Be conversational, not formal. User controls pace."
```

**3. Conversational flow per section:**
```
{Section Name}

[Primary question based on section]
> [User answer]

[Clarifying question based on answer]
> [User answer]

Generated content:
---
{AI-generated section content}
---

Does this capture it? (yes/edit/skip)
> [User choice]

# If yes: Update file, move to next
# If edit: Refine content, confirm again
# If skip: Leave as-is, move to next

Continue to {next_section}? (yes/no)
```

**4. Progress tracking:**
- Use TodoWrite for section completion
- Allow stop/resume anytime
- Respect user pace

### Review Mode

**When `--review` flag:**
```bash
# Read brief
# Analyze each section: strengths, weaknesses
# Provide suggestions (specific, actionable)
# No edits made
# Suggest: "Run /project-brief to fill gaps"
```

### Agent Coordination

**Primary agent:** brief-strategist
- Gap-driven conversation
- Section content generation
- User-paced Q&A
- Natural dialogue (not interrogation)

**Living document:** Can stop/resume anytime

### Error Handling

**Brief doesn't exist:**
```
Error: No project brief found.
Run /toolkit-init first to create initial structure.
```

**Force mode confirmation:**
```
Warning: This will recreate brief from scratch.
Existing content will be lost.
Continue? (yes/no)
```

### Integration

**Workflow position:**
```
/toolkit-init → /project-brief → /jira-epic → /plan
```

**Purpose:** Foundation for feature planning, no tech stack (product vision only)