# Command: /core_piv_loop:prime

**Phase: Prime**
**Purpose: Load comprehensive codebase context**

---

## Command Definition

This command primes the workspace by loading and understanding the entire codebase.

## What It Does

1. **Analyzes Project Structure**
   - Identifies main directories and their purposes
   - Maps out file organization
   - Recognizes technology stack

2. **Identifies Patterns**
   - Architectural patterns used
   - Coding conventions
   - Design patterns
   - Integration patterns

3. **Loads Rules**
   - Universal rules from `.claude/rules/`
   - Technology-specific rules from `technologies/*/rules/`
   - Project-specific conventions

4. **Creates Context Artifact**
   - Saves comprehensive context to `.claude/agents/context/prime-context.md`
   - Includes key files reference
   - Documents architectural decisions
   - Lists active rules

## When to Use

- **Start of session**: Always run first
- **Context switch**: When switching between unrelated features
- **After major changes**: When architecture has changed significantly
- **New contributor**: When onboarding to the codebase

## Expected Output

### 1. Context Artifact
File: `.claude/agents/context/prime-context.md`

```markdown
# Prime Context - [Project Name]

**Generated**: [Timestamp]
**Session**: [Session ID]

## Project Overview
[Brief project description]

## Technology Stack
- **Backend**: [List technologies]
- **Frontend**: [List technologies]
- **Database**: [List technologies]
- **DevOps**: [List technologies]

## Architecture
[Architectural overview and patterns]

## Project Structure
```
[Directory tree with descriptions]
```

## Key Patterns
[List key patterns and conventions]

## Active Rules
- Universal rules: `.claude/rules/00-*.md` through `.claude/rules/40-*.md`
- Technology rules: [List active technology rule sets]

## Important Files
| File | Purpose |
|------|---------|
| `path/to/file` | [Description] |

## Development Workflow
[How to build, test, run the project]

## Environment
- Local setup: [How to run locally]
- Production: [How production works]
```

### 2. Confirmation Message
```
✅ Workspace primed successfully

Context saved to: .claude/agents/context/prime-context.md

Technology stack detected:
- Backend: [list]
- Frontend: [list]

Active rules loaded:
- 5 universal rules
- [X] technology-specific rules

Ready for feature work. Use /core_piv_loop:plan-feature to start planning.
```

## Implementation Notes

For Claude Code Implementation:

1. **Glob search** for main file types to understand stack
2. **Read key files** (package.json, pom.xml, requirements.txt, etc.)
3. **Grep** for patterns and imports
4. **Check** for existing `.claude/` configuration
5. **Load** rules from `.claude/rules/` and `technologies/*/rules/`
6. **Generate** comprehensive context artifact
7. **Report** findings to user

## Artifacts Created

- `.claude/agents/context/prime-context.md` - Primary context document

## Next Steps

After priming:
1. Review the generated context
2. Proceed to planning: `/core_piv_loop:plan-feature`
3. Or ask questions about the codebase

## Related Commands

- `/core_piv_loop:plan-feature` - Create implementation plan
- `/core_piv_loop:execute` - Execute from plan
- `/validation:validate` - Run validation (automatic after execute)

---

**This command is the foundation of all PIV work. Always run before making changes.**
