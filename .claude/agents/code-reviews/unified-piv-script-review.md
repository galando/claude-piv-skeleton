# Code Review: Unified PIV Script

**Date:** 2026-01-19
**Branch:** feature/unified-piv-script
**Commit:** c3d5290cd11d79dc03e194f7fe514fdb31026705
**PIV Status:** Non-PIV (infrastructure refactoring)

## Stats

- Files Modified: 13
- Files Added: 6
- Files Deleted: 1
- New Lines: +176
- Deleted Lines: -562
- Total Changes: 738 lines

## Summary

This is a significant refactoring that consolidates PIV installation logic into a unified, modular architecture. The changes introduce:
- **Unified installer** (`piv.sh`) that auto-detects install vs update scenarios
- **Modular design** with separate files for core functions, backup, detection, verification, and installation modes
- **Atomic update operations** with staging, verification, and rollback capability
- **Cross-platform checksum support** (macOS md5 vs Linux md5sum)
- **Version tracking** with `.piv-version` file

**Overall Assessment:** This is well-structured, defensive shell scripting with strong attention to safety (backups, rollback, verification). The modularization is a significant improvement over the monolithic `install-piv.sh` that was deleted.

## PIV Compliance

N/A - This is infrastructure/tooling code, not feature implementation using PIV methodology.

## Issues Found

### Critical Issues

None

### High Priority Issues

#### Issue 1: String comparison with `==` instead of `=` for POSIX compatibility

**severity:** high
**file:** `scripts/install/unified.sh:254`
**issue:** Using `==` for string comparison in `[ ]` test - this is a bashism
**detail:**
```bash
if [ "$PINNED_VERSION" == "$current_version" ]; then
```
While the scripts use `#!/bin/bash` and `set -euo pipefail`, using `=` in `[ ]` tests is more portable and consistent with the rest of the codebase.
**suggestion:**
```bash
if [ "$PINNED_VERSION" = "$current_version" ]; then
```

#### Issue 2: Unsafe `find` without `-maxdepth` in `detect-tech.sh`

**severity:** high
**file:** `scripts/install/detect-tech.sh:21-22`
**issue:** `find . -name "pom.xml"` searches recursively without depth limit, can be slow in large node_modules
**detail:**
```bash
if find . -name "pom.xml" -type f 2>/dev/null | head -1 | grep -q .; then
```
In projects with large `node_modules` directories, this can scan hundreds of thousands of files.
**suggestion:**
```bash
if find . -maxdepth 3 -name "pom.xml" -type f 2>/dev/null | head -1 | grep -q .; then
```

### Medium Priority Issues

#### Issue 3: Hardcoded path in test file

**severity:** medium
**file:** `scripts/install/test/test-unified.sh:59`
**issue:** Absolute path `/Users/galando/dev/claude-dev-framework/scripts/piv.sh` hardcoded
**suggestion:** Use relative path or environment variable

#### Issue 4: `select_menu` return value handling inconsistency

**severity:** medium
**file:** `scripts/install/detect-tech.sh:413-450`
**issue:** Function echoes selection but callers check `$?` exit code instead of captured value

#### Issue 5: Unused `PIV_VERSION` variable

**severity:** medium
**file:** `scripts/piv.sh:20`
**issue:** Variable is set but never used for version fetching

### Low Priority Issues

#### Issue 6: Redundant commented-out source guards
**severity:** low
**file:** Multiple files in `scripts/install/`

#### Issue 7: No error propagation in `stage_changes` loop
**severity:** low
**file:** `scripts/install/update.sh:219`

## Positive Findings

1. **Excellent modular design** - Clear separation of concerns
2. **Strong safety measures** - Backup, rollback, atomic operations, checksum verification
3. **Cross-platform considerations** - macOS vs Linux handling
4. **Good defensive coding** - `set -euo pipefail`, proper quoting, subshell-safe operations
5. **User experience** - TTY detection, clear messages, dry-run mode
6. **File classification system** - Smart framework/user/additive distinction

## Environment Safety Check

- [x] No hardcoded production URLs
- [x] No hardcoded local URLs (except test file - noted)
- [x] No hardcoded API keys or secrets
- [x] All configuration via variables
- [x] Safe for both LOCAL and PROD environments

## Conclusion

**Overall Assessment:** PASS with recommended fixes

**Summary:** High-quality shell scripting infrastructure. The modularization significantly improves maintainability. The atomic update mechanism with rollback is production-grade.

`★ Insight ─────────────────────────────────────`
**1. Atomic update pattern**: The staging/verify/apply pattern prevents partial updates that could leave the system in an inconsistent state. The checksum verification adds extra safety.

**2. TTY handling for piped input**: The `curl | bash` pattern is well-executed. By detecting stdin is not a terminal and using `/dev/tty` for interactive prompts, the script supports both piped and direct execution.

**3. File classification wisdom**: The distinction between "framework" (always update), "user" (never touch), and "additive" (add only) files demonstrates thoughtful handling of the tension between framework updates and user customizations.
`─────────────────────────────────────────────────`

**Next Steps:**
1. Fix high-priority issues (string comparison, find depth limit)
2. Fix medium-priority issues (test path, select_menu usage)
3. Consider low-priority cleanup
4. Ready for commit once critical/high issues fixed
