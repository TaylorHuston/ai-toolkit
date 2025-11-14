---
# === Metadata ===
template_type: "guideline"
created: "2025-11-05"
last_updated: "2025-11-06"
status: "Active"
target_audience: ["AI Assistants", "Developers"]
description: "Structured troubleshooting methodology with 5-step loop and debug logging practices"

# === Configuration ===
troubleshooting_approach: "research_first"     # Research → Hypothesize → Implement → Test → Document
hypothesis_limit: 1                            # Test one hypothesis at a time
validation_required: true                      # Never claim "fixed" without proof
rollback_on_failure: true                      # Clean state before next attempt
debug_logging: "liberal"                       # Encourage extensive logging
---

# Troubleshooting Guidelines

**When to use**: Encountering bugs or unexpected behavior during implementation (use `/troubleshoot` command)

**Philosophy**: Research first, test systematically, validate thoroughly. Avoid shotgun debugging.

## Quick Reference

**The Loop**: Research → Hypothesize → Implement → Test → Document

**Key Rules**:
- Research BEFORE guessing (Context7 → Official docs → User guidance)
- ONE hypothesis at a time (no shotgun debugging)
- Liberal debug logging (console.log everywhere)
- NEVER claim "fixed" without proof (tests + user confirmation)
- Rollback on failure (clean state before next try)

## The 5-Step Troubleshooting Loop

### 1. Research (Don't Guess!)

**Priority order**: context-analyzer (recommended) → Context7 → Official documentation → User guidance

**Steps**:

**Option A - Invoke context-analyzer (Recommended)**:
1. Provide specific research query to context-analyzer
   - Example: "PostgreSQL JSONB aggregation timeout solutions"
   - Example: "React component re-renders on every state change - debugging patterns"
2. Receive curated summary with:
   - Official documentation excerpts (Context7)
   - Community solutions (blogs, Stack Overflow, GitHub)
   - Recommended resources with quality filtering
   - Investigation WORKLOG entry documenting findings
3. Read Investigation WORKLOG entry for curated resources
4. Check CLAUDE.md Resources section (context-analyzer checks there first)

**Option B - Manual Research**:
1. Check CLAUDE.md Resources section for project-curated links
2. Check Context7 for library/framework documentation
3. Search official docs for error messages and patterns
4. Ask user for links to relevant documentation or examples
5. Identify existing debug tools (verbose modes, framework debugging features)
6. Look for canonical examples and templates

**Questions to ask user**:
- "Do you have links to relevant docs or examples?"
- "Are there canonical patterns in your codebase I should follow?"
- "Any specific guidance on this issue?"

**Benefits of using context-analyzer**:
- Reads 10-20 sources, returns 2-5 page curated summary
- Research happens in separate context window (keeps your context clean)
- Quality-filtered resources (best solutions highlighted)
- Documented findings in Investigation WORKLOG entry

**Don't**: Try to reinvent the wheel or guess based on "common patterns"

---

### 2. Hypothesize (One Solution at a Time)

**Form ONE hypothesis** based on research findings:
- What's wrong
- Why it's happening
- What will fix it
- Reference docs/examples found in research

**Include debug plan**:
- Where to add logging
- What values to check
- Expected vs actual behavior

**Example**:
```
Hypothesis: Query runs before auth completes (per Convex Auth docs)
Debug plan:
- Log isLoading state in UserProfile component
- Log ctx.auth result in getCurrentUser query
- Log token presence in LocalStorage
Expected: isLoading should be false before query runs
```

**Don't**: Create multiple ranked hypotheses (keep it simple - test one at a time)

---

### 3. Implement (With Liberal Debug Logging)

**Apply the solution** and add debug statements liberally:

**Debug logging guidelines**:
- Log function entry/exit
- Log variable values at decision points
- Log before/after state changes
- Use descriptive prefixes: `[ComponentName]`, `[functionName]`
- Don't be shy - more logging is better than guessing

**Good debug examples**:

```typescript
// Use descriptive prefixes
console.log('[UserProfile] Component mounting');
console.log('[UserProfile] Auth state:', { isLoading, isAuthenticated });
console.log('[getCurrentUser] userId:', userId);

// Log objects with context
console.log('[auth] State:', { isLoading, isAuthenticated, user });

// Log before and after
console.log('[signIn] Before auth:', { email, hasToken: !!token });
await authAction.signIn();
console.log('[signIn] After auth:', {
  success: true,
  tokenInStorage: !!localStorage.getItem('authToken')
});

// Backend logging (shows in dev server console)
console.log('[query:getCurrentUser] Start');
console.log('[query:getCurrentUser] Context:', { hasAuth: !!ctx.auth });
```

**Track what changed** for potential rollback:
- Note all files modified
- Consider creating a git stash before implementing
- Document the changes in your hypothesis

---

### 4. Test & Confirm (Prove It Works)

**Validation checklist** (ALL required):

1. **Run automated tests**
   ```bash
   npm test
   npm run test:e2e  # If applicable
   ```

2. **Check debug logs** for expected behavior
   - Verify state transitions match hypothesis
   - Look for unexpected values/flows
   - Confirm no new errors appeared

3. **Reproduce original issue**
   - Should no longer occur
   - Try edge cases that might still fail

4. **Check for regressions**
   - Run full test suite
   - Manually test related features

**CRITICAL**: Never claim "fixed" without confirmation
- If tests pass but unsure → Ask user to verify
- If manual testing required → Wait for user confirmation
- If intermittent issue → Test multiple times

