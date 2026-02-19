---
name: scaffold-engineer
description: Use this skill when creating a new Python project or setting up tooling for an existing one. Runs pyscaffold, configures pre-commit with comprehensive hooks, and sets up CLAUDE.md for documentation. Trigger when starting new projects or adding standard tooling.
---

# Scaffold Engineer Protocol

Set up projects right from the start. Proper tooling, proper structure, proper documentation hooks.

## Core Responsibilities

1. Run pyscaffold for project structure
2. Configure pre-commit with full hook suite
3. Update all tools to latest versions
4. Set up CLAUDE.md structure for documentation-engineer

## Step 1: Initialize with PyScaffold

```bash
# Install pyscaffold if needed
pip install pyscaffold

# Create new project
putup project_name --description "Project description" --license MIT

# Or add to existing project
putup --force .
```

**PyScaffold options to consider:**
```bash
putup project_name \
  --description "Description" \
  --license MIT \
  --url "https://github.com/org/project" \
  --pre-commit \
  --github-actions \
  --cirrus
```

## Step 2: Configure Pre-commit

Create or update `.pre-commit-config.yaml`:

```yaml
repos:
  # Standard pre-commit hooks
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v5.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-ast
      - id: check-xml
      - id: check-yaml
      - id: check-added-large-files
        args: ['--maxkb=1000']
      - id: check-merge-conflict
      - id: check-json
        exclude: ^frontend/tsconfig.*\.json$
      - id: check-toml
      - id: debug-statements
      - id: mixed-line-ending
        args: ['--fix=lf']

  # pyupgrade - modernize Python syntax
  - repo: https://github.com/asottile/pyupgrade
    rev: v3.19.1
    hooks:
      - id: pyupgrade

  # Black - code formatting
  - repo: https://github.com/psf/black
    rev: 24.10.0
    hooks:
      - id: black
        language_version: python3.12

  # blacken-docs - format code in docstrings
  - repo: https://github.com/asottile/blacken-docs
    rev: 1.19.1
    hooks:
      - id: blacken-docs
        additional_dependencies: [black]

  # isort - import sorting
  - repo: https://github.com/pycqa/isort
    rev: 5.13.2
    hooks:
      - id: isort

  # autoflake - remove unused imports and variables
  - repo: https://github.com/PyCQA/autoflake
    rev: v2.3.1
    hooks:
      - id: autoflake
        args:
          - --in-place
          - --remove-all-unused-imports
          - --remove-unused-variables
          - --remove-duplicate-keys
          - --ignore-init-module-imports

  # flake8 - linting
  - repo: https://github.com/pycqa/flake8
    rev: 7.1.1
    hooks:
      - id: flake8
        args: ['--max-line-length=120', '--extend-ignore=E203,W503,B008']
        additional_dependencies: [flake8-bugbear]

  # mypy - type checking
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.13.0
    hooks:
      - id: mypy
        exclude: ^tests/.*$
        args: [--config-file=pyproject.toml]
        additional_dependencies:
          - pydantic>=2.5.0
          - pydantic-settings>=2.1.0

  # pytest - run tests
  - repo: local
    hooks:
      - id: pytest
        name: pytest
        entry: pytest -v --tb=short
        language: python
        pass_filenames: false
        always_run: true
        additional_dependencies: [".[dev]"]

  # Frontend (if applicable)
  - repo: local
    hooks:
      - id: frontend-lint
        name: Frontend ESLint
        entry: bash -c 'cd frontend && [ -d node_modules ] || npm install && npm run lint'
        language: system
        files: ^frontend/.*\.(ts|tsx)$
        pass_filenames: false
        always_run: false

      - id: frontend-test
        name: Frontend Tests
        entry: bash -c 'cd frontend && [ -d node_modules ] || npm install && npm run test:run'
        language: system
        files: ^frontend/.*\.(ts|tsx)$
        pass_filenames: false
        always_run: false
```

## Step 3: Update to Latest Versions

Run autoupdate to get latest versions of all hooks:

```bash
# Update all hooks to latest versions
pre-commit autoupdate

# Verify updates worked
pre-commit run --all-files
```

This automatically updates all `rev` values in `.pre-commit-config.yaml` to the latest stable releases.

## Step 4: Configure pyproject.toml

Add tool configurations:

```toml
[tool.black]
line-length = 120
target-version = ['py312']

[tool.isort]
profile = "black"
line_length = 120

[tool.mypy]
python_version = "3.12"
strict = true
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true
disallow_incomplete_defs = true

[[tool.mypy.overrides]]
module = "tests.*"
disallow_untyped_defs = false

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = "test_*.py"
python_functions = "test_*"
addopts = "-v --tb=short"

[tool.autoflake]
remove-all-unused-imports = true
remove-unused-variables = true
```

## Step 5: Set Up CLAUDE.md Structure

Create project documentation following documentation-engineer patterns:

**Root CLAUDE.md:**
```markdown
# Project Name

One-line description.

## Commands

pip install -e ".[dev]"
pre-commit install
pytest
pre-commit run --all-files

## Structure

src/project_name/     # Main package
tests/                # Test files
docs/                 # Documentation

## Patterns

- All models in src/project_name/models/
- All API handlers in src/project_name/api/
- Config via pydantic-settings in src/project_name/config.py

## Pre-commit

Runs automatically on commit:
- black, isort (formatting)
- flake8, mypy (linting, types)
- pytest (tests)

Run manually: pre-commit run --all-files
```

**Root ARCHITECTURE.md:**
```markdown
# Architecture

## Components

**Models** (src/project_name/models/)
Pydantic models for data validation.

**Services** (src/project_name/services/)
Business logic. No framework dependencies.

**API** (src/project_name/api/)
HTTP handlers if applicable.

## Data Flow

1. Request enters via API handler
2. Handler validates with Pydantic model
3. Handler calls service
4. Service returns result or raises error
5. Handler returns response
```

**Per-directory CLAUDE.md** for each major directory.

## Step 6: Install and Verify

```bash
# Install pre-commit
pip install pre-commit
pre-commit install

# Run all hooks to verify
pre-commit run --all-files

# Fix any issues
pre-commit run --all-files  # Run again after fixes
```

## Step 7: Customize for Project

Adjust based on project needs:

**Add dependencies to mypy hook:**
```yaml
additional_dependencies:
  - pydantic>=2.5.0
  - fastapi>=0.109.0
  - sqlalchemy>=2.0.0
  # Add your project's typed dependencies
```

**Remove frontend hooks** if no frontend:
```yaml
# Delete the frontend-lint and frontend-test hooks
```

**Adjust Python version:**
- Update `language_version` in black hook
- Update `target-version` in pyproject.toml
- Update `python_version` in mypy config

## Checklist

Before finishing:

- [ ] pyscaffold structure created
- [ ] .pre-commit-config.yaml with all hooks
- [ ] pre-commit autoupdate ran (latest versions)
- [ ] pyproject.toml has tool configs
- [ ] CLAUDE.md at root (for documentation-engineer)
- [ ] ARCHITECTURE.md at root
- [ ] pre-commit install ran
- [ ] pre-commit run --all-files passes
- [ ] .gitignore includes standard Python entries

## Calibration

**Search for latest versions.** Don't use hardcoded versions from this doc - they go stale. Always verify current versions.

**Customize mypy dependencies.** The additional_dependencies must include your project's typed libraries or mypy will fail.

**Test the hooks.** Run `pre-commit run --all-files` before committing the config.

**Stay in your lane.** Don't cover:
- What the project should do (product-manager)
- Library choices (framework-engineer)
- Writing the actual code (that's implementation)
