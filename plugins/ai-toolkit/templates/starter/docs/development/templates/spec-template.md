---
last_updated: "2025-11-13"
description: "Feature Spec template with users/actors, BDD acceptance scenarios, scope boundaries, and success metrics"

# === Template Configuration ===
type: spec
sections:
  - name: Name
    prompt: "What is the feature spec name? (concise, kebab-case friendly)"
    required: true
    format: text
  - name: Description
    prompt: "Describe the feature. What problem does it solve? What value does it provide?"
    required: true
    format: paragraph
    hint: "Write a clear paragraph explaining the purpose, context, and value of this feature."
  - name: Users & Actors
    prompt: "Who are the primary users/actors for this feature? What are their roles?"
    required: true
    format: list
    hint: "List user types with brief role descriptions. Examples: 'Admin users (system configuration)', 'End users (content creation)', 'Guest users (read-only access)'. This helps agents understand access levels and security requirements."
  - name: Acceptance Scenarios
    prompt: "Describe 2-5 key scenarios that must work when this feature is complete. Use Given-When-Then format for each scenario."
    required: true
    format: scenarios
    hint: "Example:\n**Scenario: User logs in successfully**\n- Given: User has valid credentials\n- When: User submits login form\n- Then: User is redirected to dashboard\n- And: Session cookie is set\n\nFocus on happy paths and critical edge cases. Reference actors from Users & Actors section. These scenarios become test phases."
  - name: Out of Scope
    prompt: "What is explicitly NOT included in this feature? What are the boundaries?"
    required: false
    format: list
    hint: "List features intentionally excluded to prevent scope creep. Examples: 'No SSO integration', 'No email notifications', 'No mobile app support'. Update this as scope decisions evolve. Skip if no explicit exclusions."
  - name: Definition of Done
    prompt: "How will we know this feature is complete? What are the concrete completion criteria?"
    required: true
    format: flexible
    hint: "Can be prose paragraph or bulleted checklist. Make it concrete to prevent scope creep."
  - name: Success Metrics
    prompt: "How will we measure if this feature is successful? What are the measurable targets?"
    required: false
    format: list
    hint: "List concrete, measurable success criteria. Examples: '95% test coverage', '<2s page load time', 'Zero P0 security issues', '90% user satisfaction'. These guide quality gates and testing. Skip if no specific targets."
  - name: Dependencies
    prompt: "What must exist before this feature can be completed?"
    required: false
    format: list
    hint: "Include ADR references (ADR-001), other specs (SPEC-002), external factors (OAuth setup), etc."
  - name: Tasks
    prompt: "What tasks make up this feature?"
    required: true
    format: checklist
    hint: "Bulleted checkbox list with global IDs in creation order: [ ] TASK-001, [ ] BUG-003, etc. SCOPING CRITICAL: Each task should be a deployable change (merged to main as complete unit). Break feature into 2-8 independently deployable tasks, not implementation steps. Example: 'User login with JWT' (deployable), NOT 'Create user model' (too small, not deployable alone)."
---

# SPEC-{id}: {name}

## Description
{description}

## Users & Actors
{users_actors}

## Acceptance Scenarios

{acceptance_scenarios}

## Out of Scope
{out_of_scope}

## Definition of Done
{definition_of_done}

## Success Metrics
{success_metrics}

## Dependencies
{dependencies}

## Tasks
{tasks}
