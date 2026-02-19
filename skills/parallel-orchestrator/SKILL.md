---
name: parallel-orchestrator
description: Use this skill after a plan has been approved and development is about to start. Breaks down the implementation into independent work streams that can be executed by subagents in parallel. Each subagent has access to all available skills. Trigger when transitioning from planning to implementation, especially for multi-file or multi-component work.
---

# Parallel Orchestrator Protocol

When this skill is activated, decompose approved plans into independent work streams that can be executed by subagents in parallel. You are the conductor - your job is to identify parallelizable work, define clear boundaries, and coordinate execution.

## Core Mindset

You've seen projects where:
- Sequential execution wasted time on independent tasks
- Unclear boundaries caused merge conflicts and duplicated work
- Subagents stepped on each other's toes
- Context wasn't shared properly between workers

Channel this into effective orchestration.

## When to Activate

Trigger this skill when:
- A plan has been approved and is ready for implementation
- The work spans multiple files, components, or modules
- Tasks have natural independence (different files, different concerns)
- Sequential execution would be unnecessarily slow

## Phase 1: Analyze the Plan

Before decomposing, understand:

### Dependency Analysis

```
For each task in the plan:
1. What files does it touch?
2. What does it depend on? (other tasks, shared state, order requirements)
3. What depends on it?
4. Can it be executed in isolation?
```

### Independence Criteria

Tasks are independent if:
- They touch different files
- They don't share mutable state
- Order of completion doesn't matter
- No task needs another's output as input

Tasks are dependent if:
- They modify the same file
- One creates something another uses (e.g., type definitions)
- They share database migrations or schema changes
- Integration testing requires specific order

## Phase 2: Decompose into Work Streams

### Identify Parallel Tracks

Group independent tasks into work streams:

```
## Work Stream Analysis

### Stream 1: [Name]
**Scope**: [What this stream covers]
**Files**: [List of files this stream owns]
**Tasks**:
- [ ] Task A
- [ ] Task B
**Dependencies**: None / Depends on Stream X completing first
**Skills to Apply**: [Relevant skills from the skill set]

### Stream 2: [Name]
**Scope**: [What this stream covers]
**Files**: [List of files this stream owns]
**Tasks**:
- [ ] Task C
- [ ] Task D
**Dependencies**: None / Depends on Stream Y
**Skills to Apply**: [Relevant skills]
```

### Boundary Rules

Each work stream must have:
- **Clear file ownership** - No two streams modify the same file
- **Defined interfaces** - If streams need to interact, define the contract upfront
- **Independent testability** - Each stream should be verifiable in isolation
- **Explicit handoff points** - Where does this stream's output become another's input?

## Phase 3: Define Subagent Context

Each subagent needs:

### Task Briefing Template

```
## Subagent Task: [Stream Name]

### Objective
[One sentence goal]

### Scope
**You own these files**:
- path/to/file1.ts
- path/to/file2.ts

**Do NOT modify**:
- path/to/shared/types.ts (owned by Stream 1)
- path/to/other/file.ts (owned by Stream 2)

### Tasks
1. [ ] [Specific task]
2. [ ] [Specific task]

### Context
[Relevant background from the plan]
[Decisions already made]
[Constraints to follow]

### Required Skills (MUST apply all relevant)
You MUST apply these skills during implementation:

| Skill | When | Verification |
|-------|------|--------------|
| brevity-engineer | After writing any code | Confirm code is minimal, no scope creep |
| linting-engineer | After every file change | Run linters, fix all errors |
| test-writer | After implementing logic | Write tests, verify they pass |
| framework-engineer | When adding dependencies | Research current best practices |
| self-doubt | For complex logic | Walk through reasoning step-by-step |

### Success Criteria (ALL must be verified)

**Functional Criteria**:
- [ ] [Specific, testable behavior - "User can X and sees Y"]
- [ ] [Specific, testable behavior]

**Code Quality Criteria**:
- [ ] All linters pass (linting-engineer verified)
- [ ] Code is minimal - no unnecessary additions (brevity-engineer verified)
- [ ] Tests written and passing (test-writer verified)
- [ ] No hardcoded values that should be configurable
- [ ] No TODO comments left unresolved

**Integration Criteria**:
- [ ] Changes work with existing code
- [ ] No type errors
- [ ] No broken imports

### Self-Verification Checklist
Before reporting completion, verify:
- [ ] Re-read all modified files - does the code make sense?
- [ ] Run the tests - do they actually pass?
- [ ] Check the linter output - any warnings ignored?
- [ ] Review against original task - did I miss anything?
- [ ] Review against success criteria - all boxes checked?

### Handoff
When complete, report:
- Files modified (with line counts)
- Tests added/modified
- Any concerns or edge cases discovered
- Verification status for each success criterion
```

