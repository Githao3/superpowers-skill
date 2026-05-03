# Subagent-Driven Development - Detailed Reference

Execute plan by dispatching fresh subagent per task, with two-stage review after each: spec compliance review first, then code quality review.

**Why subagents:** You delegate tasks to specialized agents with isolated context. By precisely crafting their instructions and context, you ensure they stay focused and succeed at their task. They should never inherit your session's context or history — you construct exactly what they need. This also preserves your own context for coordination work.

**Core principle:** Fresh subagent per task + two-stage review (spec then quality) = high quality, fast iteration

## When to Use

**Use when:**
- Have implementation plan with mostly independent tasks
- Staying in same session
- Want faster iteration without human-in-loop between tasks

**Don't use when:**
- Tasks are tightly coupled
- Need parallel session execution (use executing-plans instead)

## The Process

### Step 1: Read Plan and Create Todos

1. Read plan file once
2. Extract all tasks with full text and context
3. Create TodoWrite with all tasks

### Step 2: Per-Task Loop

For each task:

1. **Dispatch implementer subagent** with full task text + context
   - Use implementer prompt template (see below)
   - Provide scene-setting context: where this fits, dependencies, architectural context
   - If implementer asks questions, answer clearly and re-dispatch

2. **Implementer reports status:**
   - **DONE:** Proceed to spec compliance review
   - **DONE_WITH_CONCERNS:** Read concerns before proceeding
   - **NEEDS_CONTEXT:** Provide missing context and re-dispatch
   - **BLOCKED:** Assess blocker, provide more context or escalate

3. **Dispatch spec reviewer subagent** (after implementer completes)
   - Verify code matches spec requirements
   - Check for missing requirements AND extra/unneeded work
   - If issues found: implementer fixes, then re-review

4. **Dispatch code quality reviewer subagent** (after spec compliance passes)
   - Verify code quality, architecture, testing
   - If issues found: implementer fixes, then re-review

5. **Mark task complete** in TodoWrite

### Step 3: Final Review and Finish

After all tasks complete:
1. Dispatch final code reviewer for entire implementation
2. Use finishing-a-development-branch skill

## Model Selection

Use the least powerful model that can handle each role to conserve cost and increase speed.

- **Mechanical implementation tasks** (1-2 files, clear spec): fast, cheap model
- **Integration and judgment tasks** (multi-file coordination): standard model
- **Architecture, design, and review tasks**: most capable model

## Implementer Prompt Template

```
You are implementing Task N: [task name]

## Task Description
[FULL TEXT of task from plan]

## Context
[Scene-setting: where this fits, dependencies, architectural context]

## Before You Begin
If you have questions about requirements, approach, dependencies, or anything unclear — ask them now.

## Your Job
1. Implement exactly what the task specifies
2. Write tests (following TDD if task says to)
3. Verify implementation works
4. Commit your work
5. Self-review
6. Report back

## Self-Review Checklist
- Did I fully implement everything in the spec?
- Is this my best work? Are names clear?
- Did I avoid overbuilding (YAGNI)?
- Do tests verify real behavior?

## Report Format
- **Status:** DONE | DONE_WITH_CONCERNS | BLOCKED | NEEDS_CONTEXT
- What you implemented
- Test results
- Files changed
- Self-review findings
- Any issues or concerns
```

## Spec Reviewer Prompt Template

```
You are reviewing whether an implementation matches its specification.

## What Was Requested
[FULL TEXT of task requirements]

## What Implementer Claims They Built
[From implementer's report]

## CRITICAL: Do Not Trust the Report
- Read the actual code
- Compare actual implementation to requirements line by line
- Check for missing pieces and extra features
- Verify by reading code, not by trusting report

Report:
- ✅ Spec compliant
- ❌ Issues found: [list specifically what's missing or extra]
```

## Code Quality Reviewer Prompt Template

After spec compliance passes, dispatch code quality reviewer with:
- WHAT_WAS_IMPLEMENTED: from implementer's report
- PLAN_OR_REQUIREMENTS: task from plan
- BASE_SHA: commit before task
- HEAD_SHA: current commit
- DESCRIPTION: task summary

Reviewer checks: code quality, architecture, testing, requirements, production readiness.
Returns: Strengths, Issues (Critical/Important/Minor), Assessment.

## Red Flags - NEVER

- Skip reviews (spec compliance OR code quality)
- Start code quality review before spec compliance is ✅
- Move to next task while either review has open issues
- Dispatch multiple implementation subagents in parallel (conflicts)
- Make subagent read plan file (provide full text instead)
- Skip scene-setting context
- Ignore subagent questions
- Accept "close enough" on spec compliance
- Let implementer self-review replace actual review
- Start implementation on main/master branch without explicit user consent
