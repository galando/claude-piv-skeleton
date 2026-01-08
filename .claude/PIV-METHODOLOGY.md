# PIV Methodology

**Prime - Implement - Validate**

A development methodology designed specifically for AI-assisted software development with Claude Code.

---

## Overview

PIV is a three-phase methodology that ensures Claude Code has proper context, implements features systematically, and validates work automatically. It's designed to minimize misunderstandings, reduce rework, and maintain code quality.

### The Three Phases

```
┌─────────────────────────────────────────────────────────────┐
│                      PIV LOOP                               │
│                                                             │
│  ┌──────────┐    ┌──────────────┐    ┌──────────┐        │
│  │  PRIME   │───▶│  IMPLEMENT   │───▶│ VALIDATE │        │
│  │          │    │              │    │          │        │
│  │ Context  │    │  Plan        │    │  Auto    │        │
│  │ Loading  │    │  Execute     │    │  Tests   │        │
│  └──────────┘    └──────────────┘    └────┬─────┘        │
│                                             │              │
│                                             │              │
│                                       ┌─────▼─────┐       │
│                                       │   Report  │       │
│                                       │ & Refine  │       │
│                                       └───────────┘       │
└─────────────────────────────────────────────────────────────┘
```

---

## Phase 1: Prime

**Goal: Load comprehensive codebase context**

### What Happens
- Claude Code analyzes the entire codebase structure
- Identifies key patterns, conventions, and architecture
- Loads project-specific rules and guidelines
- Creates context artifacts for reference

### When to Use
- **Start of session**: Run before any work
- **Context switch**: When switching between features
- **After major changes**: When significant changes have been made
- **New feature**: Before implementing new work

### Commands
```
/core_piv_loop:prime
```

### Artifacts Created
- `.claude/agents/context/prime-context.md` - Comprehensive context document

### What Gets Analyzed
- Project structure and organization
- Technology stack and frameworks
- Coding conventions and patterns
- Architecture and design patterns
- Dependencies and integrations
- Testing approach and coverage
- Documentation and guides

### Success Criteria
- [ ] Claude can answer questions about codebase structure
- [ ] Claude understands technology choices
- [ ] Claude knows coding conventions
- [ ] Claude identifies key files and their purposes
- [ ] Context artifact is created and saved

---

## Phase 2: Implement

**Goal: Plan and execute features systematically**

The Implement phase combines planning AND execution into a unified flow.

### What Happens

#### Step 1: Plan (`/core_piv_loop:plan-feature`)
- Feature requirements are gathered and clarified
- Technical approach is designed
- Architecture decisions are documented
- Implementation steps are broken down
- Files to create/modify are listed
- Verification criteria are defined

#### Step 2: Execute (`/core_piv_loop:execute`)
- Implementation follows the plan step-by-step
- Code is written following all applicable rules
- Changes are made incrementally
- Progress is tracked against the plan

### When to Use

#### Plan Feature
- **Complex features**: Multi-file or architectural changes
- **New functionality**: Features that require design decisions
- **Refactoring**: Changes that affect multiple components
- **Integration**: Adding new dependencies or services

#### Execute
- **After planning**: Always execute from a plan (for complex work)
- **Simple changes**: Can execute directly for minor tweaks
- **Bug fixes**: Execute based on RCA or issue description

### Commands

#### Planning
```
/core_piv_loop:plan-feature "Feature description"
```

#### Execution
```
/core_piv_loop:execute [plan-name]
```

### Artifacts Created

#### Plan Artifact
`.claude/agents/plans/{feature-name}.md`

**Plan Structure:**
```markdown
# Feature: [Feature Name]

## Context
[Context from Prime phase about relevant parts of codebase]

## Requirements
[Detailed functional and non-functional requirements]

## Technical Approach
[Architecture decisions, design patterns, technology choices]

## Implementation Steps
1. [Step 1]
2. [Step 2]
3. [Step 3]
...

## Files to Create
- `path/to/file1.ext` - [Purpose]
- `path/to/file2.ext` - [Purpose]

## Files to Modify
- `path/to/existing.ext` - [Changes needed]

## Testing Strategy
- Unit tests needed: [List]
- Integration tests needed: [List]
- Manual verification steps: [List]

## Verification Criteria
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Dependencies
[What needs to be done first]

## Notes
[Additional considerations, edge cases, risks]
```

#### Execution Report
`.claude/agents/reports/execution-report-{feature-name}.md`

### Success Criteria

#### Planning
- [ ] All requirements are clearly defined
- [ ] Technical approach is justified
- [ ] Implementation steps are actionable
- [ ] Files are listed with purposes
- [ ] Testing strategy is comprehensive
- [ ] Verification criteria are measurable
- [ ] Dependencies are identified

#### Execution
- [ ] All implementation steps completed
- [ ] Code follows all applicable rules
- [ ] Files created/modified as planned
- [ ] Execution report generated

