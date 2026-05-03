# Systematic Debugging - Detailed Reference

Random fixes waste time and create new bugs. Quick patches mask underlying issues.

**Core principle:** ALWAYS find root cause before attempting fixes. Symptom fixes are failure.

**Violating the letter of this process is violating the spirit of debugging.**

## The Iron Law

```
NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST
```

If you haven't completed Phase 1, you cannot propose fixes.

## When to Use

Use for ANY technical issue:
- Test failures
- Bugs in production
- Unexpected behavior
- Performance problems
- Build failures
- Integration issues

**Use this ESPECIALLY when:**
- Under time pressure (emergencies make guessing tempting)
- "Just one quick fix" seems obvious
- You've already tried multiple fixes
- Previous fix didn't work
- You don't fully understand the issue

## The Four Phases

You MUST complete each phase before proceeding to the next.

### Phase 1: Root Cause Investigation

**BEFORE attempting ANY fix:**

1. **Read Error Messages Carefully**
   - Don't skip past errors or warnings
   - They often contain the exact solution
   - Read stack traces completely
   - Note line numbers, file paths, error codes

2. **Reproduce Consistently**
   - Can you trigger it reliably?
   - What are the exact steps?
   - Does it happen every time?
   - If not reproducible → gather more data, don't guess

3. **Check Recent Changes**
   - What changed that could cause this?
   - Git diff, recent commits
   - New dependencies, config changes
   - Environmental differences

4. **Gather Evidence in Multi-Component Systems**
   - For EACH component boundary: log what enters and exits
   - Verify environment/config propagation
   - Check state at each layer
   - Run once to gather evidence showing WHERE it breaks

5. **Trace Data Flow**
   - Where does bad value originate?
   - What called this with bad value?
   - Keep tracing up until you find the source
   - Fix at source, not at symptom

### Phase 2: Pattern Analysis

**Find the pattern before fixing:**

1. **Find Working Examples** - Locate similar working code in same codebase
2. **Compare Against References** - Read reference implementation COMPLETELY
3. **Identify Differences** - List every difference between working and broken
4. **Understand Dependencies** - What other components, settings, config, environment?

### Phase 3: Hypothesis and Testing

**Scientific method:**

1. **Form Single Hypothesis** - "I think X is the root cause because Y"
2. **Test Minimally** - Smallest possible change, one variable at a time
3. **Verify Before Continuing** - Did it work? Yes → Phase 4. No → NEW hypothesis
4. **When You Don't Know** - Say "I don't understand X", don't pretend

### Phase 4: Implementation

**Fix the root cause, not the symptom:**

1. **Create Failing Test Case** - Simplest possible reproduction
2. **Implement Single Fix** - ONE change at a time, no "while I'm here"
3. **Verify Fix** - Test passes? No other tests broken?
4. **If Fix Doesn't Work:**
   - STOP. Count fixes tried.
   - If < 3: Return to Phase 1
   - **If ≥ 3: Question the architecture**
5. **If 3+ Fixes Failed: Question Architecture**
   - Each fix reveals new shared state/coupling/problem
   - Fixes require "massive refactoring"
   - Each fix creates new symptoms elsewhere
   - **Discuss with the user before attempting more fixes**

## Red Flags - STOP and Follow Process

If you catch yourself thinking:
- "Quick fix for now, investigate later"
- "Just try changing X and see if it works"
- "Add multiple changes, run tests"
- "Skip the test, I'll manually verify"
- "It's probably X, let me fix that"
- "I don't fully understand but this might work"
- "One more fix attempt" (when already tried 2+)
- Each fix reveals new problem in different place

**ALL of these mean: STOP. Return to Phase 1.**

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "Issue is simple, don't need process" | Simple issues have root causes too. |
| "Emergency, no time for process" | Systematic debugging is FASTER than thrashing. |
| "Just try this first, then investigate" | First fix sets the pattern. Do it right. |
| "I'll write test after confirming fix works" | Untested fixes don't stick. Test first. |
| "Multiple fixes at once saves time" | Can't isolate what worked. Causes new bugs. |
| "I see the problem, let me fix it" | Seeing symptoms ≠ understanding root cause. |
| "One more fix attempt" (after 2+ failures) | 3+ failures = architectural problem. |

## Supporting Techniques

### Root Cause Tracing
Trace bugs backward through call stack to find original trigger:
1. Observe the symptom
2. Find immediate cause
3. Ask: What called this?
4. Keep tracing up
5. Find original trigger
6. Fix at source + add defense-in-depth

### Defense-in-Depth Validation
When you fix a bug caused by invalid data, add validation at EVERY layer:
- **Layer 1: Entry Point Validation** - Reject obviously invalid input at API boundary
- **Layer 2: Business Logic Validation** - Ensure data makes sense for this operation
- **Layer 3: Environment Guards** - Prevent dangerous operations in specific contexts
- **Layer 4: Debug Instrumentation** - Capture context for forensics

### Condition-Based Waiting
Replace arbitrary timeouts with condition polling:
```typescript
// BAD: Guessing at timing
await new Promise(r => setTimeout(r, 50));

// GOOD: Waiting for condition
await waitFor(() => getResult() !== undefined);
```

Use when tests have arbitrary delays, are flaky, or timeout in parallel.

## Real-World Impact

- Systematic approach: 15-30 minutes to fix
- Random fixes approach: 2-3 hours of thrashing
- First-time fix rate: 95% vs 40%
- New bugs introduced: Near zero vs common
