---
# === Metadata ===
template_type: "guideline"
created: "2025-11-05"
last_updated: "2025-11-18"
status: "Active"
target_audience: ["AI Assistants", "Developers"]
description: "WORKLOG entry format definitions for standard workflow, troubleshooting, and investigation contexts"

# === Configuration ===
worklog_ordering: "reverse_chronological"    # Newest entries at TOP
entry_philosophy: "stream_not_summarize"     # Write at handoffs, not after completion
entry_length: "500_chars_ideal"              # ~5-10 lines per entry
---

# WORKLOG Format Guidelines

**Purpose**: Document work flow between agents and across workflows with brief, scannable entries

**Philosophy**: Stream, don't summarize. Write entries as work happens (cross-agent handoffs), not retrospective summaries after phases complete.

**Entry Ordering**: **CRITICAL** - Always maintain **reverse chronological order** (newest entries at the TOP).

## Quick Reference

**Three Format Types**:
1. **Standard Format** - For implementation work, agent handoffs, phase completion
2. **Troubleshooting Format** - For debug loops with hypothesis tracking
3. **Investigation Format** - For research findings, external knowledge gathering

**When to write**:
- ✅ Completing work and handing off to another agent
- ✅ Receiving work back with changes needed
- ✅ Completing a phase or subtask
- ✅ **MANDATORY: After every code review** (code-reviewer writes separate entry)
- ✅ **MANDATORY: After every security review** (security-auditor writes separate entry)
- ✅ Each troubleshooting loop iteration
- ✅ Completing external research (context-analyzer)
- ❌ Don't write "STARTED" entries (waste - just do the work)

**CRITICAL - Review Agent Entries**:
- `code-reviewer` MUST write its own WORKLOG entry after reviewing each phase
- `security-auditor` MUST write its own WORKLOG entry after security reviews
- Implementation agents should NOT write review results in their entries - reviewers document their own findings
- Review entries provide detailed feedback, scores, and context for future work

**Key principle**: Newest entries at TOP, brief (~500 chars), focus on insights

---

## Format Selection Decision Tree

**What are you documenting?**

```
├─ Completed work being passed to another agent
│  └─ Use: Standard HANDOFF entry
│
├─ Completed phase/task (no more handoffs)
│  └─ Use: Standard COMPLETE entry
│
├─ Code review results
│  ├─ Approved (score >= 90)? → Use: Standard REVIEW APPROVED entry
│  └─ Issues found (score < 90)? → Use: Standard REVIEW REQUIRES CHANGES entry
│
├─ Security review results
│  ├─ No vulnerabilities? → Use: Standard REVIEW APPROVED entry
│  └─ Vulnerabilities found? → Use: Standard REVIEW REQUIRES CHANGES entry
│
├─ Review cycle resulted in plan changes
│  └─ Use: Standard PLAN CHANGES entry
│
├─ Debugging/troubleshooting work
│  ├─ Hypothesis succeeded? → Use: Troubleshooting Loop N - Success entry
│  └─ Hypothesis failed? → Use: Troubleshooting Loop N - Failed entry
│
└─ External research findings
   ├─ Found solutions? → Use: Investigation Complete entry
   └─ No clear solution? → Use: Investigation Incomplete entry
```

**See worklog-examples.md for concrete examples of each entry type.**

---

## Phase Commits Summary Section

**Purpose**: Provide quick rollback map for each completed phase

**Location**: At the **top** of WORKLOG.md (above all entries)

**Format**:
```markdown
## Phase Commits

- Phase 1.1: `abc123d` - Database schema setup
- Phase 1.2: `def456e` - JWT authentication implementation
- Phase 2.1: `ghi789j` - Frontend login form
- Phase 2.2: `klm012n` - Integration tests

---
```

**Workflow (MANDATORY after every phase commit)**:
1. Complete phase → Write WORKLOG entry → Commit phase changes
2. Get commit ID: `git rev-parse --short HEAD`
3. Update "Phase Commits" section in WORKLOG.md: `- Phase X.Y: \`commit-id\` - Brief description`
4. Commit the reference: `git add WORKLOG.md && git commit --amend --no-edit` (amend) or make separate docs commit

**Why mandatory**: Provides instant rollback map for each phase - critical for debugging and reverting specific changes

**Benefit**: Know exactly which commit to revert if a phase needs rollback

---

## Standard WORKLOG Format

**Use for**: Implementation work (`/implement`), agent handoffs, phase completion, general development, code reviews, security reviews

### Entry Types

