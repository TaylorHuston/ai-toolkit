---
last_updated: "2025-10-30"
description: "Bug report template with reproduction steps and environment details"

# === Template Configuration ===
type: bug
sections:
  - name: Description
    prompt: "What is the bug? Provide a clear summary of the issue."
    required: true
    format: paragraph
    hint: "Brief overview of the problem and its impact."
  - name: Reproduction Steps
    prompt: "How can this bug be reproduced?"
    required: true
    format: numbered-list
    hint: "Step-by-step instructions to reproduce the issue consistently."
  - name: Expected Behavior
    prompt: "What should happen?"
    required: true
    format: paragraph
    hint: "Describe the correct/expected behavior."
  - name: Actual Behavior
    prompt: "What actually happens?"
    required: true
    format: paragraph
    hint: "Describe the observed incorrect behavior."
  - name: Environment
    prompt: "What environment is this occurring in?"
    required: false
    format: list
    hint: "OS, browser, version, configuration, etc."
---

# BUG-{id}: {name}

## Description
{description}

## Reproduction Steps
{reproduction_steps}

## Expected Behavior
{expected_behavior}

## Actual Behavior
{actual_behavior}

## Environment
{environment}

**Note**: See pm-guide.md "Agile Plan Updates" if bug scope evolves during investigation.

---

**Fix Plan**: Run `/plan BUG-{id}` to create PLAN.md with root cause analysis and fix approach.
