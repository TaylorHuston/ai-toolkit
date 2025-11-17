# Sync Progress Command

**Purpose**: Automatically sync project state when user makes manual changes - analyzes git diff, updates plan to reflect progress, and documents changes in WORKLOG.

**Use when**: You've made manual code changes and want AI to catch up without manually explaining everything.

---

## Workflow

### 1. Analyze Changes

Run these commands to understand what changed:

```bash
# Check for uncommitted changes
git diff

# Check staged changes
git diff --staged

# Get summary of changed files
git status --short
```

### 2. Read Current State

Read the following files if they exist:
- `pm/active/PLAN.md` - Current task plan
- `pm/active/WORKLOG.md` - Work history
- `pm/active/spec.md` - Feature specification (if applicable)

### 3. Analyze and Update

**For each changed file in git diff**:

1. **Determine intent** - What was the user trying to accomplish?
   - New feature implementation
   - Bug fix
   - Refactoring
   - Configuration change
   - Documentation update

2. **Compare to PLAN.md**:
   - Which phase/task does this relate to?
   - Is this planned work or new direction?
   - Should any phases be marked complete?
   - Are there new tasks to add?

3. **Update PLAN.md**:
   - If there is ANY ambiguity clarify with user
   - Mark completed phases as `[x] COMPLETED`
   - Update phase descriptions if approach changed
   - Add new phases if user took different direction
   - Add notes about implementation decisions made

### 4. Document in WORKLOG

Write a WORKLOG entry with:

```markdown
## YYYY-MM-DD HH:MM - Manual Changes Synced

**Files Changed**: [list of changed files]

**Changes Made**:
- [Brief description of each major change]
- [Why it was done - inferred from code]
- [Any patterns or decisions observed]

**Plan Updates**:
- [Which phases marked complete]
- [Any new phases added]
- [Any direction changes noted]

**Status**: [Current project state after these changes]

**Next Steps**: [Suggested next actions based on plan]
```

### 5. Summary Report

Provide user with:
- **Changes detected**: Count and summary of modified files
- **Plan updates**: Which phases completed, which added
- **WORKLOG entry**: Confirmation of documentation
- **Next suggested action**: Based on remaining plan items

---

## Key Principles

1. **Infer intent from code** - Don't ask user to explain, analyze the diff
2. **Be accurate** - Only mark phases complete if truly done
3. **Preserve context** - Keep plan structure, don't overwrite unnecessarily
4. **Document decisions** - Note any technical choices made in code
5. **Suggest next steps** - Help user know what to work on next

---

## Example Usage

**Scenario**: User manually implemented authentication while AI was offline

**Command**: `/sync-progress`

**AI Actions**:
1. Runs `git diff` - sees new files: `auth/login.ts`, `auth/middleware.ts`, `db/users.sql`
2. Reads `pm/active/PLAN.md` - finds Phase 2: "Implement authentication system"
3. Updates PLAN.md:
   - Marks Phase 2 as `[x] COMPLETED`
   - Adds notes: "Used JWT tokens, bcrypt for passwords, PostgreSQL for users table"
4. Writes WORKLOG entry documenting authentication implementation
5. Reports: "Detected authentication implementation complete. Phase 2 marked done. Next: Phase 3 - API endpoint protection"

---

## Edge Cases

**No PLAN.md exists**:
- Create one based on git changes
- Infer project structure and goals
- Add phases for observed work

**Changes conflict with plan**:
- Note the deviation in WORKLOG
- Ask user if plan should be updated or reverted
- Suggest reconciliation approach

**Changes are exploratory/experimental**:
- Don't mark plan phases complete
- Add note to PLAN.md: "Exploration: [description]"
- Document in WORKLOG as research/spike

**Multiple unrelated changes**:
- Group by functional area
- Update multiple plan phases if applicable
- Create clear sections in WORKLOG entry

---

## Success Criteria

✅ All git changes analyzed and understood
✅ PLAN.md accurately reflects current state
✅ Completed work properly marked
✅ WORKLOG entry is clear and comprehensive
✅ User knows what to do next
