---
tags: ["workflow", "strategy", "project-brief", "vision", "planning", "collaboration"]
description: "Fill and improve project brief through gap-driven conversation"
argument-hint: "[--review] [--force]"
allowed-tools: ["Read", "Write", "Edit", "Task", "TodoWrite"]
model: claude-opus-4-1
references_guidelines:
  - docs/project-brief.md  # Project brief template structure and required sections
---

# /project-brief Command

**Purpose**: Fill in and improve your project brief through natural, gap-driven conversation.

## Usage

```bash
/project-brief           # Gap-driven conversation to fill missing sections
/project-brief --review  # Analyze brief and suggest improvements (no edits)
/project-brief --force   # Start from scratch (recreate brief)
```

## Philosophy

**Living document approach**:
- Brief initialized with 6 sections by `/toolkit-init`
- Fill gaps through natural conversation
- One section at a time, user-paced
- Can stop/resume anytime
- Product vision only (no tech stack)

## Command Modes

### 1. Gap-Driven Mode (Default)

**Triggers**: Brief exists (from `/toolkit-init` or previous run)

**Behavior**:
1. Read `docs/project-brief.md`
2. Analyze which sections are empty/weak
3. Conversational Q&A to fill gaps
4. One section at a time
5. User controls pace

**Conversation Pattern**:
```
Reading your brief...

Current state:
✓ Overview (complete)
⚠ Problem (needs work)
✗ Solution (empty)
✗ Target Audience (empty)
✗ Key Features (empty)
✗ Success Metrics (empty)

Let's start with Problem. What problem does {app-name} solve?
> [User answers]

What's the impact of that problem on users?
> [User answers]

✓ Updated Problem section

Continue to Solution? (yes/no)
> yes

How does {app-name} solve this problem?
> [User answers]

[Continues conversationally...]
```

**Section Order**: Problem → Solution → Target Audience → Key Features → Success Metrics

**For Each Section**:
- Ask open-ended question
- Listen to answer
- Ask clarifying/deepening question
- Generate section content from conversation
- Show generated content
- Ask: "Does this capture it? (yes/edit/skip)"
- Update file
- Ask: "Continue to {next}? (yes/no)"

### 2. Review Mode (`--review`)

**Triggers**: `--review` flag + brief exists

**Behavior**:
- Read brief
- Analyze completeness and quality
- Provide suggestions
- **No edits made**

**Output**:
```
Project Brief Analysis:

Strengths:
- Clear problem statement
- Well-defined target audience

Needs Work:
- Solution section lacks detail on "how"
- Key Features are vague
- No measurable Success Metrics

Suggestions:
1. Solution: Add specific approach/methodology
2. Features: List 3-5 concrete capabilities
3. Metrics: Define 2-3 measurable indicators

Run /project-brief (without --review) to fill these gaps.
```

### 3. Force Mode (`--force`)

**Triggers**: `--force` flag

**Behavior**:
- Ignore existing brief
- Create fresh brief with 6 empty sections
- Start gap-driven conversation from scratch

**Use When**:
- Current brief is completely wrong
- Major pivot in product vision
- Want to start over

## Brief Structure (6 Sections)

### 1. Overview
**Initialized by `/toolkit-init`** with app description

**Example**:
```markdown
## Overview
Helps solar installers create quotes faster by automating calculations
and pulling real-time pricing data.
```

### 2. Problem
**What user pain does this solve?**

**Questions**:
- What problem does {app} solve?
- Who experiences this problem?
- What's the impact/cost of this problem?

**Example**:
```markdown
## Problem
Solar installers spend 2-3 hours per quote doing manual calculations,
looking up component prices, and creating proposals. This slow process
causes them to lose deals to faster competitors and limits their ability
to scale their business.
```

### 3. Solution
**How does this solve the problem?**

**Questions**:
- How does {app} solve this?
- What's your approach?
- Why does this approach work?

**Example**:
```markdown
## Solution
SolarQuote automates quote generation by:
- Pre-built templates for common installation types
- Real-time pricing API integration with major suppliers
- Automated calculation engine for ROI and payback periods
- Professional PDF proposal generation in minutes

This reduces quote time from hours to minutes while improving accuracy.
```

### 4. Target Audience
**Who uses this?**

**Questions**:
- Who are your primary users?
- What characterizes them?
- What are their specific needs?

**Example**:
```markdown
## Target Audience
- **Primary**: Small to mid-size solar installation companies (2-20 employees)
- **Secondary**: Independent solar contractors
- **Characteristics**: Tech-savvy, growth-focused, overwhelmed by manual processes
- **Needs**: Speed, accuracy, professional presentation, integration with existing tools
```

### 5. Key Features
**What does it do?**

**Questions**:
- What are the core capabilities?
- What makes this valuable?
- What 3-5 features matter most?

**Example**:
```markdown
## Key Features
1. **Template Library**: Pre-configured templates for residential, commercial, and industrial installations
2. **Real-Time Pricing**: Live supplier pricing integration (30+ major distributors)
3. **Auto Calculations**: Automated ROI, payback period, and incentive calculations
4. **Proposal Generator**: Professional PDF proposals with branding and e-signature
5. **CRM Integration**: Syncs with Salesforce, HubSpot, and common solar CRMs
```

### 6. Success Metrics
**How will we know it's working?**

**Questions**:
- How do we measure success?
- What numbers matter?
- What would "winning" look like?

