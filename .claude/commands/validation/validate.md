---
description: Run comprehensive validation of the Example project in LOCAL DEV MODE only
---

# Validate: Run Full Validation Suite (LOCAL DEV MODE)

**⚠️ CRITICAL SAFETY RULE:**
- **ALWAYS** validate in **LOCAL DEV MODE** (Docker PostgreSQL)
- **NEVER** validate against **PRODUCTION CLOUD** database
- Production mode is ONLY for deployed releases, NEVER for development/testing

## Environment Modes

### Mode 1: Local Development (Docker) ✅ **USE THIS FOR VALIDATION**

**Configuration:** `backend/.env.local`
**Database:** Local Docker PostgreSQL
**Services:** PostgreSQL, Hasura, Adminer (all in Docker)

**Start:**
```bash
./start-local.sh
```

**Stop:**
```bash
./stop-local.sh
```

**When to use:**
- ✅ ALL validation and testing
- ✅ Feature development
- ✅ Bug fixing
- ✅ Experimentation

### Mode 2: Production (CloudProvider Cloud) ⚠️ **NEVER USE FOR VALIDATION**

**Configuration:** `backend/.env`
**Database:** CloudProvider cloud PostgreSQL
**Services:** CloudProvider cloud (authentication + database)

**Start:**
```bash
./start.sh
```

**Stop:**
```bash
./stop.sh
```

**When to use:**
- ✅ Production deployments ONLY
- ✅ Production release testing ONLY
- ❌ NEVER for feature development
- ❌ NEVER for validation/testing

## Validation Commands (LOCAL DEV MODE)

**Prerequisite: Ensure local dev environment is running:**

```bash
# Check if local mode is running
docker ps | grep postgres

# If not running, start local dev mode:
./start-local.sh
```

### 1. Verify Environment

**⚠️ CRITICAL: Confirm we're in LOCAL DEV MODE**

```bash
# Check which .env file is being used
cat backend/.env.local | grep DATABASE_URL

# Expected output: localhost or 127.0.0.1 (NOT production.example.com)
# Example: jdbc:postgresql://localhost:5432/example

# If you see production.example.com in DATABASE_URL, STOP! You're in PROD mode!
```

**✅ SAFE:** Database URL contains `localhost` or `127.0.0.1`
**❌ UNSAFE:** Database URL contains `production.example.com`

### 2. Backend Compilation

```bash
cd backend && mvn clean compile
```

**Expected:** BUILD SUCCESS

**Time:** ~30 seconds

**Why:** Ensures all Java code compiles without errors, including all dependencies.

### 3. Backend Unit Tests

```bash
cd backend && mvn test
```

**Expected:** All tests pass, zero failures

**Time:** ~60 seconds

**Why:** Verifies all unit tests pass, ensuring business logic is correct.

**Output to Check:**
```
Tests run: XXX, Failures: 0, Errors: 0, Skipped: 0
```

### 4. Backend Integration Tests (LOCAL DEV MODE)

```bash
cd backend && mvn verify -DskipUnitTests=true
```

**Expected:** All integration tests pass

**Time:** ~120 seconds

**Why:** Verifies integration with LOCAL database and external services works correctly.

**⚠️ SAFETY CHECK:**
- Uses `backend/.env.local` configuration
- Connects to LOCAL Docker PostgreSQL
- Does NOT touch CloudProvider production data

**Note:** Requires local Docker PostgreSQL to be running (`./start-local.sh`).

### 5. Test Coverage

```bash
cd backend && mvn jacoco:report
```

**Expected:** Coverage >= 80%

**Time:** ~10 seconds

**Why:** Ensures new code has adequate test coverage.

**How to Check:**
- Report generated in `backend/target/site/jacoco/index.html`
- Check overall coverage percentage
- Ensure new files have >= 80% coverage

### 6. Frontend Type Check

```bash
cd frontend && npm run type-check
```

**Expected:** No TypeScript errors

**Time:** ~15 seconds

**Why:** Ensures all TypeScript code is properly typed.

### 7. Frontend Linting

```bash
cd frontend && npm run lint
```

**Expected:** No linting errors

**Time:** ~10 seconds

**Why:** Ensures code follows project style guidelines.

### 8. Frontend Unit Tests

```bash
cd frontend && npm test
```

