# Command: /validation:validate

**Phase: Validate**
**Purpose: Run comprehensive validation pipeline (automatic)**

---

## Command Definition

This command runs a comprehensive validation pipeline to ensure code quality. It is **automatically triggered** after `/core_piv_loop:execute` completes.

## Usage

```
/validation:validate
```

**Note**: This command runs automatically after `/core_piv_loop:execute`. Manual invocation is rarely needed.

## What It Does

Runs validation through multiple levels to ensure code quality:

### Level 0: Environment Safety
- Verifies environment (local vs production)
- Checks for safety guards
- Confirms destructive operations are disabled

### Level 1: Compilation
- Compiles code without errors
- Checks for type errors
- Checks for syntax errors

### Level 2: Unit Tests
- Runs all unit tests
- Ensures new tests pass
- Ensures existing tests still pass

### Level 3: Integration Tests
- Runs integration tests (if applicable)
- Checks API contracts
- Verifies database operations

### Level 4: Code Quality
- Runs linters
- Checks code formatting
- Scans for security vulnerabilities

### Level 5: Coverage
- Measures test coverage
- Ensures coverage meets threshold (80%+)
- Identifies untested code

### Level 6: Build
- Runs full build
- Generates assets
- Checks for build warnings

## Validation Levels

```
┌─────────────────────────────────────────────────────┐
│              VALIDATION PIPELINE                     │
├─────────────────────────────────────────────────────┤
│ Level 0: Environment Safety          [Required]     │
│ Level 1: Compilation                 [Required]     │
│ Level 2: Unit Tests                  [Required]     │
│ Level 3: Integration Tests           [Optional]     │
│ Level 4: Code Quality                [Required]     │
│ Level 5: Coverage                    [Required]     │
│ Level 6: Build                       [Required]     │
└─────────────────────────────────────────────────────┘
```

## Expected Output

### Validation Report
File: `.claude/agents/reports/validation-report-{timestamp}.md`

```markdown
# Validation Report

**Run**: [Timestamp]
**Status**: ✅ PASS / ❌ FAIL

## Summary
| Level | Status | Details |
|-------|--------|---------|
| Environment | ✅ | Local mode confirmed |
| Compilation | ✅ | No errors, 3 warnings |
| Unit Tests | ✅ | 42/42 passed |
| Integration | ✅ | 15/15 passed |
| Code Quality | ✅ | No issues |
| Coverage | ✅ | 85% (threshold: 80%) |
| Build | ✅ | Success |

## Level 0: Environment Safety
✅ **Environment**: Local development
✅ **Safety checks**: Enabled
✅ **Destructive ops**: Disabled

## Level 1: Compilation
✅ **Backend**: Compiled successfully
  - No compilation errors
  - 3 warnings (non-critical)

✅ **Frontend**: Compiled successfully
  - No type errors
  - No syntax errors

## Level 2: Unit Tests
✅ **Unit tests**: 42/42 passed
  - Backend: 28/28 passed
  - Frontend: 14/14 passed
  - Duration: 2.3s

## Level 3: Integration Tests
✅ **Integration tests**: 15/15 passed
  - API endpoints: 10/10 passed
  - Database: 5/5 passed
  - Duration: 5.1s

## Level 4: Code Quality
✅ **Linting**: No issues
  - ESLint: 0 errors, 0 warnings
  - Prettier: Formatted

✅ **Security**: No vulnerabilities
  - npm audit: 0 vulnerabilities
  - Dependency check: Passed

## Level 5: Coverage
✅ **Coverage**: 85%
  - Statements: 87%
  - Branches: 82%
  - Functions: 85%
  - Lines: 86%
  - Threshold: 80% ✅

## Level 6: Build
✅ **Build**: Success
  - Backend build: Success
  - Frontend build: Success
  - Assets generated: Yes

## Overall Result
✅ **VALIDATION PASSED**

All quality checks passed. Code is ready for commit.
```

### Console Output

```
🔄 Running validation pipeline...

Level 0: Environment Safety... ✅
Level 1: Compilation... ✅
Level 2: Unit Tests... ✅ (42/42 passed)
Level 3: Integration Tests... ✅ (15/15 passed)
Level 4: Code Quality... ✅
Level 5: Coverage... ✅ (85%)
Level 6: Build... ✅

✅ Validation complete
Status: PASSED
Report: .claude/agents/reports/validation-report-{timestamp}.md
```

## Implementation Notes

For Claude Code Implementation:

1. **Check** environment safety first
2. **Run** validation levels sequentially
3. **Stop** on failure (depending on level)
4. **Collect** results from each level
5. **Generate** validation report
6. **Return** overall status

### Stop on Failure Behavior

| Level | Stop on Failure? | Reason |
|-------|------------------|--------|
| 0 - Environment | ✅ Yes | Safety critical |
| 1 - Compilation | ✅ Yes | Can't proceed with errors |
| 2 - Unit Tests | ✅ Yes | Tests must pass |
| 3 - Integration | ⚠️ Warning | Optional, warn only |
| 4 - Code Quality | ⚠️ Warning | Warn, allow override |
| 5 - Coverage | ⚠️ Warning | Warn if below threshold |
| 6 - Build | ✅ Yes | Build must succeed |

## Technology-Specific Validation

### Node.js / JavaScript
```bash
npm run lint
npm test
npm run build
npm run test:coverage
```

### Python
```bash
flake8 .
mypy .
pytest
pytest --cov
```

### Java / Spring Boot
```bash
mvn compile
mvn test
mvn jacoco:report
mvn package
```

### React + TypeScript
```bash
npm run lint
npm run type-check
npm test
npm run build
```

## Artifacts Created

- `.claude/agents/reports/validation-report-{timestamp}.md` - Validation results

## Automatic Execution

This command runs **automatically** as part of `/core_piv_loop:execute`:

```
/core_piv_loop:execute
  │
  ├─▶ Execute implementation steps
  │
  └─▶ AUTOMATIC: /validation:validate
      │
      └─▶ Generate reports
```

## Failure Handling

### On Validation Failure

1. **STOP** validation pipeline
2. **REPORT** failure clearly
3. **IDENTIFY** root cause
4. **SUGGEST** fixes
5. **ALLOW** re-validation after fixes

Example failure output:
```
❌ Validation failed

Level 2: Unit Tests... ❌ FAILED

Failed tests:
  ❌ UserServiceTest.createDuplicateEmail
  ❌ AuthControllerTest.invalidToken

Fix issues and run /validation:validate again.
```

### Re-validation

After fixing issues:
```
User: /validation:validate

Re-running validation...
Level 0: Environment Safety... ✅
Level 1: Compilation... ✅
Level 2: Unit Tests... ✅ (42/42 passed - retries passed)
...

✅ Validation passed
```

## Configuration

### Coverage Threshold
Set coverage threshold in project configuration:
```javascript
// jest.config.js
module.exports = {
  coverageThreshold: {
    global: {
      statements: 80,
      branches: 80,
      functions: 80,
      lines: 80
    }
  }
};
```

### Linting Rules
Configure linting rules in technology-specific files:
```javascript
// .eslintrc.js
module.exports = {
  rules: {
    // Your rules
  }
};
```

## Related Commands

- `/core_piv_loop:execute` - Triggers validation automatically
- `/validation:code-review` - Detailed code review (optional)
- `/validation:execution-report` - View execution report

---

**Validation ensures quality automatically. Trust the process and fix any failures.**
