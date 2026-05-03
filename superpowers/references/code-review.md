# Code Review - Detailed Reference

## Requesting Code Review

Dispatch code reviewer subagent to catch issues before they cascade. The reviewer gets precisely crafted context for evaluation — never your session's history.

**Core principle:** Review early, review often.

### When to Request Review

**Mandatory:**
- After each task in subagent-driven development
- After completing major feature
- Before merge to main

**Optional but valuable:**
- When stuck (fresh perspective)
- Before refactoring (baseline check)
- After fixing complex bug

### How to Request

**1. Get git SHAs:**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. Dispatch code reviewer subagent** with:
- WHAT_WAS_IMPLEMENTED - What you just built
- PLAN_OR_REQUIREMENTS - What it should do
- BASE_SHA - Starting commit
- HEAD_SHA - Ending commit
- DESCRIPTION - Brief summary

**3. Act on feedback:**
- Fix Critical issues immediately
- Fix Important issues before proceeding
- Note Minor issues for later
- Push back if reviewer is wrong (with reasoning)

### Code Reviewer Template

```
You are reviewing code changes for production readiness.

## What Was Implemented
{DESCRIPTION}

## Requirements/Plan
{PLAN_REFERENCE}

## Git Range to Review
Base: {BASE_SHA}
Head: {HEAD_SHA}

## Review Checklist
- Code Quality: separation of concerns, error handling, DRY, edge cases
- Architecture: design decisions, scalability, performance, security
- Testing: tests test logic not mocks, edge cases, integration tests, all passing
- Requirements: all plan requirements met, no scope creep
- Production Readiness: backward compatibility, documentation, no obvious bugs

## Output Format
### Strengths
### Issues
#### Critical (Must Fix)
#### Important (Should Fix)
#### Minor (Nice to Have)
### Assessment
**Ready to merge?** [Yes/No/With fixes]
```

## Receiving Code Review

**Core principle:** Verify before implementing. Ask before assuming. Technical correctness over social comfort.

### The Response Pattern

```
WHEN receiving code review feedback:
1. READ: Complete feedback without reacting
2. UNDERSTAND: Restate requirement in own words (or ask)
3. VERIFY: Check against codebase reality
4. EVALUATE: Technically sound for THIS codebase?
5. RESPOND: Technical acknowledgment or reasoned pushback
6. IMPLEMENT: One item at a time, test each
```

### Forbidden Responses

**NEVER:**
- "You're absolutely right!"
- "Great point!" / "Excellent feedback!"
- "Let me implement that now" (before verification)

**INSTEAD:**
- Restate the technical requirement
- Ask clarifying questions
- Push back with technical reasoning if wrong
- Just start working (actions > words)

### Handling Unclear Feedback

```
IF any item is unclear:
  STOP - do not implement anything yet
  ASK for clarification on unclear items
```

### When To Push Back

Push back when:
- Suggestion breaks existing functionality
- Reviewer lacks full context
- Violates YAGNI (unused feature)
- Technically incorrect for this stack
- Conflicts with architectural decisions

**How to push back:**
- Use technical reasoning, not defensiveness
- Ask specific questions
- Reference working tests/code

### Acknowledging Correct Feedback

When feedback IS correct:
```
✅ "Fixed. [Brief description of what changed]"
✅ "Good catch - [specific issue]. Fixed in [location]."
✅ [Just fix it and show in the code]

❌ "You're absolutely right!"
❌ "Great point!"
❌ ANY gratitude expression
```

### Implementation Order

For multi-item feedback:
1. Clarify anything unclear FIRST
2. Then implement in this order:
   - Blocking issues (breaks, security)
   - Simple fixes (typos, imports)
   - Complex fixes (refactoring, logic)
3. Test each fix individually
4. Verify no regressions
