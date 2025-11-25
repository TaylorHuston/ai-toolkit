---
tags: ["workflow", "epic", "jira", "project-management", "conversational"]
description: "Create Jira epics through natural language conversation (requires Jira integration)"
argument-hint: "[PROJ-### | --spec SPEC-###]"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Task", "TodoWrite"]
model: claude-opus-4-5
references_guidelines:
  - docs/development/misc/jira-integration.md  # Jira setup, field discovery, epic creation workflow
  - docs/development/workflows/pm-workflows.md  # Core PM workflows and epic structure
---

# /jira-epic Command

**WHAT**: Create/refine Jira epics through conversational interaction.

**WHY**: Natural conversation for creating PM tracking containers in Jira. Epics organize work at the PM tool level.

**HOW**: See jira-integration.md for Jira integration patterns, field discovery, and epic creation workflow.

**REQUIRES**: Jira integration enabled in CLAUDE.md (see jira-integration.md for setup)

## Usage

```bash
/jira-epic                 # Create new Jira epic
/jira-epic PROJ-###        # Refine existing Jira epic
/jira-epic --spec SPEC-### # Create Jira epic from local spec
```

## Execution Flow

**Before you start**: Read jira-integration.md for Jira requirements, field discovery, and conversational creation patterns.

### High-Level Steps

1. **Verify Jira Integration**
   - Check CLAUDE.md jira.enabled = true
   - If disabled: Error with setup instructions

2. **Load Context**
   - If --spec: Read local spec for pre-population
   - If PROJ-###: Fetch existing epic from Jira
   - Load field requirements from cache

3. **Conversational Creation/Refinement**
   - Ask for required fields (summary, description, epic name)
   - Discover custom fields per jira-integration.md
   - Create/update via Atlassian MCP

4. **Optional Issue Suggestions**
   - Suggest initial issues based on epic scope
   - Interactive: Create selected issues
   - Per jira-integration.md task suggestion strategy

5. **Optional Spec Creation**
   - Offer to create local spec from epic
   - Run /spec --jira-epic PROJ-### if accepted

**See jira-integration.md "Epic Creation Workflow" for complete conversational flow details.**

## Local vs Jira

**Local Specs** (`/spec`): Document WHAT to build, version controlled
**Jira Epics** (this command): PM tracking container, network required

See jira-integration.md for semantic mapping and workflow patterns.

## Example

```
User: /jira-epic --spec SPEC-001

AI: Creating Jira epic from SPEC-001: User Authentication
    ✓ Loaded spec content for pre-population

    Summary: User Authentication
    Epic Name: user-authentication
    Customize? (yes/no)

User: no

AI: ✓ Created PROJ-100: User Authentication
    🔗 https://company.atlassian.net/browse/PROJ-100

    Add initial issues? (yes/no)

User: yes

AI: Suggested:
    1. User Registration (Story)
    2. Login Flow (Story)
    3. Database Schema (Task)

    Create which? (1,2,3/all/none)

User: all

AI: ✓ PROJ-101: User Registration
    ✓ PROJ-102: Login Flow
    ✓ PROJ-103: Database Schema

    Next: /jira-import PROJ-101
```

## Workflow Patterns

**Spec-first** (recommended):
```
/spec → /jira-epic --spec SPEC-### → /jira-import PROJ-###
```

**Epic-first**:
```
/jira-epic → /spec --jira-epic PROJ-### → /jira-import PROJ-###
```

**Epic-only** (no local spec):
```
/jira-epic → /jira-import PROJ-###
```

## Agent Coordination

**Primary**: project-manager (conversation, Jira interaction)
**Supporting**: Domain specialists (field suggestions), test-engineer (acceptance criteria)

## Error Handling

- **Jira not enabled**: Show setup instructions from jira-integration.md
- **MCP unavailable**: Check Atlassian MCP configuration
- **Spec not found**: Verify path and spec number
- **Field discovery fails**: Run /refresh-schema

See jira-integration.md for complete error scenarios and solutions.

## Integration

**Creates**: Jira epic (PROJ-###)
**Next step**: `/jira-import PROJ-###` or `/spec --jira-epic PROJ-###`

See jira-integration.md for complete workflow integration.