**Expected:** All tests pass

**Time:** ~30 seconds

**Why:** Verifies frontend logic and components work correctly.

### 9. Frontend Build

```bash
cd frontend && npm run build
```

**Expected:** Build completes successfully, outputs to `dist/`

**Time:** ~45 seconds

**Why:** Ensures production build works without errors.

### 10. Database Migration Check (LOCAL DEV MODE)

```bash
cd backend && mvn flyway:info -Dflyway.configFiles=src/main/resources/application.properties
```

**Expected:** All migrations applied, no pending migrations

**Why:** Verifies LOCAL database schema is up to date.

**⚠️ SAFETY CHECK:**
- Only affects LOCAL Docker database
- Does NOT touch CloudProvider production database

### 11. Application Startup (LOCAL DEV MODE)

**⚠️ CRITICAL: Only run this in LOCAL DEV MODE!**

```bash
# Start backend in LOCAL mode
cd backend && mvn spring-boot:run &
BACKEND_PID=$!
sleep 15  # Wait for startup

# Check we're connecting to LOCAL database
curl -s http://localhost:8080/actuator/health | jq .

# Expected output:
# {
#   "status": "UP"
# }

# Verify database connection (should be localhost)
curl -s http://localhost:8080/actuator/info | jq .

# Stop backend
kill $BACKEND_PID 2>/dev/null || true
```

**Expected:** Status UP, database is LOCAL (not production.example.com)

**Why:** Verifies application starts successfully with all changes in LOCAL mode.

**⚠️ SAFETY CHECKS:**
- Verify `application.properties` or `.env.local` is being used
- Verify database URL points to localhost
- Verify NO connection to production.example.com

## Summary Report

After all validations complete, provide a summary report with:

### Environment Confirmation

**✅ CONFIRMED:** Running in LOCAL DEV MODE
- Database: Local Docker PostgreSQL
- Configuration: `backend/.env.local`
- NO connection to CloudProvider production

### Validation Results

| Level | Command | Status | Time | Notes |
|-------|---------|--------|------|-------|
| 0 | Environment Check | ✅ PASS | 5s | LOCAL mode confirmed |
| 1 | Backend Compilation | ✅ PASS / ❌ FAIL | 30s | |
| 2 | Backend Unit Tests | ✅ PASS / ❌ FAIL | 60s | Tests run: X, Failures: 0 |
| 3 | Integration Tests (LOCAL) | ✅ PASS / ❌ FAIL | 120s | Tests run: X, Failures: 0 |
| 4 | Test Coverage | ✅ PASS / ❌ FAIL | 10s | Coverage: XX% |
| 5 | Frontend Type Check | ✅ PASS / ❌ FAIL | 15s | |
| 6 | Frontend Linting | ✅ PASS / ❌ FAIL | 10s | |
| 7 | Frontend Tests | ✅ PASS / ❌ FAIL | 30s | Tests pass: X |
| 8 | Frontend Build | ✅ PASS / ❌ FAIL | 45s | |
| 9 | DB Migration Check (LOCAL) | ✅ PASS / ❌ FAIL | 5s | |
| 10 | App Startup (LOCAL) | ✅ PASS / ❌ FAIL | 15s | Status: UP |

### Overall Health Assessment

**Status:** ✅ PASS / ❌ FAIL

**Pass Rate:** X/11 validations passing

**Total Time:** X minutes

**Environment:** ✅ LOCAL DEV MODE (SAFE)

### Issues Found

**If any validations failed, document:**

1. **Level X - Validation Name**
   - **Error:** [Error message or output]
   - **Impact:** [What this means]
   - **Fix:** [How to fix it]

### Recommendations

**Based on validation results:**

- [ ] All validations passing - ready for code review
- [ ] Some validations failing - fix and re-run
- [ ] Coverage below 80% - add more tests
- [ ] Linting errors - fix style issues

## Safety Checklist

Before running validation, ALWAYS confirm:

- [ ] Using `backend/.env.local` (NOT `backend/.env`)
- [ ] Database URL points to localhost/127.0.0.1
- [ ] NO connection to production.example.com in configuration
- [ ] Started with `./start-local.sh` (NOT `./start.sh`)
- [ ] Testing data is disposable (NOT production data)

