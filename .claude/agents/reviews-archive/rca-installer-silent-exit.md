# Root Cause Analysis: PIV Installer Silent Exit

**Date:** 2026-01-20
**Issue:** PIV installer exits silently in merge mode when `.piv-version` file is missing
**Severity:** High - Blocks update functionality
**Status:** Implementation Ready

## Issue Summary

**Description:**
When running the PIV installer/updater via `curl -s ... | bash` on a project with an existing merge-mode installation, the script exits silently after printing "Checking for updates..." and "Updating PIV Framework". No error message is displayed, and no update occurs.

**Expected Behavior:**
The installer should either:
1. Update the existing installation with new files
2. Display "Already up to date!" if no changes are needed
3. Show an error message if something goes wrong

**Actual Behavior:**
The script prints the header "Updating PIV Framework" then exits silently, returning the user to their shell prompt without any feedback.

**Impact:**
- Affected users: Anyone with merge-mode installation who lacks a `.piv-version` file
- Affected features: Update functionality via `curl | bash`
- Severity: **High** - Makes updates appear broken
- Data impact: None (no data corruption)

## Reproduction

**Can Reproduce:** Yes - Confirmed locally

**Reproduction Steps:**
1. Have a project with merge-mode PIV installation but no `.claude/.piv-version` file
2. Run: `curl -s https://raw.githubusercontent.com/galando/claude-dev-framework/main/scripts/piv.sh | bash`
3. Observe script exits silently after "Updating PIV Framework" header

**Environment:**
- Mode: Merge-mode update
- Shell: bash with `set -euo pipefail`
- Trigger condition: Missing `.claude/.piv-version` file

## Analysis

**Related Files:**
- `scripts/piv.sh:10` - Sets `set -euo pipefail` (exit on error)
- `scripts/install/update.sh:25-36` - `parse_piv_version()` function
- `scripts/install/unified.sh:285` - Calls `parse_piv_version` without checking return code

**Code Flow:**
```
main() → route_to_install_or_update() → update_existing()
  → parse_piv_version() [returns 1 on missing file]
  → Script exits due to set -e (exit on error)
```

**The Problem:**
```bash
# scripts/install/update.sh:25-36
parse_piv_version() {
    local version_file=".claude/.piv-version"

    INSTALLED_VERSION="unknown"
    INSTALLED_COMMIT="unknown"
    # ... defaults set ...

    if [ ! -f "$version_file" ]; then
        return 1  # <-- PROBLEM: Returns error for expected condition
    fi
    # ...
}
```

With `set -e` active in `piv.sh`, the non-zero return causes immediate script termination.

## Root Cause

### Bug #1: Silent Exit (Primary)

**Root Cause:**
`parse_piv_version()` returns exit code 1 when the `.piv-version` file doesn't exist. Combined with `set -euo pipefail` in the main script, this causes a silent exit without any error message.

**Why it Happened:**
1. The function treats a missing `.piv-version` file as an error condition
2. However, this is actually an **expected state** for projects installed before version tracking was added
3. The function already sets safe default values (`INSTALLED_VERSION="unknown"`)
4. The caller (`update_existing`) doesn't check/ignore the return code
5. With `set -e`, any non-zero return causes script termination

### Bug #2: Missing "Full" Reference Files (Secondary)

**Root Cause:**
`install_merge_mode()` in `scripts/install/merge-mode.sh` only copies a hardcoded list of files. It does **not** copy the `reference/rules-full/`, `reference/skills-full/`, or `reference/patterns/` directories.

**Affected Files (NOT copied during fresh install):**
- `.claude/reference/rules-full/*.md` - Complete rule documentation
- `.claude/reference/skills-full/*.md` - Complete skill documentation
- `.claude/reference/patterns/*.md` - Pattern documentation

**Why it Happened:**
The fresh install path (`install_merge_mode`) was written with a hardcoded list of files to copy, while the update path (`update_existing` → `detect_changes`) uses `find` to discover all files recursively. This inconsistency means:

