# Command: /core_piv_loop:execute

**Phase: Implement**
**Purpose: Execute implementation plan step-by-step**

---

## Command Definition

This command executes an implementation plan created by `/core_piv_loop:plan-feature`, following the steps systematically and creating/modifying files as specified.

## Usage

```
/core_piv_loop:execute [plan-name]
```

If no plan name is specified, uses the most recent plan.

## What It Does

### 1. Load Plan
- Reads plan from `.claude/agents/plans/{plan-name}.md`
- Parses implementation steps
- Identifies files to create/modify
- Reviews verification criteria

### 2. Execute Steps
- Follows implementation steps in order
- Creates files as specified
- Modifies existing files as planned
- Follows all applicable rules
- Writes tests alongside code

### 3. Track Progress
- Marks steps as completed
- Tracks files created/modified
- Notes any deviations from plan
- Records decisions made

### 4. Trigger Validation (Automatic)
- Automatically runs `/validation:validate`
- **CRITICAL**: Verification includes compilation check
- **CRITICAL**: Verification includes test execution
- Runs all validation levels
- Generates validation report
- Generates execution report

**⚠️ Important:** The automatic validation **MUST verify compilation** before proceeding to code review. If compilation fails, the execution is marked as FAILED and code review is NOT run.

## When to Use

- **After planning**: Execute a plan created by `/core_piv_loop:plan-feature`
- **From plan name**: Execute a specific plan
- **Latest plan**: Execute most recent plan (default)

## Expected Output

### During Execution

```
🔄 Executing plan: [Feature name]

Step 1/5: [Step description]
  ✅ Creating: path/to/file.ext
  ✅ Modifying: path/to/other.ext

Step 2/5: [Step description]
  ✅ Creating: path/to/another.ext
  ...

All steps completed.

🔄 Running automatic validation...
```

### Execution Report Artifact
File: `.claude/agents/reports/execution-report-{feature-name}.md`

```markdown
# Execution Report: [Feature name]

**Executed**: [Timestamp]
**Plan**: [Plan name]
**Status**: ✅ Success / ❌ Failed

## Summary
- Steps completed: [X]/[Y]
- Files created: [N]
- Files modified: [N]
- Tests added: [N]

## Implementation Details

### Completed Steps
1. ✅ **Step 1**: [Description]
   - Created: `path/to/file.ext`
   - Modified: `path/to/other.ext`
   - Notes: [Any notes]

2. ✅ **Step 2**: [Description]
   - Created: `path/to/another.ext`
   - Notes: [Any notes]

[Continue for all steps]

### Files Created
| File | Purpose |
|------|---------|
| `path/to/file` | [Description] |

### Files Modified
| File | Changes |
|------|---------|
| `path/to/file` | [Description of changes] |

## Validation Results
[Link to validation report]

## Deviations from Plan
[Note any deviations from the original plan]

## Notes
[Additional implementation notes, decisions made, etc.]
```

### Validation Report (Automatic)
File: `.claude/agents/reports/validation-report-{timestamp}.md`

See `/validation:validate` command documentation.

### Final Message

```
✅ Execution complete

Plan: [Feature name]
Status: ✅ Success

Files created: [N]
Files modified: [N]
Tests added: [N]

Validation: ✅ All checks passed
Reports:
- Execution: .claude/agents/reports/execution-report-{feature-name}.md
- Validation: .claude/agents/reports/validation-report-{timestamp}.md

Ready for review. Use /validation:code-review for detailed review.
```

## Implementation Notes

For Claude Code Implementation:

1. **Load** plan from file (or most recent)
2. **Parse** implementation steps
3. **Execute** steps in order
4. **For each step**:
   - Read relevant files
   - Follow all applicable rules
   - Create/modify files
   - Write tests
   - Mark step complete
5. **Track** all changes
6. **Trigger** `/validation:validate` (automatic)
7. **Generate** execution report
8. **Present** results to user

## Execution Behavior

### Following Rules
During execution, Claude must:
- ✅ **FOLLOW** all universal rules from `.claude/rules/`
- ✅ **FOLLOW** all technology-specific rules
- ✅ **MATCH** existing code style
- ✅ **REUSE** existing patterns
- ✅ **WRITE** tests for all new code
- ✅ **DOCUMENT** complex logic

### Error Handling
If execution fails:
1. **STOP** at current step
2. **REPORT** error clearly
3. **SAVE** progress made
4. **SUGGEST** fix
5. **ALLOW** resume after fix

### Plan Deviations
If Claude needs to deviate from plan:
1. **EXPLAIN** why deviation is needed
2. **PROPOSE** alternative
3. **CONFIRM** with user
4. **UPDATE** plan if approved
5. **DOCUMENT** deviation in execution report

## Artifacts Created

- **Code files**: As specified in plan
- **Test files**: For all new code
- `.claude/agents/reports/execution-report-{feature-name}.md` - Execution summary
- `.claude/agents/reports/validation-report-{timestamp}.md` - Validation results (automatic)

## Progress Tracking

During execution, maintain progress:

```
✅ Step 1: Completed
✅ Step 2: Completed
🔄 Step 3: In progress
⏳ Step 4: Pending
⏳ Step 5: Pending
```

If execution is interrupted:
- Save current progress
- Document completed steps
- Allow resume from last step

## Quality Checks

Before triggering validation:

- [ ] All steps completed
- [ ] All files created/modified as planned
- [ ] Code follows all rules
- [ ] Tests written for new code
- [ ] No compilation errors
- [ ] No obvious bugs

## Next Steps

After execution:

1. **REVIEW** execution report
2. **REVIEW** validation report
3. **RUN** manual tests if needed
4. **CREATE** commit with changes
5. **OPTIONAL** Run `/validation:code-review` for detailed review

## Related Commands

- `/core_piv_loop:prime` - Load context
- `/core_piv_loop:plan-feature` - Create plan (before execute)
- `/validation:validate` - Run validation (automatic after execute)
- `/validation:code-review` - Detailed code review (optional)
- `/validation:execution-report` - View execution report

---

**Execution follows the plan systematically. Deviate only when necessary and document why.**

**Validation runs automatically to ensure quality.**