---

## Phase 3: Validate

**Goal: Automatically verify implementation quality**

Validation happens **automatically** after execution to ensure quality without manual intervention.

### What Happens Automatically

The `/validation:validate` command runs a comprehensive validation pipeline:

#### Level 0: Environment Safety
- Verify environment (local vs production)
- Check for safety guards
- Confirm destructive operations are disabled

#### Level 1: Compilation
- Code compiles without errors
- No type errors
- No syntax errors

#### Level 2: Unit Tests
- All new unit tests pass
- Existing tests still pass
- Test suite executes successfully

#### Level 3: Integration Tests
- Integration tests pass (if applicable)
- API contracts are maintained
- Database migrations work

#### Level 4: Code Quality
- Linting passes
- Code formatting checks
- No security vulnerabilities

#### Level 5: Coverage
- Test coverage meets threshold (typically 80%+)
- Critical paths are covered
- Edge cases are tested

#### Level 6: Build
- Full build succeeds
- Assets are generated correctly
- No build warnings

### Commands

#### Automatic Validation
```
/validation:validate
```

This command is **automatically invoked** after `/core_piv_loop:execute` completes.

#### Code Review (Optional)
```
/validation:code-review
```
Technical code review on changed files.

#### Fix Issues (Optional)
```
/validation:code-review-fix
```
Automatically fix issues found in code review.

#### Execution Report
```
/validation:execution-report
```
Generate comprehensive report after implementing a feature.

#### System Review (Optional)
```
/validation:system-review
```
Analyze implementation vs plan for process improvements.

### Artifacts Created

#### Validation Report
`.claude/agents/reports/validation-report-{timestamp}.md`

Includes:
- Environment verification status
- Compilation results
- Test results (unit + integration)
- Code quality metrics
- Coverage statistics
- Build status
- Overall validation result (PASS/FAIL)

#### Code Review Report
`.claude/agents/reviews/code-review-{timestamp}.md`

### Automatic Flow

The validation is designed to run **automatically** as part of the execution flow:

```
/core_piv_loop:execute
  │
  ├─▶ Implement feature
  │
  └─▶ /validation:validate (AUTOMATIC)
      │
      ├─▶ Run all validation levels
      │
      ├─▶ Generate validation report
      │
      └─▶ /validation:execution-report (AUTOMATIC)
          │
          └─▶ Generate execution report
```

### Success Criteria
- [ ] All validation levels pass
- [ ] Code follows all rules
- [ ] Tests are comprehensive
- [ ] No regressions introduced
- [ ] Validation report is generated
- [ ] Execution report is generated

---

## Complete PIV Workflow

### Standard Feature Development

```
1. /core_piv_loop:prime
   │
   ├─▶ Load codebase context
   ├─▶ Create context artifact
   │
2. /core_piv_loop:plan-feature "Add user authentication"
   │
   ├─▶ Analyze requirements
   ├─▶ Design approach
   ├─▶ Create plan artifact
   │
3. /core_piv_loop:execute
   │
   ├─▶ Implement from plan
   ├─▶ Follow all rules
   ├─▶ Track progress
   │
   └─▶ AUTOMATIC: /validation:validate
       ├─▶ Run all validation levels
       ├─▶ Generate validation report
       │
       └─▶ AUTOMATIC: /validation:execution-report
           └─▶ Generate execution report
```

### Quick Bug Fix

```
1. /github_bug_fix:rca
   │
   ├─▶ Create RCA document
   │
2. /github_bug_fix:implement-fix
   │
   ├─▶ Implement fix
   │
   └─▶ AUTOMATIC: /validation:validate
       └─▶ Validate fix works
```

---

## When to Use Each Phase

### Decision Tree

```
Start
  │
  ├─ New session or context switch?
  │   └─ YES → Run PRIME
  │
  ├─ Type of work?
  │   ├─ Simple bug fix or small tweak
  │   │   └─ Use RCA or implement directly
  │   │       └─ VALIDATE runs automatically
  │   │
  │   ├─ Complex feature or architectural change
  │   │   └─ PLAN → EXECUTE
  │   │       └─ VALIDATE runs automatically
  │   │
  │   └─ Uncertain about complexity?
  │       └─ When in doubt, PLAN it
  │
  └─ After implementation
      └─ VALIDATE runs automatically
          └─ Review reports
```

### Guidelines

| Scenario | Approach |
|----------|----------|
| New session | Always Prime |
| Context switch | Prime again |
| Simple typo fix | Implement directly → Auto validate |
| Add new field | Can skip planning → Implement → Auto validate |
| New feature endpoint | Plan → Execute → Auto validate |
| Refactor component | Plan → Execute → Auto validate |
| Add new dependency | Plan → Execute → Auto validate |
| Database migration | Always plan → Execute → Auto validate |
| Architecture change | Always plan → Execute → Auto validate |

