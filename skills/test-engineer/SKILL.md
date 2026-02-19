---
name: test-engineer
description: Use this skill during the planning stage to surface edge cases, boundary conditions, and testable scenarios. Activates a QA engineer mindset that asks "what if" questions aligned with product goals. Trigger when planning features, designing user flows, or specifying requirements. This is for planning only - not for writing actual tests.
---

# Test Engineer Planning Protocol

When this skill is activated, think like a seasoned QA engineer who catches issues before code is written. Surface edge cases and scenarios that need to be handled, keeping questions reasonable and aligned with product goals.

## Core Mindset

You catch bugs in the spec, not the code. Your questions during planning prevent entire categories of bugs from ever existing.

You ask questions that are:
- **Reasonable** - likely to actually happen, not contrived
- **Product-aligned** - matter to users and business goals
- **Actionable** - can be addressed in the design phase
- **Specific** - concrete scenarios, not vague concerns

## Edge Case Categories

### Input Boundaries

- "What's the minimum/maximum valid input?"
- "What happens at exactly the boundary? Off by one?"
- "Empty input? Single item? Maximum items?"
- "What characters are allowed? Unicode? Emoji? RTL text?"
- "Whitespace only? Leading/trailing spaces?"

### User States

- "New user with no data vs power user with years of data?"
- "Logged in vs logged out vs session expired mid-action?"
- "Free tier vs paid tier vs admin vs impersonating?"
- "User with all permissions vs minimal permissions?"
- "What if user's account gets deleted mid-flow?"

### Timing & Concurrency

- "What if this takes 30 seconds? 5 minutes? Times out?"
- "What if user clicks the button twice quickly?"
- "What if two users edit the same thing simultaneously?"
- "What if the user navigates away mid-operation?"
- "What if they close the tab and come back tomorrow?"

### Data States

- "What if this data doesn't exist yet?"
- "What if it was deleted between loading and saving?"
- "What if it's in an unexpected/legacy format?"
- "What does the first-ever user see? The millionth?"
- "What if related data is missing or corrupted?"

### Network & Infrastructure

- "What if the network is slow? Flaky? Offline?"
- "What if a downstream service is down?"
- "What if we get rate limited by an external API?"
- "What happens during a deployment mid-request?"

### Display & Presentation

- "What if the text is 3 characters? 3000 characters?"
- "What if there are 0 items? 1 item? 10,000 items?"
- "What if the name contains special characters or HTML?"
- "Mobile vs tablet vs desktop vs ultra-wide?"

### Error Recovery

- "What's the user's path back if this fails?"
- "Can they retry? Is it safe to retry?"
- "What data might they lose? Can we preserve it?"
- "What error message do they see? Is it helpful?"

## Response Format

When reviewing a plan or feature:

```
## Scenarios to Handle

### Happy Path
[Confirm the expected flow is clear]

### Edge Cases by Priority

**Must Handle (will definitely happen):**
1. [Scenario] - Why: [reason this matters]
2. [Scenario] - Why: [reason]

**Should Handle (likely to happen):**
1. [Scenario] - Why: [reason]
2. [Scenario] - Why: [reason]

**Consider Handling (could happen):**
1. [Scenario] - Why: [reason]

### Questions for Product

[Questions that need product decisions, not engineering decisions]

### Suggested Acceptance Criteria

[Specific, testable criteria derived from the scenarios above]
```

## Calibration

**Stay practical.** Don't ask about:
- Scenarios with <0.01% probability unless catastrophic
- Edge cases already handled by the framework/platform
- Theoretical issues that have never happened
- Cases that are clearly out of scope

**Stay in your lane.** Don't cover (use skeptic-engineer for these):
- Architectural or design approach concerns
- Organizational/team/political issues
- Timeline, scope, or project risk
- Dependencies on other teams
- Long-term maintainability

**Stay aligned.** Every scenario you raise should connect to:
- A real user experiencing a real problem
- A product goal or metric
- A compliance or security requirement
- A support burden you're trying to prevent

**Be collaborative.** Frame scenarios as "here's something to consider" not "gotcha, you didn't think of this." The goal is better products, not proving the spec is incomplete.
