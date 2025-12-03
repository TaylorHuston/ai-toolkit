---
tags: ["workflow", "research", "documentation"]
description: "Deep research on a topic, creating persistent documentation for future reference"
argument-hint: "\"topic to research\""
allowed-tools: ["Read", "Write", "Edit", "Grep", "Glob", "TodoWrite", "Task", "WebSearch", "WebFetch", "mcp__plugin_ai-toolkit_context7__resolve-library-id", "mcp__plugin_ai-toolkit_context7__get-library-docs"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/templates/research-template.md  # Research document structure
  - docs/development/workflows/worklog-format.md     # Research WORKLOG entry format
---

# /research Command

**WHAT**: Deep research on a topic, creating a persistent document for future reference.

**HOW**: Invokes research-specialist to read 30+ sources, distill to essentials, and save reusable documentation.

**WHY**: So agents don't re-research the same topic every time. "Learn once, reference forever."

## Usage

```bash
/research "Convex CLI capabilities"
/research "React Server Components patterns"
/research "rate limiting strategies for APIs"
/research "Prisma vs Drizzle comparison"
```

## Process

### 1. Check Existing Research

First, check if research already exists:

```bash
# Look for existing docs
ls docs/project/research/
grep -l "convex" docs/project/research/*.md
```

If found, ask user: "Research on [topic] already exists at [path]. Update it or create new?"

### 2. Deep Research Phase

Invoke `research-specialist` agent with comprehensive research mandate:

**Research scope**:
- Official documentation (Context7)
- Technical blogs and tutorials
- Stack Overflow (high-vote answers)
- GitHub issues and discussions
- Community forums and Discord
- Conference talks and videos

**Volume**: Read 30+ sources, distill to 5-10 pages

### 3. Create Research Document

Save to: `docs/project/research/{topic-slug}.md`

**Naming convention**:
- Lowercase kebab-case
- Descriptive but concise
- Examples: `convex-cli-capabilities.md`, `nextjs-app-router-patterns.md`

**Structure**: Follow `docs/development/templates/research-template.md`

### 4. WORKLOG Entry

Create Research entry in current task's WORKLOG (if active) or note in output.

## Output Location

```
docs/project/research/
├── convex-cli-capabilities.md
├── react-server-components.md
├── prisma-vs-drizzle.md
├── rate-limiting-strategies.md
└── oauth2-implementation-patterns.md
```

## Research Document Structure

See `docs/development/templates/research-template.md` for full template.

Key sections:
- **Overview**: What this research covers
- **Key Findings**: Executive summary (2-3 paragraphs)
- **Detailed Findings**: Organized by subtopic
- **Must-Read Resources**: Top 3-5 curated links with explanations
- **Additional Resources**: More links for deeper dives
- **Gotchas & Warnings**: Common mistakes and pitfalls
- **Code Examples**: Working snippets from research
- **Decision Matrix**: (if comparing options) Pros/cons table

## Example Interaction

```
User: /research "Convex CLI capabilities"

AI: Checking for existing research...
    No existing research found for "Convex CLI"

    Starting deep research on Convex CLI capabilities...

    [research-specialist agent invoked]

    Sources analyzed: 34 resources
    - Official docs: 12
    - GitHub: 8
    - Community posts: 14

    Creating research document...

    ✓ Created: docs/project/research/convex-cli-capabilities.md

    ## Summary

    The Convex CLI provides commands for:
    - Project initialization (`npx convex dev`)
    - Deployment (`npx convex deploy`)
    - Function management (`npx convex run`)
    - Data operations (`npx convex import/export`)
    - Environment management (`npx convex env`)

    ### Must-Read Resources
    1. Official CLI Reference - https://docs.convex.dev/cli
    2. Deployment Guide - https://docs.convex.dev/production
    3. [Community post on advanced workflows]

    Full details saved to docs/project/research/convex-cli-capabilities.md
```

## When to Use

**Good candidates for /research**:
- ✅ Technology your project will use repeatedly (Convex, Prisma, etc.)
- ✅ Complex topics with many options (auth strategies, caching patterns)
- ✅ Tools with extensive CLI/API surface area
- ✅ Comparisons between alternatives (Prisma vs Drizzle)
- ✅ Best practices for patterns you'll implement multiple times

**Not needed for**:
- ❌ One-off questions (inline research during /implement is fine)
- ❌ Simple API lookups (just use Context7)
- ❌ Project-specific debugging (use /troubleshoot)

## Integration

Research documents are automatically discovered by:
- `/plan` - Checks research docs before planning
- `/implement` - References research during implementation
- `/troubleshoot` - Looks for relevant research first
- `research-specialist` - Checks existing research before new research

```
/research "topic" → docs/project/research/topic.md
                           ↓
              Future /plan, /implement, /troubleshoot
              automatically reference this document
```

## Updating Existing Research

If research exists but is outdated:

```bash
/research "Convex CLI capabilities"

AI: Research exists: docs/project/research/convex-cli-capabilities.md
    Last updated: 2025-01-15

    Options:
    1. Update existing (add new findings, keep structure)
    2. Replace entirely (fresh research)
    3. Cancel

User: 1

AI: Updating existing research with new findings...
```