- **Fresh install**: Only copies explicitly listed files
- **Update**: Would copy all files (but Bug #1 prevents updates from running)

**Code locations:**
- `scripts/install/merge-mode.sh:60-63` - Only copies `PIV-METHODOLOGY.md` from reference
- Missing: Copy of `rules-full/`, `skills-full/`, `patterns/` directories

## Fix Strategy

### Fix for Bug #1: Silent Exit

**Change:** `parse_piv_version()` to return 0 (success) when the `.piv-version` file doesn't exist. The function already sets appropriate default values, so this is a valid "no data found" condition rather than an error.

**Implementation Steps:**
1. **Modify `scripts/install/update.sh:35`**
   - Change: `return 1` → `return 0`
   - Rationale: Missing version file is an expected state, not an error

2. **Add version file creation for legacy installations**
   - In `update_existing()`, if version is "unknown", create a fresh `.piv-version` file
   - This prevents future issues and establishes a baseline

### Fix for Bug #2: Missing Full Reference Files

**Change:** `install_merge_mode()` to recursively copy all `reference/` subdirectories, not just `PIV-METHODOLOGY.md`.

**Implementation Options:**

**Option A: Use `merge_dirs` (Recommended)**
```bash
# Replace the single file copy with directory merge
if [ -d "$PIV_SOURCE_DIR/.claude/reference" ]; then
    ensure_dir ".claude/reference"
    merge_dirs "$PIV_SOURCE_DIR/.claude/reference" ".claude/reference"
fi
```

**Option B: Explicit subdirectory copies**
```bash
# Copy each reference subdirectory explicitly
for subdir in methodology rules-full skills-full patterns; do
    if [ -d "$PIV_SOURCE_DIR/.claude/reference/$subdir" ]; then
        ensure_dir ".claude/reference/$subdir"
        merge_dirs "$PIV_SOURCE_DIR/.claude/reference/$subdir" ".claude/reference/$subdir"
    fi
done
```

**Recommendation:** Option A is simpler and more maintainable.

**Files to Modify:**
- `scripts/install/update.sh:35` - Change return code (Bug #1)
- `scripts/install/merge-mode.sh:60-63` - Copy entire reference directory (Bug #2)
- `scripts/install/unified.sh:285-292` - Add version file initialization (Bug #1, optional)

**Testing Strategy:**
- Unit test: Verify `parse_piv_version` returns 0 for missing file
- Integration test: Run installer on project without `.piv-version`
- Verify: After install, check that `rules-full/` and `skills-full/` exist
- Edge case: Install, delete `.piv-version`, run update again

**Validation:**
```bash
# Test 1: Fresh project without version file
rm -f .claude/.piv-version
bash scripts/piv.sh --dry-run
# Expected: Completes successfully, shows "Already up to date"

# Test 2: Verify full reference files exist
ls -la .claude/reference/rules-full/
ls -la .claude/reference/skills-full/
# Expected: Both directories with .md files

# Test 3: Verify version file created
ls -la .claude/.piv-version
# Expected: File now exists
```

## Impact

**Current Impact - Bug #1 (Silent Exit):**
- Users affected: Anyone who installed PIV before version tracking was added
- Features affected: Update functionality via curl pipe
- Severity: High - completely blocks updates
- Data impact: None (no data loss or corruption)

**Current Impact - Bug #2 (Missing Full Files):**
- Users affected: Everyone using merge mode installations
- Features affected: Access to complete rule/skill documentation
- Severity: Medium - files are missing but compressed versions are available
- Data impact: Missing reference documentation (rules-full, skills-full, patterns)

**Potential Side Effects:**
- Bug #1 fix is low-risk: Only changes a return code for an expected condition
- Bug #2 fix is low-risk: Uses existing `merge_dirs` function, just expands scope
- Default values are already set correctly before the problematic return in Bug #1
- No changes to user data or existing file modifications

## Prevention

**How to Prevent:**
- [x] Add test: Verify installer works with missing `.piv-version` file
- [x] Update patterns: Document that missing state files should return 0 with defaults
- [ ] Add monitoring: Consider adding telemetry to track installer success/failure
- [ ] Improve patterns: Use `|| true` pattern when calling functions that may have valid error returns

**Code Review Checklist Update:**
- [ ] Functions called in `set -e` context should return 0 for expected states
- [ ] Missing configuration files should set defaults and return success
- [ ] Add explicit `|| true` or error handling where non-zero returns are acceptable

## Next Steps

1. **Implement fix using:** `/bug_fix:implement-fix .claude/agents/reviews/rca-installer-silent-exit.md`
2. **Validate fix:** Run installer on test project without `.piv-version`
3. **Update prevention measures:** Add test coverage for this scenario
4. **Close issue:** Verify user's update now works correctly

---

**RCA Status:** Implementation Ready

**Bugs Identified:**
1. **Bug #1** (High): Silent exit due to `parse_piv_version()` returning 1
2. **Bug #2** (Medium): Missing `rules-full/`, `skills-full/`, `patterns/` directories

**Fix Complexity:** Low
- Bug #1: 1-line code change
- Bug #2: Replace single file copy with directory merge

**Estimated Risk:** Very Low

**Verification:**
```bash
# Test Bug #1 fix
rm -f .claude/.piv-version && bash scripts/piv.sh --dry-run

# Test Bug #2 fix
rm -rf .claude/reference && bash scripts/piv.sh --dry-run
ls -la .claude/reference/rules-full/
ls -la .claude/reference/skills-full/
```
