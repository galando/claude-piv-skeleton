# Using PIV Spec-Kit with GitHub Copilot

> **Auto-loading**: Copilot automatically reads `.github/copilot-instructions.md`

---

## Installation

```bash
curl -s https://raw.githubusercontent.com/galando/piv-speckit/main/scripts/piv.sh | bash
```

**What this creates:**

| File/Directory | Purpose |
|----------------|---------|
| `AGENTS.md` | Core PIV methodology (reference with `@file:AGENTS.md`) |
| `.github/copilot-instructions.md` | **Auto-loaded** by Copilot automatically |
| `.specs/` | Directory for feature specifications |
| `.specs/.templates/` | Reusable templates |
| `constitution.md` | Project principles (you'll create this) |

---

## How Copilot Loads Context

**Good news**: Copilot automatically reads `.github/copilot-instructions.md`!

No special setup needed. The PIV rules are always active.

### For Additional Context

Use these in Copilot Chat when needed:
- **`@workspace`** - Include entire workspace in context
- **`@file:AGENTS.md`** - Reference specific files
- **`@file:constitution.md`** - Reference your project constitution

---

## The PIV Workflow

| Phase | What to Say in Copilot Chat |
|-------|----------------------------|
| **Constitution** | "@workspace Create a project constitution" |
| **Prime** | "@workspace Analyze this codebase" |
| **Plan** | "@workspace Plan a feature for X" |
| **Execute** | "@workspace Implement using TDD - test first" |
| **Validate** | "@workspace Review code for issues" |

---

## Step-by-Step Guide

### 1. Constitution (One-Time)

Create project principles. Do this once.

**In Copilot Chat:**

```
@workspace Create a project constitution and save it to constitution.md

Include:
- Project name and purpose
- Tech stack
- TDD requirements (mandatory, 80%+ coverage)
- Code style preferences
- Git workflow (conventional commits)
```

---

### 2. Prime (Start of Each Session)

Load codebase context before changes.

**In Copilot Chat:**

```
@workspace Analyze this codebase. Read constitution.md and AGENTS.md.
Tell me:
- Project structure
- Frameworks and patterns used
- How tests are organized
```

---

### 3. Plan (For Complex Features)

Create specs before coding.

**In Copilot Chat:**

```
@workspace Plan a feature for "password reset".

Create in .specs/password-reset/:
- spec.md: User stories and requirements
- plan.md: Technical architecture
- tasks.md: Implementation checklist
- quickstart.md: TL;DR

Reference .specs/.templates/ for format.
```

---

### 4. Execute (TDD is Mandatory)

Implement with strict TDD: RED → GREEN → REFACTOR.

**In Copilot Chat:**

```
@workspace Implement the tasks in .specs/password-reset/tasks.md

CRITICAL: Use strict TDD
1. Write failing test FIRST - show it to me
2. Run test - confirm it fails
3. Write minimal code to pass
4. Refactor while tests pass

Start with Task 1.1
```

**If Copilot writes code first:**

```
STOP. You skipped TDD.
Delete that code and write the test first.
Show me the failing test before any implementation.
```

---

### 5. Validate

Verify quality after implementation.

**In Copilot Chat:**

```
@workspace Validate the password-reset feature:
- Are all tests passing?
- Is coverage 80%+?
- Any security issues?
- Does code follow existing patterns?
```

---

### 6. Commit

```
@workspace Create a conventional commit message for these changes.
Format: type(scope): description
```

---

## Copilot-Specific Features

### Copilot Chat Commands

| Command | Use For |
|---------|---------|
| `/explain` | Understand existing code |
| `/tests` | Generate tests |
| `/fix` | Fix bugs or issues |
| `/doc` | Generate documentation |

**Examples:**

```
/explain @workspace How does authentication work?

/tests Generate tests for UserService using Given-When-Then pattern

/fix @workspace Fix this following security guidelines in AGENTS.md
```

### Inline Suggestions

Copilot's inline suggestions work better with comments:

```java
// TDD: Test should verify user creation
// Test class: UserServiceTest
// Test method: shouldCreateUserWithValidData
public User createUser(CreateUserRequest request) {
    // Copilot will suggest implementation based on comments
}
```

### Reference Specific Files

```
@workspace @file:AGENTS.md @file:constitution.md
Implement this feature following our guidelines.
```

---

## Directory Structure

```
your-project/
├── AGENTS.md                    # ← Reference with @workspace
├── constitution.md              # ← Project principles
└── .specs/
    ├── .templates/              # ← Reusable templates
    │   ├── spec-template.md
    │   ├── plan-template.md
    │   └── tasks-template.md
    └── password-reset/          # ← Feature specs
        ├── spec.md
        ├── plan.md
        ├── tasks.md
        └── quickstart.md
```

---

## Troubleshooting

### Copilot Doesn't Follow AGENTS.md

1. Always use `@workspace` prefix
2. Reference files explicitly:
   ```
   @workspace @file:AGENTS.md Follow these guidelines
   ```
3. Verify workspace context is enabled in settings

### Copilot Skips TDD

Be explicit and firm:

```
STOP. This project requires strict TDD.

Rules:
1. NO implementation code before tests
2. Show me the failing test first
3. Wait for my approval before writing implementation

Delete what you wrote and start with the test.
```

### Copilot Doesn't See .github/copilot-instructions.md

1. Verify file exists: `ls .github/copilot-instructions.md`
2. Restart VS Code / JetBrains
3. Check Copilot is logged in

### Copilot Doesn't See Other Files

1. Make sure you're using Copilot Chat (not just inline)
2. Use `@workspace` to enable file access
3. Reference files: `@file:path/to/file.md`

---

## Copilot Chat vs Inline Copilot

| Feature | Copilot Chat | Inline Copilot |
|---------|--------------|----------------|
| Auto-loads .github/copilot-instructions.md | ✅ Yes | ✅ Yes |
| Read AGENTS.md | ✅ With @workspace | ⚠️ Limited |
| Multi-file context | ✅ Yes | ⚠️ Current file only |
| TDD workflow | ✅ Can guide | ❌ Just autocomplete |
| Best for | Planning, reviews | Quick completions |

**Recommendation:** Use Copilot Chat for PIV workflow, inline for quick edits.

---

## Quick Reference

```
┌─────────────────────────────────────────────────────────┐
│  COPILOT + PIV QUICK REFERENCE                          │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Auto-loaded: .github/copilot-instructions.md           │
│                                                         │
│  For more context: @workspace @file:AGENTS.md            │
│                                                         │
│  CONSTITUTION (once)                                    │
│  "Create constitution in constitution.md"               │
│                                                         │
│  PRIME (each session)                                   │
│  "@workspace Analyze codebase"                          │
│                                                         │
│  PLAN (complex features)                                │
│  "@workspace Plan feature X in .specs/X/"               │
│                                                         │
│  EXECUTE (TDD!)                                         │
│  "Implement tasks.md - FAILING TEST first"              │
│                                                         │
│  VALIDATE                                               │
│  "@workspace Review code, check coverage"               │
│                                                         │
│  TDD CYCLE: 🔴 RED → 🟢 GREEN → 🔵 REFACTOR             │
│                                                         │
│  SLASH COMMANDS:                                        │
│  /explain  /tests  /fix  /doc                           │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

*PIV Spec-Kit - [github.com/galando/piv-speckit](https://github.com/galando/piv-speckit)*
