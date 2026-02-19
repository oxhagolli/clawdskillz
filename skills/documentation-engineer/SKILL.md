---
name: documentation-engineer
description: Use this skill after design decisions or major code changes to update documentation. Creates per-directory CLAUDE.md files and maintains top-level CLAUDE.md and ARCHITECTURE.md. Trigger after completing features, refactoring, or architectural changes.
---

# Documentation Engineer Protocol

Write docs that help Claude and humans understand code fast. Be brief. Use examples. Skip the fluff.

## Core Principles

- **Concise over complete** - One clear sentence beats three vague ones
- **Examples over explanations** - Show, don't tell
- **Simple words** - "use" not "utilize", "get" not "retrieve"
- **No decoration** - No ASCII art, no fancy formatting, no emoji
- **Update, don't append** - Keep docs current, remove outdated info

## File Structure

```
project/
├── CLAUDE.md           # Project overview, key patterns, gotchas
├── ARCHITECTURE.md     # System design, data flow, major components
├── src/
│   ├── CLAUDE.md       # This directory's purpose and patterns
│   ├── auth/
│   │   └── CLAUDE.md   # Auth-specific context
│   └── api/
│       └── CLAUDE.md   # API-specific context
```

## Top-Level CLAUDE.md

What to include:
- Project purpose (1-2 sentences)
- How to run/build/test (commands only)
- Key patterns used throughout
- Common gotchas
- Important conventions

Example:
```markdown
# Project

Order management API for retail clients.

## Commands

npm install
npm run dev      # localhost:3000
npm test         # run all tests

## Patterns

- All handlers in src/handlers/, one file per endpoint
- Validation via zod schemas in src/schemas/
- Errors thrown as HttpError(code, message)

## Gotchas

- Tests require local postgres running
- Auth tokens expire after 1 hour in dev
```

## Top-Level ARCHITECTURE.md

What to include:
- High-level system diagram (text, not ASCII art)
- Major components and their responsibilities
- Data flow for key operations
- External dependencies

Example:
```markdown
# Architecture

## Components

**API Server** (src/api/)
Handles HTTP requests. Validates input, calls services, returns responses.

**Services** (src/services/)
Business logic. No HTTP awareness. Returns plain objects or throws errors.

**Database** (src/db/)
Postgres via pg-promise. All queries in repository files.

**Queue** (src/queue/)
Redis-backed job queue for async tasks. Workers in src/workers/.

## Data Flow: Create Order

1. POST /orders hits OrderHandler
2. Handler validates via OrderSchema
3. Handler calls OrderService.create()
4. Service checks inventory, calculates totals
5. Service calls OrderRepository.insert()
6. Service queues confirmation email
7. Handler returns 201 with order ID
```

## Per-Directory CLAUDE.md

Keep it short. Cover:
- What this directory contains
- Key files and their purposes
- Patterns specific to this directory
- Example usage if not obvious

Example:
```markdown
# src/auth/

Handles authentication and authorization.

## Files

- middleware.ts - Express middleware, attaches user to req
- tokens.ts - JWT creation and verification
- permissions.ts - Role-based access checks

## Usage

// In a route
app.get('/admin', authMiddleware, requireRole('admin'), handler)

// Check permission manually
if (canAccess(user, 'orders:write')) { ... }
```

## Writing Style

**Do:**
```markdown
Returns user object or null if not found.
```

**Don't:**
```markdown
This function is responsible for retrieving the user object from the
database. In cases where the user cannot be found, it will return a
null value to indicate the absence of the requested user.
```

**Do:**
```markdown
## Usage

const user = await getUser(id)
if (!user) return notFound()
```

**Don't:**
```markdown
## Usage Instructions

In order to properly utilize this function, you will need to first
ensure that you have a valid user ID. Then, you can call the function
as demonstrated in the following comprehensive example...
```

## When to Update

Update docs after:
- Adding new directories
- Changing key patterns
- Adding/removing major components
- Changing build/run commands
- Discovering gotchas worth noting

Remove from docs when:
- Feature/pattern no longer exists
- Command changed
- File was deleted/renamed

## Checklist

Before finishing:
- [ ] Is every sentence necessary?
- [ ] Could this be shorter?
- [ ] Is there an example for anything non-obvious?
- [ ] Did I remove outdated info?
- [ ] Would a new dev understand this in 30 seconds?

## Calibration

**Stay in your lane.** Don't cover:
- Code implementation or review (brevity-engineer)
- Linting/type checking (linting-engineer)
- What features to build (product-manager)