## Phase 4: Orchestration Strategy

### Parallel Execution Pattern

```
┌─────────────────────────────────────────────────────┐
│                   ORCHESTRATOR                       │
│  (You - coordinates, doesn't implement directly)    │
└───────────────┬─────────────┬───────────────────────┘
                │             │
    ┌───────────▼───┐    ┌────▼───────────┐
    │   Subagent 1  │    │   Subagent 2   │
    │   Stream A    │    │   Stream B     │
    │   [files]     │    │   [files]      │
    └───────────────┘    └────────────────┘
                │             │
                ▼             ▼
    ┌─────────────────────────────────────┐
    │          INTEGRATION PHASE           │
    │  (Verify, resolve conflicts, test)  │
    └─────────────────────────────────────┘
```

### Execution Phases

1. **Parallel Phase**: Launch independent subagents simultaneously
2. **Sync Points**: Define where streams must synchronize
3. **Integration Phase**: Merge results, resolve any conflicts
4. **Verification Phase**: Comprehensive double-check of ALL work (see below)

## Phase 5: Verification Protocol (MANDATORY)

After all subagents complete, the orchestrator MUST run a full verification. This is not optional.

### Verification Checklist

```
## Post-Implementation Verification

### 1. Code Review (brevity-engineer lens)
- [ ] Review ALL modified files
- [ ] Check for scope creep - any code that wasn't in the plan?
- [ ] Check for unnecessary abstractions
- [ ] Check for leftover debug code, console.logs, TODOs

### 2. Linting & Types (linting-engineer lens)
- [ ] Run full linter across all changed files
- [ ] Run type checker
- [ ] Zero errors, zero warnings (or justified exceptions)
- [ ] Consistent typing patterns with rest of codebase

### 3. Tests (test-writer lens)
- [ ] Run full test suite
- [ ] All tests pass
- [ ] New code has test coverage
- [ ] Tests are meaningful (not just coverage padding)

### 4. Integration Verification
- [ ] No merge conflicts between subagent work
- [ ] Shared interfaces match across boundaries
- [ ] No duplicate code introduced by parallel work
- [ ] Import paths all resolve correctly

### 5. Plan Compliance
- [ ] Every task from original plan is complete
- [ ] No tasks were skipped or partially done
- [ ] No unauthorized additions (stayed in scope)
- [ ] Success criteria from plan are ALL met

### 6. Double-Check Pass
Re-verify critical items:
- [ ] Security: No secrets, credentials, or sensitive data exposed
- [ ] Performance: No obvious N+1 queries or inefficient loops
- [ ] Error handling: Failures are handled gracefully
- [ ] Edge cases: Known edge cases from test-engineer are covered
```

### Verification Failure Protocol

If verification fails:
1. **Identify**: Which subagent's work has issues?
2. **Isolate**: What specific problem needs fixing?
3. **Fix**: Either fix directly or re-task the subagent
4. **Re-verify**: Run verification again after fixes
5. **Do not proceed** until all verification passes

### Handling Dependencies

For sequential dependencies:
```
Stream 1 (types/interfaces) → Stream 2 (implementation) → Stream 3 (tests)
```

For partial dependencies:
```
Stream 1 ──┬──→ Stream 2a ──┬──→ Integration
           └──→ Stream 2b ──┘
```

## Response Format

When orchestrating:

```
## Work Decomposition

### Plan Summary
[Brief recap of what we're implementing]

### Dependency Graph
[ASCII diagram or description of dependencies]

### Parallel Execution Plan

**Phase 1 - Parallel** (can run simultaneously):

#### Subagent 1: [Stream Name]
- Scope: [description]
- Files: [list]
- Tasks: [list]
- Skills: [relevant skills]
- Est. complexity: [Low/Medium/High]

#### Subagent 2: [Stream Name]
- Scope: [description]
- Files: [list]
- Tasks: [list]
- Skills: [relevant skills]
- Est. complexity: [Low/Medium/High]

**Phase 2 - Sequential** (depends on Phase 1):

#### Subagent 3: [Stream Name]
- Depends on: Subagent 1, Subagent 2
- ...

**Phase 3 - Integration & Verification** (MANDATORY):
- [ ] Merge all changes from subagents
- [ ] Run full test suite - must pass
- [ ] Run linting-engineer across entire codebase
- [ ] Run brevity-engineer review on all changes
- [ ] Verify ALL success criteria from each subagent
- [ ] Cross-check: no conflicts, no duplicates, no gaps

### Success Criteria Summary
[Aggregate all success criteria from subagents here]
- [ ] Criterion 1 from Subagent 1
- [ ] Criterion 2 from Subagent 1
- [ ] Criterion 1 from Subagent 2
- [ ] ...
- [ ] ALL criteria verified

### Conflict Prevention
[How we're avoiding merge conflicts and stepping on toes]

### Rollback Strategy
[What to do if a subagent fails]

### Final Sign-Off
- [ ] All subagent tasks complete
- [ ] All success criteria verified
- [ ] All verification checks pass
- [ ] Ready for user review
```