#### HANDOFF Entry (passing work to another agent)

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] → [NEXT: next-agent]

Brief summary of what was done (5-10 lines max).

Gotcha: [critical issues encountered, if any]
Lesson: [key insights, if any]
Files: [key/files/changed.js]

→ Passing to {next-agent} for {reason}
```

#### COMPLETE Entry (phase fully done, no more handoffs)

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] (Phase X.Y COMPLETE)

Phase complete summary (5-10 lines).

Status:
- ✅ Tests passing
- ✅ Quality gates met
- ✅ PLAN.md updated

Files: [key/files/changed.js]
```

#### REVIEW APPROVED Entry (code/security review passed)

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: code-reviewer|security-auditor] (Review Approved)

Reviewed: [Phase/feature/files reviewed]
Scope: [Quality/Security/Performance - which aspects reviewed]
Verdict: ✅ Approved [clean / with minor notes]

Strengths:
- [Key positive aspect 1]
- [Key positive aspect 2]

[Optional] Notes:
- [Minor suggestion or observation]

Files: [files reviewed]
```

#### REVIEW REQUIRES CHANGES Entry (issues found, passing back)

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: code-reviewer|security-auditor] → [NEXT: implementation-agent]

Reviewed: [Phase/feature/files reviewed]
Scope: [Quality/Security/Performance]
Verdict: ⚠️ Requires Changes

Critical:
- [Issue description] @ file.ts:line - [Fix needed]

[Optional] Major:
- [Issue description] @ file.ts:line - [Fix needed]

[Optional] Minor:
- [Issue description] - [Suggestion]

Files: [files reviewed]

→ Passing back to {agent-name} for fixes
```

#### PLAN CHANGES Entry (documenting adaptations after reviews)

```markdown
## YYYY-MM-DD HH:MM - Review Cycle: Plan Updated

[Review type] completed on [Phase X] implementation.

**Key Findings**:
- [Finding 1 that requires action]
- [Finding 2 that requires action]
- [Finding 3 if applicable]

**Decisions**:
- [What changed in TASK.md and why]
- [What changed in PLAN.md and why]
- [What was deferred/descoped and why]

**Files Updated**: TASK.md, PLAN.md
**Full report**: [link if needed]
```

### Required Elements

- **Timestamp**: Always run `date '+%Y-%m-%d %H:%M'` - never estimate
- **Agent identifier**: Name of the agent (or @username for humans via `/worklog`)
- **Arrow notation**: Use `→` for handoffs to show work flow
- **Brief summary**: What YOU did (not entire phase history) - keep scannable
- **Gotchas/Lessons**: Only if significant (don't force it)
- **Files**: Key files modified (helps locate changes via diff)
- **Handoff note**: Who receives work and why (for handoffs only)

---

## Troubleshooting WORKLOG Format

**Use for**: Debug loops (`/troubleshoot`), hypothesis testing, issue investigation

**Key difference**: Structured format tracks hypothesis → findings → result

### Troubleshooting Entry Template

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] (Troubleshooting Loop N)

Hypothesis: [Theory based on research - brief]
Debug findings: [Key insights from debug logs]
Implementation: [What was changed - files and approach]
Research: [Docs/examples referenced]
Result: ✅ Fixed / ❌ Not fixed - [evidence]

Files: src/file.ts (added debug statements)
Tests: X/X passing
Manual verification: [User confirmed / Awaiting confirmation]

→ [If not fixed: Next research direction]
```

### Troubleshooting Format Variants

#### If Hypothesis FAILED

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] (Loop N - Failed)

Hypothesis: [Theory that didn't work]
Debug findings: [What logs revealed that disproved hypothesis]
Implementation: [What was tried]
Result: ❌ Not fixed - [why it failed, what was learned]
Rollback: ✅ Changes reverted

Files: (all changes reverted)
Next: [Alternative approach based on debug findings]
```

#### If Hypothesis SUCCEEDED

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] (Loop N - Success)

Hypothesis: [Theory that worked]
Debug findings: [Key logs that confirmed fix]
Implementation: [Final changes made]
Research: [Docs that led to solution]
Result: ✅ Fixed - [test results and user confirmation]