**Example**:
```markdown
## Success Metrics
- **Speed**: Reduce quote time from 2-3 hours to <15 minutes
- **Adoption**: 100 active installers within 6 months
- **Accuracy**: 95%+ quote accuracy (vs manual calculations)
- **Business Impact**: Users report 25%+ increase in closed deals
- **Retention**: 80%+ monthly active user retention
```

## Implementation Details

### Gap Detection

**Empty Section**: No content or just whitespace
**Weak Section**: Less than 50 characters or very vague

**Analysis Example**:
```python
# Pseudo-code for gap analysis
sections = parse_brief(file)

for section in sections:
    if len(section.content) == 0:
        status = "empty"
    elif len(section.content) < 50:
        status = "weak"
    elif is_vague(section.content):  # AI judgment
        status = "needs_detail"
    else:
        status = "complete"
```

### Conversational Flow

**Start Each Section**:
```
{Section Name}

[Ask primary question]
> [User answer]

[Ask clarifying question based on answer]
> [User answer]

[Optional: Ask deepening question]
> [User answer]

[Generate section content]
Proposed content:
---
{generated content}
---

Does this capture it? (yes/edit/skip)
```

**User Responses**:
- **yes**: Update section, move to next
- **edit**: Ask what to change, regenerate
- **skip**: Leave section as-is, move to next

### Agent Invocation

**Invoke brief-strategist agent**:
```
Task: "Complete project brief through gap-driven conversation.

1. Read docs/project-brief.md
2. Identify gaps (empty/weak/vague sections)
3. For each incomplete section:
   - Ask open question about section topic
   - Listen to user answer
   - Ask clarifying question to deepen understanding
   - Generate section content from conversation
   - Show proposed content to user
   - Get confirmation (yes/edit/skip)
   - Update file if yes
4. After each section, ask if user wants to continue
5. Provide progress summary when done

Section order: Problem, Solution, Target Audience, Key Features, Success Metrics

Be conversational, not formal. Back-and-forth dialogue, not interrogation."
```

## Integration with Workflow

**Position**: Foundation for feature planning

```
/toolkit-init (creates brief with Overview, empty sections)
    ↓
/project-brief (fill gaps through conversation)
    ↓
/epic (create features aligned with brief)
    ↓
/plan TASK-### (implement features)
```

## Examples

### Example 1: First Run After Init

```bash
/project-brief

Reading your brief...

Current state:
✓ Overview: "Helps solar installers create quotes faster"
✗ Problem (empty)
✗ Solution (empty)
✗ Target Audience (empty)
✗ Key Features (empty)
✗ Success Metrics (empty)

Let's fill in the gaps. Starting with Problem.

What problem does SolarQuote solve?
> Solar installers waste 2-3 hours per quote on manual work

What's the impact of that on their business?
> They lose deals to faster competitors and can't scale

✓ Updated Problem section:
---
Solar installers spend 2-3 hours per quote doing manual calculations,
looking up component prices, and creating proposals. This slow process
causes them to lose deals to faster competitors and limits their ability
to scale their business.
---

Continue to Solution? (yes/no)
> yes

How does SolarQuote solve this problem?
> [Continues conversation...]
```

### Example 2: Resuming After Break

```bash
/project-brief

Reading your brief...

Current state:
✓ Overview (complete)
✓ Problem (complete)
✓ Solution (complete)
✗ Target Audience (empty)
✗ Key Features (empty)
✗ Success Metrics (empty)

Good progress! Let's continue with Target Audience.

Who are your primary users?
> [Conversation continues from where you left off...]
```

### Example 3: Review Mode

```bash
/project-brief --review

Analyzing your brief...

Completeness:
✓ Overview - Clear and concise
✓ Problem - Well-defined with impact
⚠ Solution - Present but lacks specifics on "how"
✓ Target Audience - Specific user personas
⚠ Key Features - Only 2 features listed, could use 3-5
✗ Success Metrics - Empty

Recommendations:
1. Solution: Add specific approach or methodology
2. Key Features: List 3 more core capabilities
3. Success Metrics: Define 2-3 measurable indicators

Run /project-brief to address these gaps.
```

## Design Decisions

### Why Gap-Driven Instead of Comprehensive Upfront?

**Traditional approach**: Ask all questions upfront before writing
**Gap-driven**: Fill what's missing, ignore what's complete

**Benefits**:
- Faster for users who partially know their vision
- Can stop/resume anytime
- Focuses effort where needed
- Respects existing content

### Why No Tech Stack in Brief?

**Product vision** (brief): What you're building and why
**Project context** (CLAUDE.md): How you're building it

**Benefit**: Can share brief with non-technical stakeholders without implementation details cluttering the message

### Why 6 Specific Sections?

**Standard framework** that captures:
- The problem (why)
- The solution (what)
- The users (who)
- The capabilities (how)
- The validation (success)

**Provides structure** without being prescriptive about format

## Related Commands

- **`/toolkit-init`**: Creates initial brief with Overview filled, other sections empty
- **`/epic`**: Create feature epics after brief is clear
- **`/adr`**: Make technical decisions aligned with brief

## Tools

- **Read**: Load existing brief
- **Write**: Create brief if doesn't exist
- **Edit**: Update specific sections
- **Task**: Invoke brief-strategist agent for conversational Q&A
- **TodoWrite**: Track progress through sections

---

**Next Steps**: After completing brief, run `/epic` to start creating features aligned with your vision.
