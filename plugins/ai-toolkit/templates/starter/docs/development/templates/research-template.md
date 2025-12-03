---
last_updated: "2025-12-03"
description: "Template for persistent research documents created by /research command"

# === Template Configuration ===
type: research
sections:
  - name: Overview
    prompt: "What topic does this research cover? What questions does it answer?"
    required: true
    format: paragraph
    hint: "Brief description of scope and purpose. What will readers learn?"
  - name: Key Findings
    prompt: "Executive summary - what are the most important takeaways?"
    required: true
    format: paragraph
    hint: "2-3 paragraphs summarizing the essential findings. Reader should understand the core insights without reading the full document."
  - name: Detailed Findings
    prompt: "Organized findings by subtopic"
    required: true
    format: structured
    hint: "Break down by logical categories. Include code examples where relevant."
  - name: Must-Read Resources
    prompt: "Top 3-5 curated resources that matter most"
    required: true
    format: list
    hint: "Each resource needs: title, URL, why it's valuable, key takeaway"
  - name: Additional Resources
    prompt: "More resources for deeper dives"
    required: false
    format: list
    hint: "Secondary resources, reference docs, related topics"
  - name: Gotchas & Warnings
    prompt: "Common mistakes, pitfalls, and things to watch out for"
    required: true
    format: list
    hint: "What do people get wrong? What's not obvious?"
  - name: Code Examples
    prompt: "Working code snippets from research"
    required: false
    format: code
    hint: "Practical examples that demonstrate key concepts"
---

# Research: {topic}

> **Last Updated**: {date}
> **Sources Analyzed**: {count} resources
> **Created By**: research-specialist via `/research`

## Overview

{overview}

## Key Findings

{key_findings}

---

## Detailed Findings

### {subtopic_1}

{detailed_content}

### {subtopic_2}

{detailed_content}

### {subtopic_3}

{detailed_content}

---

## Must-Read Resources

These are the resources that actually matter. Read these first.

### 1. {resource_title}

**URL**: {url}
**Why it matters**: {why_valuable}
**Key takeaway**: {key_point}

### 2. {resource_title}

**URL**: {url}
**Why it matters**: {why_valuable}
**Key takeaway**: {key_point}

### 3. {resource_title}

**URL**: {url}
**Why it matters**: {why_valuable}
**Key takeaway**: {key_point}

---

## Additional Resources

For deeper exploration:

- **{title}** - {url} - {brief_description}
- **{title}** - {url} - {brief_description}
- **{title}** - {url} - {brief_description}

---

## Gotchas & Warnings

Common mistakes and non-obvious issues:

1. **{gotcha_title}**: {description}
   - *How it manifests*: {symptoms}
   - *Solution*: {fix}

2. **{gotcha_title}**: {description}
   - *How it manifests*: {symptoms}
   - *Solution*: {fix}

---

## Code Examples

### {example_title}

```{language}
{code}
```

**What this demonstrates**: {explanation}

### {example_title}

```{language}
{code}
```

**What this demonstrates**: {explanation}

---

## Decision Matrix

*(Include if research compares multiple options)*

| Aspect | Option A | Option B | Option C |
|--------|----------|----------|----------|
| {criteria_1} | {value} | {value} | {value} |
| {criteria_2} | {value} | {value} | {value} |
| {criteria_3} | {value} | {value} | {value} |
| **Best for** | {use_case} | {use_case} | {use_case} |

---

## Summary

{final_summary_and_recommendation}

---

**Research conducted**: {date}
**Sources**: {breakdown_by_type}
**Next steps**: {suggested_actions}
