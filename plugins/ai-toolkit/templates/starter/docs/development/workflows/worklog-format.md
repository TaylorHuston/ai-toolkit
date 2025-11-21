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
entry_philosophy: "context_rich_handoffs"    # Provide all context next agent needs
entry_length: "information_complete"         # 10-20 lines average, focus on findings/gotchas
---

# WORKLOG Format Guidelines

**Purpose**: Provide complete context for the next agent to continue work effectively

**Philosophy**: Context-rich handoffs. Each entry should contain ALL information the next agent needs - nothing more, nothing less. Focus heavily on key findings, gotchas, and lessons learned.

**Entry Length**: Average 10-20 lines per entry. Brief entries miss critical context; overly long entries become noise. The test: "Can the next agent continue without re-reading code or re-discovering issues?"

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

**Key principle**: Newest entries at TOP, information-complete (10-20 lines), emphasize findings and gotchas

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

**Completed**: [What was accomplished in this phase - 2-3 lines]

**Implementation approach**:
- [Key decision 1 and rationale]
- [Key decision 2 and rationale]
- [Pattern/library used and why]

**Key findings**:
- [Discovery 1 that affects future work]
- [Discovery 2 that changes understanding]
- [Unexpected behavior observed]

**Gotchas encountered**:
- [Issue 1]: [How it manifested] → [Solution/workaround]
- [Issue 2]: [Root cause discovered] → [Fix applied]

**Testing status**:
- [Test results summary]
- [Edge cases validated]
- [Known limitations]

**Files modified**: [key/files/changed.js, other/important.ts]

**Next agent context**: [Specific information next-agent needs to continue]

→ Passing to {next-agent} for {specific-reason}
```

#### COMPLETE Entry (phase fully done, no more handoffs)

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] (Phase X.Y COMPLETE)

**Phase objective**: [What this phase was meant to accomplish]

**Implementation summary**:
- [Approach taken and why]
- [Key architectural decisions]
- [Libraries/patterns used]

**Key findings**:
- [Discovery 1 that affects future phases]
- [Discovery 2 about system behavior]
- [Performance/security insights]

**Gotchas for future phases**:
- [Issue 1]: [Context] → [How we solved it / How to avoid]
- [Issue 2]: [Root cause] → [Lesson learned]

**Test coverage**:
- Unit: [X/Y tests, coverage %]
- Integration: [test scenarios covered]
- Edge cases validated: [list]

**Quality gates**:
- ✅ Tests passing (all X tests green)
- ✅ Code review score: [score/100]
- ✅ Coverage target met: [%]
- ✅ PLAN.md checkboxes updated

**Files modified**: [key/files/changed.js, tests/added.test.ts]

**Notes for future work**: [Anything next phases should know]
```

#### REVIEW APPROVED Entry (code/security review passed)

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: code-reviewer|security-auditor] (Review Approved)

**Reviewed**: [Phase X.Y / Feature name / Specific implementation]
**Scope**: [Quality/Security/Performance/Architecture - what was examined]
**Score**: [92/100] ✅ Approved
**Verdict**: Clean approval [or: Approved with minor notes]

**Strengths observed**:
- [Positive pattern 1 - why it's good]
- [Positive pattern 2 - impact on codebase]
- [Well-handled edge case or design decision]

**Code quality highlights**:
- [Test coverage aspect - specific number]
- [Error handling approach - why effective]
- [Performance consideration - measurement]

**Minor observations** (non-blocking):
- [Suggestion 1 for future consideration]
- [Pattern that could be improved later]
- [Documentation enhancement opportunity]

**Files reviewed**: [src/file1.ts, src/file2.ts, tests/file.test.ts]

**Recommendation**: Approved for merge. [Additional context if relevant]
```

#### REVIEW REQUIRES CHANGES Entry (issues found, passing back)

```markdown
## YYYY-MM-DD HH:MM - [AUTHOR: code-reviewer|security-auditor] → [NEXT: implementation-agent]

**Reviewed**: [Phase X.Y / Feature name / Specific implementation]
**Scope**: [Quality/Security/Performance/Architecture]
**Score**: [78/100] ⚠️ Requires Changes
**Verdict**: Issues must be addressed before merge

**Critical issues** (blocking):
1. [Issue description and impact] @ file.ts:123
   - **Problem**: [What's wrong and why it's critical]
   - **Fix needed**: [Specific action required]
   - **Context**: [Why this matters / What breaks without fix]

2. [Security/correctness issue] @ file.ts:456
   - **Problem**: [Vulnerability or bug description]
   - **Fix needed**: [Remediation approach]
   - **Reference**: [OWASP category / pattern to use]

**Major issues** (should fix):
- [Issue with significant impact] @ file.ts:789
  - **Problem**: [What's suboptimal]
  - **Recommendation**: [Better approach]
  - **Benefit**: [Why this improves the code]

**Minor suggestions** (optional):
- [Enhancement opportunity]
- [Documentation clarification]

**What works well**:
- [Positive aspect to preserve]
- [Good pattern used]

**Files reviewed**: [src/file1.ts (5 issues), src/file2.ts (2 issues)]

**Estimated fix time**: [15 minutes / 1 hour / etc.]

→ Passing back to {agent-name} for fixes. Focus on critical issues first.
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
- **Completed/Objective**: What was accomplished or reviewed (context setter)
- **Implementation approach**: Key decisions and rationale (helps next agent understand choices)
- **Key findings**: Discoveries that affect future work (critical context)
- **Gotchas encountered**: Issues hit and solutions found (prevent re-discovery)
- **Testing/Quality status**: Results and coverage (confidence level)
- **Files modified**: Key files changed (helps locate implementation)
- **Next agent context**: Specific info handoff recipient needs (direct handoff)

**Information density test**: Can the next agent continue effectively without:
- Re-reading the code to understand what was done?
- Re-discovering the same issues you encountered?
- Asking clarifying questions about your decisions?

If any answer is "no", add more context to the entry.

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

1. **Information completeness over brevity**: 10-20 lines average. Include all context next agent needs. Brief entries save time writing but cost time during handoffs.

2. **Emphasize findings and gotchas**: These are gold for future work. Spend 40-60% of entry on:
   - What you discovered that wasn't obvious
   - Issues you hit and how you solved them
   - Behavior that surprised you
   - Patterns that worked/didn't work

3. **Document WHY, not just WHAT**:
   - Good: "Used bcrypt with cost 12 (not 10) because auth testing showed 10 was too fast for our security requirements"
   - Bad: "Added password hashing"

4. **Capture attempted alternatives**:
   - "Tried X approach but switched to Y because [specific reason]" prevents re-attempting failed approaches

5. **Quantify when possible**:
   - "Test coverage: 94% (127/135 lines)"
   - "Performance improved 40% (200ms → 120ms)"
   - "Code review score: 92/100"

6. **File:line references for issues**: Always include specific locations for problems
   - "SQL injection risk @ src/auth.ts:45 in login query"
   - "Memory leak @ src/cache.ts:123 when clearing expired entries"

7. **Reference decisions**: For architecture decisions, use `/adr` command to create ADR

8. **Write for the future**: Developers reading weeks/months later need full context

9. **Newest first**: Always add new entries at the TOP of WORKLOG.md (reverse chronological)

10. **Be honest about failures**: Failed attempts with lessons learned are more valuable than success stories without context

11. **Review specificity**: Review entries need extra detail - include reasoning, not just findings

**Quality check**: Before writing entry, ask: "If I read only this entry tomorrow, could I continue the work effectively?" If no, add more detail.

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
