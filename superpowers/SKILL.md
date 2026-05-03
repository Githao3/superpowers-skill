---
name: superpowers
description: "This skill should be used when starting any software development task - building features, fixing bugs, creating projects, or implementing changes. Provides a complete development methodology: brainstorm designs before coding, write detailed plans, execute with TDD and subagent-driven development, review code quality, and finish branches properly. Activates automatically before implementation to ensure disciplined, high-quality software delivery."
---

# Superpowers - Complete Development Methodology

Superpowers is a complete software development methodology built on composable skills. It enforces disciplined workflows: design before coding, plan before implementing, test before writing code, verify before claiming done.

**Core principle:** Process over guessing. Evidence over claims. Tests before code.

## When to Use

**Mandatory activation before ANY development work:**
- Creating features, building components, adding functionality
- Fixing bugs, debugging issues, resolving test failures
- Refactoring code, modifying behavior
- Starting new projects or major features

**The agent MUST check for this skill before any task. Even a 1% chance this skill applies means it should be invoked.**

## The Core Workflow

```
1. BRAINSTORMING → Refine idea into approved design
2. WRITING PLANS → Break design into bite-sized tasks
3. EXECUTION → Implement tasks with TDD (subagent-driven or inline)
4. CODE REVIEW → Verify quality at each step
5. FINISH BRANCH → Merge, PR, or cleanup
```

**The workflow is mandatory, not optional.** Skills trigger automatically at the right moments.

## Skill Priority

When multiple sub-skills could apply:

1. **Process skills first** (brainstorming, debugging) - determine HOW to approach the task
2. **Implementation skills second** (TDD, plan execution) - guide execution

"Let's build X" → brainstorming first, then implementation.
"Fix this bug" → systematic debugging first, then TDD.

## 1. Brainstorming (Before Any Code)

**Trigger:** Before any creative or implementation work.

**Hard gate:** Do NOT write any code until design is presented and user approves.

Read full process: `references/brainstorming.md`

**Quick checklist:**
1. Explore project context (files, docs, recent commits)
2. Ask clarifying questions one at a time
3. Propose 2-3 approaches with trade-offs
4. Present design in sections, get approval after each
5. Write design doc to `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`
6. Self-review spec (placeholders, contradictions, ambiguity)
7. User reviews written spec
8. Transition to writing-plans

**Key principles:**
- One question at a time, multiple choice preferred
- YAGNI ruthlessly - remove unnecessary features
- Always propose 2-3 approaches before settling
- Scale design sections to complexity
- Design for isolation and clarity

## 2. Writing Plans

**Trigger:** After design approval, before touching code.

Read full process: `references/writing-plans.md`

**Quick checklist:**
1. Map file structure before defining tasks
2. Break into bite-sized tasks (2-5 minutes each)
3. Every step has exact file paths, complete code, verification steps
4. No placeholders (no TBD, TODO, "implement later")
5. Self-review: spec coverage, placeholder scan, type consistency
6. Offer execution choice: subagent-driven or inline

**Task structure pattern:**
```
### Task N: [Component Name]
**Files:** Create/Modify/Test paths
- [ ] Step 1: Write the failing test [with code]
- [ ] Step 2: Run test to verify it fails [with command + expected output]
- [ ] Step 3: Write minimal implementation [with code]
- [ ] Step 4: Run test to verify it passes [with command]
- [ ] Step 5: Commit [with git command]
```

## 3. Execution

### Option A: Subagent-Driven Development (Recommended)

**Trigger:** When executing plans with independent tasks in same session.

Read full process: `references/subagent-driven-development.md`

**Core loop per task:**
1. Dispatch implementer subagent with full task text + context
2. Implementer implements, tests, commits, self-reviews
3. Dispatch spec reviewer subagent → verify code matches spec
4. Dispatch code quality reviewer subagent → verify code quality
5. Mark task complete, proceed to next

**Model selection:** Use least powerful model that can handle each role.
- Mechanical tasks (1-2 files, clear spec) → fast, cheap model
- Integration tasks (multi-file) → standard model
- Architecture/review tasks → most capable model

**Red flags - NEVER:**
- Skip reviews (spec compliance OR code quality)
- Start code quality review before spec compliance passes
- Move to next task while either review has open issues
- Start implementation on main/master without explicit consent

### Option B: Inline Execution

**Trigger:** When subagents unavailable or user prefers inline execution.

Read full process: `references/executing-plans.md`

**Process:**
1. Load and review plan critically
2. Execute tasks sequentially, follow steps exactly
3. Run verifications as specified
4. Stop when blocked, ask for help rather than guessing

## 4. Test-Driven Development (During Implementation)

**Trigger:** Before writing ANY implementation code. Always.

Read full process: `references/test-driven-development.md`

**The Iron Law:**
```
NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST
```