## Anti-Patterns to Avoid

**Over-parallelization**:
- Don't create 10 subagents for 10 one-line changes
- Some work is faster done sequentially
- Coordination overhead can exceed time saved

**Unclear boundaries**:
- Never have two subagents "share" a file
- Always define who owns what
- Ambiguity leads to conflicts

**Missing context**:
- Each subagent needs enough context to work independently
- Don't assume they can read the orchestrator's mind
- Include relevant decisions, constraints, and conventions

**Ignoring integration**:
- Parallel work still needs to come together
- Plan the merge strategy upfront
- Reserve time for integration testing

## Calibration

**Right-size the decomposition.** 2-4 parallel streams is usually optimal. More adds coordination overhead. Fewer misses parallelization benefits.

**Trust but verify.** Subagents are capable but may miss context. Review their work at sync points. Double-check EVERYTHING before reporting success.

**Fail fast.** If a subagent hits a blocker, surface it immediately rather than waiting for integration.

**Stay in your lane.** Don't cover:
- Whether the plan is good (skeptic-engineer already reviewed)
- Feature scope (product-manager already scoped)
- Test scenarios (test-engineer already identified)

This skill is purely about efficient execution of an approved plan.

## Skill Propagation (MANDATORY)

Each subagent MUST be instructed to apply these skills. This is not optional - it's how we maintain quality across parallel execution.

### Required Skills by Work Type

| Work Type | MUST Apply | Verification Step |
|-----------|------------|-------------------|
| Any code changes | brevity-engineer | Review for minimalism before completion |
| Any code changes | linting-engineer | Run linter, zero errors before completion |
| New features | test-writer | Tests written and passing |
| Adding deps | framework-engineer | Research documented, modern library chosen |
| Complex logic | self-doubt | Step-by-step reasoning documented |
| Documentation | documentation-engineer | CLAUDE.md updated if needed |

### Skill Application Protocol

For each subagent:
1. **List required skills** in the briefing (not "available" - "required")
2. **Define verification** for each skill application
3. **Require sign-off** - subagent must confirm each skill was applied

Example briefing language:
```
You MUST apply these skills and verify completion:

1. brevity-engineer
   - Apply: Review all code for minimalism
   - Verify: [ ] No unnecessary code, no scope creep

2. linting-engineer
   - Apply: Run linter after all changes
   - Verify: [ ] Zero lint errors, zero warnings

3. test-writer
   - Apply: Write tests for new functionality
   - Verify: [ ] Tests exist and pass
```

### Non-Negotiable Quality Gates

Before ANY subagent can report "complete":
- [ ] All required skills applied
- [ ] All verification checkboxes checked
- [ ] Self-review performed
- [ ] No known issues hidden or deferred

## Orchestrator Completion Checklist

Before the orchestrator can report the work as done, ALL of the following must be verified:

```
## Final Orchestrator Verification

### Subagent Completion
- [ ] Subagent 1 reported complete with all verifications
- [ ] Subagent 2 reported complete with all verifications
- [ ] [All subagents accounted for]

### Integration Verification
- [ ] All changes merged without conflicts
- [ ] No duplicate code across subagent boundaries
- [ ] Interfaces between components work correctly
- [ ] No orphaned imports or broken references

### Full Codebase Checks
- [ ] FULL test suite passes (not just new tests)
- [ ] FULL linter passes across all changed files
- [ ] Type checking passes
- [ ] No regressions introduced

### Plan Compliance
- [ ] Original plan: every task complete
- [ ] Success criteria: every criterion verified
- [ ] Scope: no unauthorized additions
- [ ] Quality: all skills properly applied

### Double-Check Items
- [ ] Re-read each modified file one more time
- [ ] Run tests one more time
- [ ] Check linter one more time
- [ ] Verify no debug code, console.logs, or TODOs left

### Sign-Off
- [ ] I have personally verified all the above
- [ ] I am confident this work is complete and correct
- [ ] Ready to hand off to user for final review
```

**Do NOT report completion until every box is checked.**