Files: src/component.ts, tests/component.test.ts
Tests: 245/245 passing
Manual verification: User confirmed working
Debug cleanup: [Kept as comments / Removed / Converted to logger]
```

---

## Investigation Results WORKLOG Format

**Use for**: External research (`context-analyzer`), documentation lookup, resource discovery, knowledge gathering

**Key difference**: Focuses on research findings and resource curation, not code changes or hypothesis testing

### Investigation Entry Template

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: context-analyzer] (Investigation Complete)

Query: [What was being researched]
Sources: [Number] resources analyzed ([docs/blogs/SO/GitHub breakdown])
Key findings: [2-3 sentence summary of discoveries]

Top solutions:
1. [Solution name] - [Brief description and use case]
2. [Solution name] - [Brief description and use case]
3. [Solution name] - [Brief description and use case]

Curated resources:
- [Resource title] - [url] - [Why valuable]
- [Resource title] - [url] - [Why valuable]

💡 Suggested for CLAUDE.md:
- [Resource] → [Category] - [Why add to project resources]

→ Passing findings to {agent-name} for implementation
```

### Investigation Format Variants

#### When No Clear Solution Found

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: context-analyzer] (Investigation Incomplete)

Query: [What was being researched]
Sources: [Number] resources analyzed
Key findings: No definitive solution found. [What was learned]

Partial findings:
- [Finding 1 with uncertainty noted]
- [Finding 2 with caveats]

Resources checked:
- Official docs - [What was missing]
- Community sources - [What was found but not conclusive]

Recommendation: [Suggest alternative approach, ask domain expert, try different search terms]

→ Passing to {agent-name} with findings (no implementation recommended yet)
```

### Required Elements (Investigation Format)

- **Timestamp**: Always run `date '+%Y-%m-%d %H:%M'` - never estimate
- **Query**: What was being researched (the request from calling agent)
- **Sources**: Count and breakdown (docs/blogs/SO/GitHub)
- **Key findings**: High-level summary (2-3 sentences max)
- **Top solutions**: Ranked approaches with use cases
- **Curated resources**: Best resources found (2-5 links with context)
- **Suggested resources**: Exceptional finds worth adding to CLAUDE.md (0-3 max)
- **Handoff note**: Which agent receives findings and for what purpose

---

## WORKLOG Best Practices

**Apply to all formats (standard, troubleshooting, investigation, reviews)**:

1. **Keep entries scannable**: ~500 chars is ideal, can be longer for critical gotchas
2. **Focus on insights**: Document WHY things were done certain ways, not just WHAT
3. **Capture alternatives**: "Tried X but Y worked better because..." helps future work
4. **Reference decisions**: For architecture decisions, use `/adr` command to create ADR
5. **Write for the future**: Developers reading weeks/months later need context
6. **Newest first**: Always add new entries at the TOP of WORKLOG.md (reverse chronological)
7. **Be honest about failures**: Failed attempts are valuable documentation
8. **Review specificity**: For review entries, always include file:line references for issues

### When to Mix Formats

**Use MULTIPLE formats in same WORKLOG** when work involves research + troubleshooting + implementation.

See worklog-examples.md for multi-format workflow examples.

---

## Integration with Commands

### Commands Using Standard Format

- **`/implement`**: HANDOFF and COMPLETE entries for phase work
- **`/worklog`**: Manual entries by humans with @username
- **All agents**: Handoff entries during multi-agent workflows
- **`/quality`**: Review entries with quality scores
- **`code-reviewer`**: REVIEW APPROVED and REVIEW REQUIRES CHANGES entries
- **`security-auditor`**: REVIEW APPROVED and REVIEW REQUIRES CHANGES entries with OWASP classifications

### Commands Using Troubleshooting Format

- **`/troubleshoot`**: Structured hypothesis/result entries
- **During `/implement`**: When hitting issues requiring debug loop

### Commands Using Investigation Format

- **`context-analyzer`**: Research findings and resource curation entries
- **During `/troubleshoot`**: Research phase before hypothesis formation
- **Implementation agents**: When requesting external knowledge

### Commands Reading WORKLOG

- **`/implement --next`**: Reads WORKLOG to understand previous work
- **`/sanity-check`**: Reviews WORKLOG for drift detection
- **`/plan`**: May reference WORKLOG for context
- **All agents**: Read WORKLOG before continuing work

---

## Related Documentation

**For concrete examples**: See `worklog-examples.md` for all entry type examples

**For troubleshooting methodology**: See `troubleshooting.md` for complete 5-step debug loop and debug logging best practices

**For workflow context**: See `development-loop.md` for agent handoff patterns and quality gates

**For file structure**: See `pm-workflows.md` for TASK.md, BUG.md, and PLAN.md formats

**For complex decisions**: Use `/adr` command to create Architecture Decision Records for significant technical decisions
