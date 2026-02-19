# clawdskillz

Engineering mindset skills for Claude Code. These skills activate different professional personas - skeptical engineers, pragmatic PMs, rigorous thinkers - to improve the quality of AI-assisted development.

## Skills Overview

| Skill | Phase | Purpose |
|-------|-------|---------|
| **self-doubt** | Meta | Question your reasoning step-by-step |
| **venture-capitalist** | Pre-product | Validate business ideas, research competitors |
| **product-manager** | Planning | Scope features, clarify requirements |
| **skeptic-engineer** | Planning | Poke holes in technical approaches |
| **test-engineer** | Planning | Surface edge cases and scenarios |
| **framework-engineer** | Implementation | Research libraries, find deprecations |
| **brevity-engineer** | Implementation | Ensure minimal, spec-aligned code |
| **linting-engineer** | Implementation | Run linters, enforce type consistency |
| **test-writer** | Implementation | Write fast, meaningful tests |
| **documentation-engineer** | Implementation | Maintain CLAUDE.md and docs |
| **scaffold-engineer** | Setup | Initialize projects with proper tooling |

## Installation

### Option 1: Install via Marketplace (Recommended)

Add the marketplace to Claude Code:

```
/plugin marketplace add oxhagolli/clawdskillz
```

Then install the plugin set you want:

```
# Install all skills
/plugin install all-skills@clawdskillz

# Or install by category
/plugin install planning-skills@clawdskillz
/plugin install implementation-skills@clawdskillz
/plugin install setup-skills@clawdskillz
/plugin install thinking-skills@clawdskillz
```

### Option 2: Install Directly

Install a specific plugin directly:

```
/plugin install all-skills@oxhagolli/clawdskillz
```

### Option 3: Copy to Your Project

Copy the skills you want into your project's `.claude/skills/` directory:

```bash
# Clone the repo
git clone https://github.com/oxhagolli/clawdskillz.git

# Copy specific skills
cp -r clawdskillz/skills/skeptic-engineer .claude/skills/
cp -r clawdskillz/skills/product-manager .claude/skills/
```

### Option 4: Add to CLAUDE.md

Copy the content of any SKILL.md directly into your project's CLAUDE.md:

```bash
cat clawdskillz/skills/skeptic-engineer/SKILL.md >> CLAUDE.md
```

## Plugin Sets

### thinking-skills
- `self-doubt` - Rigorous step-by-step reasoning with self-questioning

### planning-skills
- `venture-capitalist` - Business validation, competitor research, market analysis
- `product-manager` - Requirements, scope control, clarity
- `skeptic-engineer` - Technical approach critique, architecture risks
- `test-engineer` - Edge cases, scenarios to handle during planning

### implementation-skills
- `framework-engineer` - Library selection, deprecation checks, best practices
- `brevity-engineer` - Code minimalism, repo standards, scope in code
- `linting-engineer` - Lint execution, type checking, type consistency
- `test-writer` - Integration tests, fast tests, pragmatic coverage
- `documentation-engineer` - CLAUDE.md per directory, ARCHITECTURE.md

### setup-skills
- `scaffold-engineer` - Project initialization with pyscaffold, pre-commit

### all-skills
All of the above.

## Usage

Once installed, skills activate automatically based on context. You can also invoke them explicitly:

```
Apply the skeptic-engineer skill: I'm planning to rewrite our auth system to use JWTs...

Use self-doubt: Walk me through why this algorithm is O(n log n)...

Apply venture-capitalist: I want to build a new SaaS for...
```

## Skill Separation

Each skill has a clear domain and explicitly defers to other skills:

Idea & Design:
```
venture-capitalist → "Should this exist?" (market, competition)
        ↓
product-manager → "What exactly should it do?" (scope, requirements)
        ↓
skeptic-engineer → "Will this approach work?" (architecture, risks)
        ↓
test-engineer → "What scenarios must we handle?" (edge cases)
```

Implementation:
```
framework-engineer → "Which libraries should we use?" (dependencies)
        ↓
brevity-engineer → "Is this code minimal?" (scope in code)
        ↓
linting-engineer → "Does this pass checks?" (lint, types)
        ↓
test-writer → "Are tests meaningful and fast?" (test implementation)
        ↓
documentation-engineer → "Is this documented?" (CLAUDE.md, docs)
```

## License
[Apache License 2.0](./LICENSE)
