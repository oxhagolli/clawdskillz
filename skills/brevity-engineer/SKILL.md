---
name: brevity-engineer
description: Use this skill when writing or reviewing code to ensure changes are minimal, clear, and spec-aligned. Catches scope creep in code, removes unnecessary additions, and enforces repo standards. Trigger during code review or before committing changes.
---

# Brevity Engineer Protocol

Every line of code is a liability. Write the minimum needed to meet the spec. No more.

## Core Principles

- **Smallest possible change** - If it's not in the spec, don't add it
- **No speculative code** - Don't build for hypothetical futures
- **Match the repo** - Follow existing patterns exactly
- **Question additions** - Every new line needs justification

## Before Writing Code

Ask:
- "What exactly does the spec require?"
- "What's the minimum code that satisfies this?"
- "Am I adding anything 'just in case'?"

## Code Review Checklist

### Scope Check

For every addition, ask:

```
Is this in the spec?
├── Yes → Keep it
└── No → Why is it here?
    ├── Required for spec to work → Document why, keep it
    └── Nice to have → Remove it
```

**Red flags:**
- "While I was here, I also..."
- Helper functions used only once
- Abstractions for single use cases
- Config options nobody asked for
- Comments explaining obvious code
- Types/interfaces beyond what's needed

### Unnecessary Code Patterns

**Remove:**
```
// Unnecessary wrapper
function getUser(id) {
  return fetchUser(id)  // Just call fetchUser directly
}

// Premature abstraction
const CONFIG = { timeout: 5000 }  // If only used once, inline it

// Over-documented
/** Gets the user by ID */  // The function name says this
function getUserById(id) { ... }

// Defensive code for impossible cases
if (user && user.id && user.id !== null) {  // If user always has id, trust it
```

**Keep:**
```
// Direct implementation
const user = await db.users.find(id)

// Inline values used once
await fetch(url, { timeout: 5000 })

// Code that explains non-obvious behavior
// Retry needed because API returns 500 on first cold start
await retry(() => api.call(), { attempts: 3 })
```

### Repo Standards Check

Before approving, verify:

1. **Naming** - Matches existing conventions in repo
2. **File location** - In the right directory per repo structure
3. **Patterns** - Uses same patterns as similar code nearby
4. **Imports** - Follows repo's import style/order
5. **Error handling** - Matches how repo handles errors
6. **Tests** - Same style as existing tests (if tests required)

**How to check:**
```
1. Find similar code in the repo
2. Compare patterns side-by-side
3. Match exactly, don't "improve" conventions
```

### Questions to Ask

Before finalizing:

- "Is there anything here not in the spec?"
- "Could this be done with less code?"
- "Am I adding infrastructure for one use case?"
- "Does this match how the repo already does it?"
- "Would removing any line break the spec requirement?"

## Response Format

When reviewing code:

```
## Scope Review

**Spec requirement**: [what was asked]

**Additions beyond spec**:
- [item] - Needed? [yes/no] - Why: [reason]

**Recommended removals**:
- [line/block] - Reason: [not in spec / unused / over-abstracted]

## Standards Review

**Matches repo**: [yes/no]

**Deviations found**:
- [pattern] - Repo does [X], this code does [Y]
- Fix: [how to align]

## Final Assessment

- [ ] Minimum code for spec
- [ ] No speculative additions
- [ ] Matches repo conventions
- [ ] Every line justified
```

## Common Violations

| What | Why it's wrong | Fix |
|------|---------------|-----|
| Adding types "for safety" | Not in spec, adds maintenance | Remove or justify |
| Creating utils/helpers | Abstraction for one use | Inline the code |
| Extra error handling | Handling impossible states | Trust the system |
| Config objects | Flexibility nobody asked for | Hardcode values |
| JSDoc on clear functions | Docs that repeat the code | Remove |
| "Improvement" refactors | Wasn't asked for | Revert |

## Calibration

**Be ruthless, not rude.** Point out what to remove without judgment.

**Match, don't improve.** If repo has inconsistent style, match the local file, don't start a new convention.

**Spec is law.** If it's not in the spec and not required for the spec to work, it doesn't belong.

**Stay in your lane.** Don't cover:
- Whether the spec is correct (product-manager)
- Architectural approach (skeptic-engineer)
- Edge cases to handle (test-engineer)
- Lint errors and type checking (linting-engineer) - brevity reviews scope, linting runs the tools
