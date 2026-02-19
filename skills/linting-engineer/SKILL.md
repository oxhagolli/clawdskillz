---
name: linting-engineer
description: Use this skill after every code change to run linting and type checking. Ensures code passes all lint checks, types are correct and consistent, and typing patterns match repo conventions. Trigger after writing or modifying code.
---

# Linting Engineer Protocol

After every code change, run the linter and type checker. Fix what they catch. No exceptions.

## Core Principles

- **Run after every change** - Not at the end, after each file/change
- **Use repo's tools** - Don't add new linters or type checkers
- **Strictest mode available** - If there's a strict flag, use it
- **Fix, don't disable** - Never add ignore comments unless absolutely necessary
- **Type everything** - All new code must be properly typed
- **Be consistent** - Use the same typing patterns as the rest of the repo

## Step 1: Find the Repo's Linter

Check for linting configuration in this order:

```
1. package.json scripts (lint, lint:fix, check, format)
2. Config files:
   - .eslintrc.* / eslint.config.*
   - .prettierrc.* / prettier.config.*
   - pyproject.toml (ruff, black, flake8)
   - setup.cfg / .flake8
   - .rubocop.yml
   - rustfmt.toml / .rustfmt.toml
   - .golangci.yml
3. Makefile targets (lint, check, fmt)
4. CI config (.github/workflows/*.yml) - see what CI runs
```

**Use what exists. Don't create new configs.**

## Step 2: Run Linting

Run the linter command found in the repo:

```bash
# Common patterns - use what the repo has
npm run lint          # JS/TS projects
npm run lint:fix      # With auto-fix
pnpm lint             # pnpm projects
yarn lint             # yarn projects

ruff check .          # Python (ruff)
ruff check . --fix    # Python with fix
black --check .       # Python (black)
flake8 .              # Python (flake8)

cargo fmt --check     # Rust
cargo clippy          # Rust

go fmt ./...          # Go
golangci-lint run     # Go
```

**If strict mode exists, use it:**
```bash
npm run lint -- --max-warnings=0
ruff check . --strict
cargo clippy -- -D warnings
```

## Step 3: Run Type Checking

Find and run the repo's type checker:

```bash
# TypeScript
tsc --noEmit
npm run typecheck

# Python
mypy .
pyright .
python -m pyright

# Check pyproject.toml or mypy.ini for config
```

**Use strict mode:**
```bash
tsc --noEmit --strict
mypy --strict .
pyright --strict
```

## Step 4: Ensure Type Consistency

Before adding types, check how the repo does it:

**Find the pattern:**
```
1. Look at 3-5 similar files in the repo
2. Note which typing approach they use
3. Match exactly
```

**Consistency rules:**

| If repo uses... | Then use... | Don't mix with... |
|-----------------|-------------|-------------------|
| TypeScript interfaces | interfaces | type aliases for objects |
| TypeScript type aliases | type aliases | interfaces for same purpose |
| Pydantic models | Pydantic | dataclasses or TypedDict |
| Python dataclasses | dataclasses | Pydantic or attrs |
| typing.TypedDict | TypedDict | Pydantic or dataclasses |
| attrs | attrs | dataclasses or Pydantic |
| Zod schemas | Zod | io-ts or yup |

**Red flags:**
- Mixing Pydantic and dataclasses in the same module
- Using `Any` when a proper type exists
- Inconsistent Optional vs Union[X, None] style
- Different import styles (`from typing import` vs `typing.`)

**Fix inconsistencies:**
```python
# Bad - mixing styles
from pydantic import BaseModel
from dataclasses import dataclass

class UserRequest(BaseModel): ...  # Pydantic
@dataclass
class UserResponse: ...  # dataclass - inconsistent!

# Good - pick one, match repo
from pydantic import BaseModel

class UserRequest(BaseModel): ...
class UserResponse(BaseModel): ...
```

## Step 5: Fix All Violations

For each lint or type violation:

1. **Read the error** - Understand what rule was violated
2. **Fix the code** - Change the code to comply
3. **Re-run linter** - Verify the fix worked
4. **Repeat** - Until clean

**Never:**
- Add `// eslint-disable` or `# noqa` to bypass
- Modify linter config to make errors pass
- Skip files with `--ignore-path`

**Only exception:** If fixing would break functionality and the rule is genuinely wrong for this case, document why and get approval before disabling.

## Step 6: Verify

After all fixes:

```bash
# Run full lint check
npm run lint

# Run type check
tsc --noEmit
mypy .

# Run formatter check if separate
npm run format:check
prettier --check .
```

All must pass with zero warnings and zero type errors.

## Response Format

After running checks:

```
## Lint & Type Check Results

**Linter**: [command] | **Type checker**: [command]
**Mode**: [strict/standard]

**Initial violations**: [lint count] lint, [type count] type errors

**Fixes applied**:
- [file:line] - [what was fixed]

**Type consistency**: [consistent / issues found]
- [any inconsistencies noted]

**Final status**: [PASS / FAIL]

**Remaining issues** (if any):
- [issue] - Why: [reason]
```

## Common Fixes by Language

**JavaScript/TypeScript:**
- Missing semicolons → Add them (or remove if repo style)
- Unused imports → Remove them
- `any` types → Add proper types, never leave `any`
- console.log → Remove or use proper logging
- Missing return types → Add explicit return types
- Implicit any in parameters → Add parameter types

**Python:**
- Import order → Let isort/ruff fix
- Line length → Break lines appropriately
- Unused variables → Remove or prefix with `_`
- Missing type hints → Add them to all functions
- `Any` usage → Replace with proper types
- Inconsistent Optional → Match repo style (Optional[X] vs X | None)
- Mixed typing patterns → Convert to repo's standard (Pydantic/dataclass/TypedDict)

**Go:**
- Formatting → `go fmt` handles it
- Unused imports → Remove them
- Error not checked → Handle the error

**Rust:**
- Warnings → Fix or explicitly allow with reason
- Formatting → `cargo fmt` handles it
- Clippy suggestions → Usually correct, apply them

## Calibration

**Run early, run often.** Don't wait until you've written 500 lines to lint.

**Fix immediately.** Don't accumulate lint debt.

**Trust the linter.** If the repo configured a rule, there's a reason. Fix the code, don't fight the rule.

**Stay in your lane.** Don't cover:
- Whether the linting rules are correct (that's repo config)
- Code logic issues (that's code review)
- Test coverage (that's testing)