**Example validation message**:
```
Tests passing (245/245). Debug logs show correct flow:
- [UserProfile] Auth state: { isLoading: false, isAuthenticated: true }
- [getCurrentUser] userId: "j57abc123..."

Can you verify login works in your browser and share any console errors if you see them?
```

---

### 5. Document (WORKLOG Entry)

**Write WORKLOG entry** following troubleshooting format.

**For complete troubleshooting WORKLOG format**, see `worklog-format.md` which documents:
- Troubleshooting entry template (Hypothesis → Debug findings → Implementation → Result)
- Success entry format (Loop N - Success)
- Failure entry format (Loop N - Failed)
- Loop continuation patterns
- Complete examples with all required fields

**Quick Reference:**
- Always add at TOP (reverse chronological order)
- Use format: `## YYYY-MM-DD HH:MM - [AUTHOR: agent-name] (Troubleshooting Loop N)`
- Include: Hypothesis, Debug findings, Implementation, Research, Result
- Track loop number for multi-iteration debugging
- Document rollback status if hypothesis failed

---

## Loop Continuation

### If Issue NOT Fixed

**Rollback changes**:
```bash
git checkout -- [modified files]
# Or restore from stash
git stash pop
```

**Return to Step 1** with new research direction:
- Use debug findings to inform next hypothesis
- Look for alternative explanations
- Consider asking user for additional context
- Review related code areas

**After 3+ failed loops**:
- Pause and ask user for additional context
- Consider pair debugging session
- Review if problem statement is correct
- Check if issue is reproducible

---

### If Issue IS Fixed

**Ask user about debug cleanup**:

**Option A: Keep as comments** (recommended)
```typescript
// Helpful for future debugging
// console.log('[UserProfile] Auth state:', { isLoading, isAuthenticated });
```

**Option B: Remove debug statements** (if cluttering)
```bash
# Review changes
git diff

# Stage only actual fixes, not debug logs
git add -p
```

**Option C: Convert to proper logging** (production-ready)
```typescript
import { logger } from '@/lib/logger';

if (process.env.NODE_ENV === 'development') {
  logger.debug('[UserProfile] Auth state:', { isLoading, isAuthenticated });
}
```

---

## Debug Logging Best Practices

### Prefix Conventions

**Component/function identification**:
```typescript
// GOOD - Clear ownership
console.log('[UserProfile] Rendering');
console.log('[getCurrentUser] Query starting');
console.log('[auth:signIn] Processing');

// BAD - Ambiguous
console.log('rendering');
console.log('starting');
```

### Context-Rich Logging

**Log objects with labels**:
```typescript
// GOOD - Labeled context
console.log('[auth] State:', { isLoading, isAuthenticated, user });
console.log('[query] Params:', { userId, includeProfile: true });

// BAD - Unlabeled values
console.log(isLoading, isAuthenticated, user);
```

### State Transitions

**Log before and after changes**:
```typescript
// GOOD - Track changes
console.log('[user] Before update:', user);
await updateUser({ name: 'New Name' });
console.log('[user] After update:', user);

// BAD - Only final state
await updateUser({ name: 'New Name' });
console.log(user);
```

### Backend Logging

**Server logs** (appear in dev server console):
```typescript
export const getCurrentUser = query({
  handler: async (ctx) => {
    console.log('[query:getCurrentUser] Starting');
    console.log('[query:getCurrentUser] Auth context:', {
      hasAuth: !!ctx.auth,
      sessionId: ctx.auth?.sessionId
    });

    const userId = await getAuthUserId(ctx);
    console.log('[query:getCurrentUser] UserId:', userId);

    if (!userId) {
      console.log('[query:getCurrentUser] No user - returning null');
      return null;
    }

    const user = await ctx.db.get(userId);
    console.log('[query:getCurrentUser] User found:', !!user);

    return user;
  }
});
```

---

## Integration with Development Loop

**Use `/troubleshoot` when**:
- Tests are failing unexpectedly
- Feature works inconsistently
- Error messages are unclear
- "It should work but doesn't"
- After 2+ failed implementation attempts

**During `/implement`**:
```bash
# If you hit an issue during implementation
/troubleshoot  # Uses existing WORKLOG.md, continues same context

# After fixing
# Return to /implement to continue phase work
```

**Standalone bug investigation**:
```bash
/troubleshoot BUG-007  # Creates BUG-007 directory with WORKLOG.md
```

---

## Common Anti-Patterns to Avoid

❌ **Shotgun debugging**: Making multiple changes at once
✅ **Systematic approach**: One hypothesis, test, evaluate

❌ **Guessing**: "Let me try changing this..."
✅ **Research first**: "According to the docs..."

❌ **Claiming success**: "This should fix it!"
✅ **Proving success**: "Tests pass, user confirmed working"

❌ **Accumulating changes**: Keeping failed attempts
✅ **Clean rollback**: Revert before next attempt

❌ **Silent changes**: No logging to verify behavior
✅ **Liberal logging**: Console.log at every decision point

---

## Related Documentation

**WORKLOG Formats**:
- `worklog-format.md` - Complete WORKLOG entry formats (Standard, Troubleshooting, Investigation, Reviews)

**Issue and Plan Management**:
- `pm-guide.md` - TASK.md, BUG.md file formats
- `pm-guide.md` - PLAN.md structure and progress tracking
- Use `/adr` command for architecture decisions requiring detailed rationale

**Development Workflow**:
- `development-loop.md` - Core development workflow and quality gates
- `testing-standards.md` - Test coverage requirements and approach