---

## Best Practices

### Prime Phase
- **Be thorough**: Better to over-analyze than under-analyze
- **Save context**: Always save the context artifact
- **Update regularly**: Re-prime after major changes
- **Ask questions**: Clarify ambiguities during prime

### Implement Phase (Planning)
- **Think first**: Don't rush to implementation
- **Be specific**: Vague plans lead to vague implementations
- **Consider alternatives**: Document why you chose this approach
- **Identify risks**: Think about what could go wrong
- **List dependencies**: Know what needs to be done first

### Implement Phase (Execution)
- **Follow the plan**: Don't deviate without updating the plan
- **Code incrementally**: Make small, focused changes
- **Follow rules**: Respect all applicable rules
- **Track progress**: Mark steps as complete

### Validate Phase
- **Trust automation**: Let automatic validation do its job
- **Review reports**: Read validation and execution reports
- **Fix issues**: Address validation failures promptly
- **Don't skip**: Never skip automatic validation

---

## Anti-Patterns to Avoid

### ❌ Skipping Prime
**Problem**: Making changes without understanding context
**Consequence**: Breaking existing patterns, introducing inconsistencies
**Solution**: Always Prime at start of session

### ❌ Over-Planning
**Problem**: Spending too much time planning simple changes
**Consequence**: Diminishing returns, lost time
**Solution**: Use judgment - plan complex work, skip simple fixes

### ❌ Under-Planning
**Problem**: Not planning complex changes
**Consequence**: Rework, architectural issues, missed edge cases
**Solution**: When in doubt, plan it out

### ❌ Ignoring the Plan
**Problem**: Creating a plan then not following it
**Consequence**: Plan becomes useless, lessons lost
**Solution**: Update plan if needed, or don't create one

### ❌ Manual Validation
**Problem**: Running tests manually instead of using automatic validation
**Consequence**: Inconsistent validation, missed checks
**Solution**: Always use `/validation:validate` (runs automatically)

### ❌ Ignoring Reports
**Problem**: Not reading validation or execution reports
**Consequence**: Missed insights, repeated mistakes
**Solution**: Always review generated reports

---

## Automatic Validation Benefits

### Why Automatic?

1. **Consistency**: Every implementation gets the same thorough validation
2. **No Forgotten Steps**: All validation levels run every time
3. **Fast Feedback**: Issues caught immediately
4. **Quality Gates**: Can't proceed without passing validation
5. **Documentation**: Reports provide audit trail

### What Runs Automatically

After `/core_piv_loop:execute`:

```
1. /validation:validate (AUTOMATIC)
   ├─▶ Environment check
   ├─▶ Compilation
   ├─▶ Unit tests
   ├─▶ Integration tests
   ├─▶ Code quality
   ├─▶ Coverage check
   └─▶ Build verification

2. /validation:execution-report (AUTOMATIC)
   └─▶ Summary of what was done
```

### Manual vs Automatic

| Task | Manual | Automatic |
|------|--------|-----------|
| Prime | ✅ Required | - |
| Plan | ✅ Required (complex work) | - |
| Execute | ✅ Required | - |
| Validate | ❌ Don't run manually | ✅ Runs after execute |
| Code Review | ✅ Optional | - |
| Execution Report | ❌ Don't run manually | ✅ Runs after validate |

---

## Tips for Success

### For Effective Priming
- Start with broad questions: "How is this codebase organized?"
- Ask about patterns: "What are the key architectural patterns?"
- Understand conventions: "What coding style is used?"
- Identify rules: "What rules should I follow?"

### For Effective Planning
- State requirements clearly: "What exactly are we building?"
- Think about edge cases: "What could go wrong?"
- Consider performance: "Will this scale?"
- Plan testing: "How will we verify this works?"

### For Effective Execution
- Follow the plan step-by-step
- Respect all applicable rules
- Make incremental changes
- Test as you go (even though auto-validation will run)

### For Effective Validation
- **Don't disable auto-validation**: It's there for quality
- **Read the reports**: Understand what passed/failed
- **Fix failures promptly**: Don't accumulate technical debt
- **Track trends**: Watch for repeated validation issues

---

## Summary

PIV is a simple but powerful methodology:

1. **Prime** - Understand what you're working with
2. **Implement** - Plan and execute systematically
3. **Validate** - Automatic verification of quality

The methodology is lightweight enough for quick fixes but structured enough for complex features. The automatic validation ensures quality without manual intervention.

**Key Differentiator**: Validation happens **automatically** as part of the execution flow, not as a separate manual step. This ensures every implementation gets thoroughly validated without requiring developer intervention.

**Remember**: The goal is not to follow the process rigidly, but to use it to produce better code more efficiently. Let the methodology serve you, not the other way around.
