---
tags: ["workflow", "spike", "exploration", "technical-research", "conversational"]
description: "Create time-boxed technical explorations to answer feasibility and approach questions"
argument-hint: "[SPIKE-### | --complete SPIKE-###]"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Task", "TodoWrite"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/spike-workflow.md  # Complete spike workflows, templates, examples
  - docs/development/workflows/pm-workflows.md    # PM artifact hierarchy
---

# /spike Command

**WHAT**: Create time-boxed technical exploration to answer "Can we?" or "Which approach?" questions.

**WHY**: Prevent costly wrong technical decisions by exploring alternatives in a structured, documented way before committing to implementation.

**HOW**: Create SPIKE.md with questions to answer, use `/plan --spike` for exploration plans, `/implement --spike` for prototyping (no commits), generate findings summary.

## Usage

```bash
/spike                        # Start conversation (create new spike)
/spike SPIKE-###              # Work with existing spike
/spike --complete SPIKE-###   # Generate findings summary, create follow-ups
```

## When to Use Spike

**Use spike when:**
- Technical approach is uncertain
- Need to compare 2+ alternatives (GraphQL vs REST, Library A vs B)
- Feasibility is unknown ("Can we even do this?")
- High risk that prototyping would reduce
- Learning new tech before committing to implementation

**Don't use spike when:**
- Approach is clear from requirements
- Team has done similar work before
- Just need to read docs (no hands-on exploration needed)
- Uncertainty is in requirements, not technology

**See spike-workflow.md for detailed decision criteria and examples.**

## Execution Flow

**Before you start**: Read spike-workflow.md for complete workflows, file templates, and integration patterns.

### Create New Spike

1. **Determine Mode**
   - No arguments: Create new spike
   - SPIKE-### provided: Work with existing spike
   - --complete SPIKE-###: Generate summary and follow-ups

2. **Load Context**
   - Read docs/development/templates/spike-template.md for structure
   - Glob pm/issues/SPIKE-*/SPIKE.md for next number
   - If related spec provided, read SPEC-###.md for context

3. **Conversational Creation**
   - Follow templates/spike-template.md section prompts
   - What questions need to be answered?
   - Time box (max hours)? Deadline?
   - Related spec/task (if any)?
   - How many approaches to explore?
   - For each approach: Brief description

4. **Create Spike File**
   - Create pm/issues/SPIKE-###-description/ directory
   - Write SPIKE.md using templates/spike-template.md structure
   - Fill in all template sections from conversational responses
   - Follow templates/spike-template.md frontmatter and format

5. **Next Steps**
   - Inform user: "Run `/plan --spike SPIKE-###` to create exploration plans"
   - If Jira mode enabled, ask: "Create Jira spike?" (use /jira-spike)

### Complete Spike (`--complete SPIKE-###`)

**When to use:** After all explorations complete, to consolidate findings and create follow-ups.

**Workflow:**

1. **Load Context**
   - Read SPIKE-###.md
   - Read all WORKLOG-*.md files for findings
   - Read all PLAN-*.md files for context

2. **Generate Draft Summary**
   - Create SPIKE-SUMMARY.md with:
     - Executive summary with recommendation
     - Questions answered (one section per question)
     - Approach comparison table
     - Recommendation rationale
     - Next steps (tasks, ADRs, spec updates)
   - Use spike-workflow.md SPIKE-SUMMARY.md template
   - Pull findings from WORKLOG-*.md files

3. **User Refinement**
   - Present draft to user
   - User edits/refines before finalizing
   - Save finalized SPIKE-SUMMARY.md

4. **Prototype Code Decision**
   - Ask: "Keep prototype code for reference? (yes/no)"
   - If no: Delete prototype/ directory
   - If yes: Keep for reference (will be committed with docs)

5. **Follow-up Actions** (interactive)
   - Ask: "Create ADR to document decision?"
     - If yes: Run /adr with spike findings as evidence
   - Ask: "Update related spec?"
     - If yes: Add technical approach section to SPEC.md
   - Ask: "Create implementation tasks?"
     - If yes: Create TASK.md files based on chosen approach

6. **Commit Spike Documentation**
   - git add pm/issues/SPIKE-###/
   - Commit with message: "docs: SPIKE-### findings - [recommendation]"
   - Update SPIKE.md status to "complete"

**See spike-workflow.md for complete templates and examples.**

## Integration with Other Commands

**Related commands:**
- `/plan --spike SPIKE-###` - Create exploration plans (works like regular /plan but for spike)
- `/implement --spike SPIKE-### --plan N` - Execute exploration without commits
- `/jira-spike "question"` - Create spike in Jira (if Jira mode enabled)

**Workflow integration:**
- Spikes created BEFORE planning when technical uncertainty exists
- `/plan` blocks if referenced spike incomplete
- Spike findings inform `/adr` decision documentation
- Spike findings referenced in SPEC.md technical approach

**See spike-workflow.md for complete integration patterns and examples.**

## File Structure

```
pm/issues/SPIKE-###-description/
├── SPIKE.md                # Spike definition (questions, approaches, criteria)
├── PLAN-1.md              # Exploration plan for approach 1
├── WORKLOG-1.md           # Findings from approach 1 exploration
├── PLAN-2.md              # Exploration plan for approach 2
├── WORKLOG-2.md           # Findings from approach 2 exploration
├── SPIKE-SUMMARY.md       # Consolidated findings + recommendation
└── prototype/             # Exploration code (optional: kept or deleted)
    ├── approach-1/
    └── approach-2/
```

## Design Philosophy

**Minimal command, convention-driven:**
- Command creates SPIKE.md and orchestrates completion
- Heavy workflows delegated to spike-workflow.md
- Reuses existing `/plan` and `/implement` infrastructure
- File templates and examples in spike-workflow.md

**Time-boxed exploration:**
- Must end even if questions not fully answered
- Actual time tracked vs time box
- Prevents analysis paralysis

**Actionable output:**
- SPIKE-SUMMARY.md with clear recommendation
- Creates tasks, ADRs, updates specs
- Prototype code optional (kept for reference or deleted)

**See spike-workflow.md for complete philosophy, patterns, and best practices.**
