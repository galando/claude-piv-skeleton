---
description: "Create project constitution with core principles"
argument-hint: ""
---

# Constitution: Define Project Principles

**Goal:** Create a project constitution that guides all AI-assisted development decisions.

## Execution

1. Check if `.claude/memory/constitution.md` exists
2. If exists, read it and summarize current principles
3. If not, offer to create from template

## What is a Constitution?

A **constitution** is a one-time document that defines:
- **Project purpose**: What this project is and why it exists
- **Core principles**: Quality standards, testing requirements, code style
- **Technology stack**: Frameworks, languages, databases
- **Technical constraints**: What to use and what to avoid
- **Development guidelines**: Git workflow, code review, testing
- **Security requirements**: Security standards and practices
- **Performance requirements**: Performance targets

## Why Create a Constitution?

**Benefits:**
- Guides all AI decisions for consistent output
- Single source of truth for project choices
- Reduces ambiguity in technical decisions
- Onboards new developers quickly
- Prevents technical debt through clear principles

## Constitution vs. Spec Artifacts

| Aspect | Constitution | Spec/Plan/Tasks |
|--------|--------------|-----------------|
| Scope | Entire project | Single feature |
| Frequency | Once per project | Per feature |
| Changes | Rarely | Often |
| Content | Principles, stack, guidelines | Requirements, approach, steps |

## Location

Constitution is stored at: `.claude/memory/constitution.md`

This file is read automatically during:
- `/piv_loop:plan-feature` - Planning phase reads constitution
- `/piv_loop:prime` - Prime checks for constitution existence

## Template

Use this structure when creating constitution:

```markdown
# Project Constitution

**Last Updated:** {DATE}
**Project:** {PROJECT_NAME}

## Purpose

{What this project is and why it exists}

## Core Principles

1. **Quality First**
   - {Your quality standards}

2. **Testing**
   - TDD is mandatory
   - 80%+ test coverage
   - Integration tests for critical paths

3. **Code Style**
   - {Your style preferences}

## Technology Stack

**Backend:**
- Framework: {e.g., Spring Boot 3.2}
- Language: {e.g., Java 21}
- Database: {e.g., PostgreSQL 16}

**Frontend:**
- Framework: {e.g., React 19}
- Language: {e.g., TypeScript}
- Styling: {e.g., TailwindCSS}

## Technical Constraints

**Use:**
- {Technology choices to use}

**Avoid:**
- {Anti-patterns to avoid}

## Development Guidelines

**Git Workflow:**
- Branch from: `main`
- Commit style: Conventional Commits
- PR required: Yes

**Code Review:**
- Required for: All changes
- Reviewers: {Who reviews}

**Testing:**
- Required before: All commits
- Test command: {e.g., `mvn test`}

## Security Requirements

- {Security requirements}

## Performance Requirements

- {Performance targets}
```

**Note:** Replace `{PLACEHOLDERS}` with your project-specific values.

## Usage

**Create constitution:**
```
/piv_loop:constitution
```

**Update constitution:**
Edit `.claude/memory/constitution.md` directly
