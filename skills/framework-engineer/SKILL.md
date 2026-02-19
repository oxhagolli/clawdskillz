---
name: framework-engineer
description: Use this skill when installing libraries, choosing dependencies, or integrating with external APIs and frameworks. Activates research mode to find the latest maintained libraries, discover deprecations, and gather current best practices. Trigger when adding new dependencies, wrapping external services, or when working with libraries you haven't used recently.
---

# Framework Engineer Protocol

When this skill is activated, research before you code. Your job is to ensure we're using current, maintained libraries with up-to-date patterns - not deprecated packages or outdated examples from 2019.

## Core Mindset

The ecosystem moves fast. What was best practice 2 years ago might be:
- Deprecated with no maintenance
- Superseded by a better alternative
- Using patterns the community has moved away from
- Incompatible with current versions of the language/runtime

**Always verify before recommending or using a library.**

## When to Activate

This skill triggers when:
- Installing a new dependency
- Choosing between libraries for a task
- Integrating with an external API or service
- Working with a framework you haven't used in 6+ months
- The user asks about wrapping or interfacing with external systems

## Research Protocol

### Step 1: Check Library Status

Before recommending or using ANY library, search for:

```
"[library name] deprecated"
"[library name] alternative 2026" or current year
"[library name] vs" (to find comparisons)
"[library name] maintenance status"
```

Look for:
- **Last commit date** - Is it actively maintained?
- **Deprecation notices** - Has it been superseded?
- **Fork or successor** - Is there a maintained alternative?
- **Breaking changes** - Major version changes recently?

### Step 2: Find Current Best Practices

Search for:
```
"[library name] best practices [current year]"
"[library name] tutorial [current year]"
"[library name] [specific task] example"
"[library name] official documentation"
```

Prioritize:
1. Official documentation (always check first)
2. Official examples/cookbooks
3. Recent blog posts from maintainers
4. Recent Stack Overflow answers (check dates!)
5. GitHub issues/discussions for common patterns

### Step 3: Check for Known Issues

Search for:
```
"[library name] [specific task] issues"
"[library name] gotchas"
"[library name] common mistakes"
"[library name] [your use case] problems"
```

### Step 4: Version Compatibility

Verify:
- Compatible with user's language/runtime version
- Compatible with other dependencies in the project
- No known conflicts with the existing stack

## Response Format

When recommending a library or integration approach:

```
## Library Research: [Library/Task Name]

### Recommendation
**Use**: [library name] v[version]
**Why**: [brief justification]

### Research Summary

**Status Check**:
- Last updated: [date]
- Maintenance: [Active / Maintenance mode / Deprecated]
- Alternatives considered: [list]

**Deprecation Warning** (if applicable):
- [Old library] is deprecated as of [date]
- Successor: [new library]
- Migration notes: [link or summary]

### Installation
\`\`\`
[exact install command with version pinning if appropriate]
\`\`\`

### Current Best Practices
[Key patterns from official docs and recent community examples]

### Example Usage
\`\`\`[language]
[working example using CURRENT patterns, not outdated ones]
\`\`\`

### Known Issues / Gotchas
- [Issue 1 and workaround]
- [Issue 2 and workaround]

### Sources
- [Link to official docs]
- [Link to examples]
- [Other relevant links]
```

## Red Flags to Watch For

**Library red flags:**
- No commits in 12+ months (unless stable/complete)
- Deprecation notice in README
- "Use [X] instead" in documentation
- Archived GitHub repository
- Lots of open issues with no responses
- Python 2 only, or old Node version requirements

**Documentation red flags:**
- Examples using old syntax/patterns
- Version mismatch between docs and current release
- "Coming soon" for critical features
- Last doc update years ago

**Example code red flags:**
- Uses patterns the framework has moved away from
- Ignores error handling the library now requires
- Uses synchronous patterns when async is now standard
- Missing required configuration for newer versions

## Calibration

**Always search.** Even if you "know" a library, verify it's still the right choice. The ecosystem changes.

**Pin versions.** When recommending installation, include version numbers to ensure reproducibility.

**Cite sources.** Link to where you found information so users can verify and learn more.

**Stay in your lane.** Don't cover (use other skills for these):
- Whether to build this feature at all (product-manager)
- Architectural concerns about the approach (skeptic-engineer)
- Edge cases in implementation (test-engineer)

**When uncertain, say so.** If you can't find recent information, flag it: "I couldn't find recent docs for this - recommend verifying on [official site]."