**If ANY of these checks fail:**
- ❌ STOP immediately
- ❌ DO NOT run validation
- ❌ Fix configuration first
- ❌ Re-check environment

## Troubleshooting

### Common Issues

**Issue: Backend compilation fails**
- **Check:** Java version (should be 24)
- **Fix:** Ensure JAVA_HOME points to Java 24
- **Command:** `java -version` should show 24

**Issue: Tests fail**
- **Check:** Test output for specific failure
- **Fix:** Debug failing test, fix implementation
- **Command:** `mvn test -Dtest=FailingTest` for single test

**Issue: Integration tests fail**
- **Check:** LOCAL Docker PostgreSQL is running
- **Fix:** Start local database with `./start-local.sh`
- **Command:** `docker ps | grep postgres`

**Issue: Accidentally connected to CloudProvider**
- **Symptoms:** Tests run against production data
- **Fix:** STOP immediately, check configuration
- **Verify:** `cat backend/.env.local | grep DATABASE_URL`
- **Safe:** URL should contain `localhost` or `127.0.0.1`
- **Unsafe:** URL contains `production.example.com`

**Issue: Coverage below 80%**
- **Check:** JaCoCo report for uncovered lines
- **Fix:** Add tests for uncovered code
- **Command:** Open `backend/target/site/jacoco/index.html`

**Issue: Frontend build fails**
- **Check:** TypeScript errors
- **Fix:** Fix type errors or add // @ts-ignore if necessary
- **Command:** `npm run type-check` for details

**Issue: Application won't start**
- **Check:** Application logs for errors
- **Fix:** Fix configuration issues
- **Command:** Check `backend/src/main/resources/application.properties`
- **Verify:** Using LOCAL database configuration

## Success Criteria

**Validation is considered PASSING when:**

- ✅ Running in LOCAL DEV mode (confirmed)
- ✅ All 11 validation levels pass
- ✅ Zero test failures
- ✅ Coverage >= 80%
- ✅ No compilation errors
- ✅ No linting errors
- ✅ Application starts successfully
- ✅ NO connection to CloudProvider production

**If any validation fails:**

- ❌ Fix the issue
- ❌ Re-run the failed validation
- ❌ Continue until all validations pass

---

**⚠️ CRITICAL REMINDERS:**

1. **ALWAYS** validate in LOCAL DEV MODE (`./start-local.sh`)
2. **NEVER** validate in PRODUCTION MODE (`./start.sh`)
3. **VERIFY** database URL points to localhost before running
4. **CHECK** configuration is using `.env.local` not `.env`
5. **STOP** if you see production.example.com in any connection string

**Remember:** Run validations in order. Don't skip levels. Fix failures before proceeding.

**Safety First:** Better to stop and check than to accidentally touch production data!

---

## Final State: Ready to Commit

**When ALL validations pass:**

### Report Success

```markdown
✅ ✅ ✅ ALL VALIDATIONS PASSED ✅ ✅ ✅

Feature Implementation: COMPLETE
Code Quality: VERIFIED
Test Coverage: ACHIEVED
Security: CHECKED
Performance: VALIDATED

SUMMARY:
- Files Created: X
- Files Modified: X
- Tests Added: X
- Test Coverage: XX%
- All Issues: RESOLVED

STATUS: ✅ READY TO COMMIT

The feature is complete and all quality gates have passed.
You can now commit this feature with confidence.

NEXT STEP: Run `/commit` to finish
```

### What This Means

- Code compiles without errors
- All tests pass (unit + integration)
- Test coverage meets requirements (≥80%)
- No critical or high priority issues
- Security scan passed
- Performance validated
- Environment verified (LOCAL mode)

### User Action Required

**Do NOT auto-commit** (manual approval gate)

User should:
1. Review the summary above
2. Check file list is correct
3. Verify test coverage is adequate
4. Run `/commit` when ready

### If Called Manually (Not From Execute)

Show:
- Validation results summary
- Pass/fail status for each check
- "Run `/commit` to finish if all validations passed"

Don't auto-chain (user is in control of manual flow).

### If Any Validation Fails

Don't reach "Ready State" - Instead:
- Tell user which validation failed
- Explain what went wrong
- Suggest how to fix
- Ask user to fix and re-run `/validation:validate`

**Only reach "Ready State" when ALL validations pass.**