**RED-GREEN-REFACTOR cycle:**
1. **RED** - Write one minimal failing test
2. **Verify RED** - Run test, confirm it fails for right reason
3. **GREEN** - Write simplest code to pass
4. **Verify GREEN** - Run test, confirm it passes + all other tests pass
5. **REFACTOR** - Clean up while keeping tests green
6. Repeat

**Write code before test? Delete it. Start over. No exceptions.**

## 5. Systematic Debugging

**Trigger:** When encountering any bug, test failure, or unexpected behavior.

Read full process: `references/systematic-debugging.md`

**The Iron Law:**
```
NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST
```

**Four phases (complete each before next):**
1. **Root Cause Investigation** - Read errors, reproduce, check changes, gather evidence, trace data flow
2. **Pattern Analysis** - Find working examples, compare, identify differences
3. **Hypothesis and Testing** - Form single hypothesis, test minimally, verify
4. **Implementation** - Create failing test, implement single fix, verify

**If 3+ fixes fail:** STOP. Question the architecture, not the symptoms.

## 6. Code Review

### Requesting Code Review

**Trigger:** After each task completion, before merging.

Read full process: `references/code-review.md`

**Process:**
1. Get git SHAs (base and head)
2. Dispatch code reviewer with full context
3. Fix Critical issues immediately, Important before proceeding
4. Push back with technical reasoning if reviewer is wrong

### Receiving Code Review

**Core principle:** Verify before implementing. Technical correctness over social comfort.

**Forbidden responses:** "You're absolutely right!", "Great point!", "Thanks for catching that!"

**Instead:** Restate the technical requirement, ask clarifying questions, push back with reasoning if wrong, or just fix it.

## 7. Git Worktrees

**Trigger:** Before starting feature work that needs isolation.

Read full process: `references/git-worktrees.md`

**Process:**
1. Check existing directories (.worktrees/ > worktrees/)
2. Verify directory is git-ignored
3. Create worktree with new branch
4. Run project setup (auto-detect)
5. Verify clean test baseline

## 8. Finishing a Development Branch

**Trigger:** When implementation complete and all tests pass.

Read full process: `references/finishing-branch.md`

**Process:**
1. Verify tests pass (mandatory, no exceptions)
2. Determine base branch
3. Present exactly 4 options: merge locally, create PR, keep as-is, discard
4. Execute chosen option
5. Cleanup worktree (for options 1, 2, 4)

## 9. Verification Before Completion

**Trigger:** Before claiming ANY work is complete, fixed, or passing.

**The Iron Law:**
```
NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE
```

**The gate function:**
1. IDENTIFY what command proves the claim
2. RUN the full command (fresh, complete)
3. READ full output, check exit code
4. VERIFY output confirms the claim
5. ONLY THEN make the claim

**Red flags - STOP:**
- Using "should", "probably", "seems to"
- Expressing satisfaction before verification
- Trusting agent success reports
- About to commit/push/PR without verification

## 10. Dispatching Parallel Agents

**Trigger:** When facing 2+ independent tasks without shared state.

Read full process: `references/parallel-agents.md`

**Use when:** 3+ test files failing with different root causes, multiple independent subsystems broken.

**Don't use when:** Failures are related, need full context, agents would interfere.

**Pattern:**
1. Identify independent domains
2. Create focused agent tasks (specific scope, clear goal, constraints)
3. Dispatch in parallel
4. Review and integrate results

## Philosophy

- **Test-Driven Development** - Write tests first, always
- **Systematic over ad-hoc** - Process over guessing
- **Complexity reduction** - Simplicity as primary goal
- **Evidence over claims** - Verify before declaring success
- **YAGNI** - You Aren't Gonna Need It
- **DRY** - Don't Repeat Yourself

## Quick Reference - When to Use Each Sub-Skill

| Situation | Sub-Skill | Reference |
|-----------|-----------|-----------|
| Starting any creative work | Brainstorming | `references/brainstorming.md` |
| Design approved, need plan | Writing Plans | `references/writing-plans.md` |
| Plan ready, execute with subagents | Subagent-Driven Dev | `references/subagent-driven-development.md` |
| Plan ready, execute inline | Executing Plans | `references/executing-plans.md` |
| Writing any code | TDD | `references/test-driven-development.md` |
| Bug, test failure, unexpected behavior | Systematic Debugging | `references/systematic-debugging.md` |
| Task complete, need review | Code Review | `references/code-review.md` |
| Starting feature work | Git Worktrees | `references/git-worktrees.md` |
| All tasks done | Finishing Branch | `references/finishing-branch.md` |
| About to claim done | Verification | Inline (this file, section 9) |
| Multiple independent problems | Parallel Agents | `references/parallel-agents.md` |
| Creating new skills | Writing Skills | `references/writing-skills.md` |
