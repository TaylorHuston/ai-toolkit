---
last_updated: "2025-11-20"
description: "Spike template for time-boxed technical explorations to answer feasibility and approach questions"

# === Template Configuration ===
type: spike
sections:
  - name: Questions to Answer
    prompt: "What questions need to be answered through this spike?"
    required: true
    format: numbered-list
    hint: "List specific questions this exploration will answer (feasibility, performance comparison, approach selection, etc.)"
  - name: Time Box
    prompt: "What is the maximum time allowed for this spike?"
    required: true
    format: text
    hint: "Typical spikes: 4-8 hours. Must end even if questions not fully answered. This prevents analysis paralysis."
  - name: Deadline
    prompt: "When must this spike be completed?"
    required: false
    format: date
    hint: "ISO format (YYYY-MM-DD) or relative (e.g., 'end of week')"
  - name: Approaches to Explore
    prompt: "How many approaches will you explore? What are they?"
    required: true
    format: numbered-list
    hint: "Each approach gets its own PLAN-N.md and WORKLOG-N.md. Typically 2-3 approaches maximum."
  - name: Success Criteria
    prompt: "How will you know the spike is successful?"
    required: true
    format: checklist
    hint: "What deliverables or decisions indicate completion? Focus on questions answered, not features built."
  - name: Related Spec
    prompt: "Is this spike related to an existing spec?"
    required: false
    format: text
    hint: "SPEC-### ID if this spike informs a feature spec"
---

# {id}: {title}

## Questions to Answer

{questions}

## Time Box

**Maximum time**: {time_box}
**Deadline**: {deadline}
**Actual time**: 0 hours

## Approaches to Explore

{approaches}

## Success Criteria

{success_criteria}

## Related Work

{related_work}

---

**Exploration Plans**: Run `/plan {id}` to create exploration plans for each approach.
**See**: `docs/development/workflows/spike-workflow.md` for complete spike methodology.
