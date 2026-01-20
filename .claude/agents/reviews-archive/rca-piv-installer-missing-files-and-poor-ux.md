# Root Cause Analysis: PIV Installer - Missing Files & Poor UX

**Date:** 2026-01-19
**Issue:** Empty reference directories, missing adaptive learning commands, silent installation
**Severity:** High
**Status:** Implementation Ready

---

## Executive Summary

After running the PIV installer, three distinct issues were identified:

1. **Missing reference files** (High): `.claude/reference/rules-full/` and `skills-full/` are empty
2. **Missing adaptive learning commands** (Medium): 3 validation commands not installed
3. **Poor user experience** (Medium): Installer runs silently with minimal feedback

---

## Issue Summary

### Bug #1: Empty Reference Directories

**Expected Behavior:**
- `.claude/reference/rules-full/` should contain 10 detailed rule documents (~90KB)
- `.claude/reference/skills-full/` should contain 6 detailed skill documents (~40KB)

**Actual Behavior:**
- `.claude/reference/rules-full/` is empty (0 files)
- `.claude/reference/skills-full/` is empty (0 files)

### Bug #2: Missing Adaptive Learning Commands

**Expected Behavior:**
- `.claude/commands/validation/learn.md` - Analyze code reviews for patterns
- `.claude/commands/validation/learning-status.md` - Display learning metrics
- `.claude/commands/validation/suggest-improvement.md` - Generate improvement suggestions

**Actual Behavior:**
- These 3 files are missing from snap-to-calendar

### Bug #3: Silent Installation

**Expected Behavior:**
- Clear progress indicators for each stage
- Summary of what was installed
- Next steps guidance

**Actual Behavior:**
- Installer runs silently
- No progress indication
- Generic success message only

**Impact:**
- Affected users: Anyone who ran PIV installer since commit 894ee4d
- Affected features: Reference documentation, adaptive learning system
- Severity: **High** - Core framework documentation is missing

---

## Detailed File Comparison

### Framework Source Files (68 total):
| Category | Files | Status |
|----------|-------|--------|
| Commands | 13 files | 3 missing in target |
| Rules | 8 files | Different versions |
| Rules-full | 10 files | **0 copied to target** |
| Skills-full | 6 files | **0 copied to target** |
| Skills | 6 SKILL.md files | Not copied to target |
| Reference | 1 PIV-METHODOLOGY.md | Copied correctly |
| Agents/learning | 1 file | Not copied to target |

### snap-to-calendar Files (32 total):
- Missing: 36 files from framework
- Extra: 3 project-specific rules (21-testing.md, frontend/react-best-practices.md)

---

## Root Cause Analysis

### Root Cause #1: Reference Directories Created But Never Populated

**File:** `scripts/install/update.sh:318-333`

```bash
ensure_reference_structure() {
    print_info "Ensuring reference directory structure..."

    local dirs=(
        ".claude/reference/methodology"
        ".claude/reference/rules-full"      # ← CREATED EMPTY
        ".claude/reference/skills-full"     # ← CREATED EMPTY
        ".claude/reference/patterns"
    )

    for dir in "${dirs[@]}"; do
        ensure_dir "$dir"    # ← Only creates directories
    done

    log "INFO" "Reference structure ensured"  # ← MISLEADING - implies done
}
```

**The Bug:** The function creates directories but **never copies files into them**.

### Root Cause #2: Change Detection Doesn't Scan Reference Subdirectories

**File:** `scripts/install/update.sh:140-164`

```bash
detect_changes() {
    local changes=""
    local upstream="$PIV_SOURCE_DIR/.claude"

    # Check framework files
    for dir in commands skills reference; do    # ← "reference" only checks top-level
        if [ -d "$upstream/$dir" ]; then
            find "$upstream/$dir" -type f -name "*.md" 2>/dev/null | while read -r upstream_file; do
                # ... compare and add/update logic ...
            done
        fi
    done
}
```

**The Bug:** The `find "$upstream/reference"` command finds:
- `reference/methodology/PIV-METHODOLOGY.md` ✓
- `reference/rules-full/*.md` ✗ (Not found because loop only looks at top-level)

The problem is that the `find` within `reference/` should recurse, but even if it did, the actual files need to be copied by `stage_changes()` and `apply_staged_changes()`.

### Root Cause #3: Missing UX Feedback

**File:** `scripts/install/unified.sh:98-144`

The installer goes through 7 stages but only shows 2 headers:

```bash
print_header "Installing PIV"
# ...silent detection...
print_header "Analyzing Project"
# ...silent installation...
print_success "PIV installed successfully"  # ← Only feedback!
```

**Missing feedback points:**
1. Prerequisite check status
2. Technology detection results
3. Selected installation mode
4. File copy progress
5. Number of files installed
6. Backup location
7. Next steps

---

## Complete Validation Findings

### Missing Files in snap-to-calendar:

#### Reference Files (16 missing):
```
.claude/reference/rules-full/api-design-full.md
.claude/reference/rules-full/database-full.md
.claude/reference/rules-full/documentation-full.md
.claude/reference/rules-full/general-full.md
.claude/reference/rules-full/git-full.md
.claude/reference/rules-full/pragmatic-principles-full.md
.claude/reference/rules-full/security-full.md
.claude/reference/rules-full/tdd-full.md
.claude/reference/rules-full/testing-full.md
.claude/reference/rules-full/testing-gwt-full.md
.claude/reference/skills-full/adaptive-learning-full.md
.claude/reference/skills-full/api-design-full.md
.claude/reference/skills-full/code-review-full.md
.claude/reference/skills-full/security-full.md
.claude/reference/skills-full/test-driven-development-full.md
.claude/reference/skills-full/test-writing-full.md
```

#### Command Files (3 missing):
```
.claude/commands/validation/learn.md
.claude/commands/validation/learning-status.md
.claude/commands/validation/suggest-improvement.md
```

#### Other Files (7 missing):
```
.claude/rules/05-pragmatic.md
.claude/rules/backend/10-api-design.md
.claude/rules/backend/20-database.md
.claude/skills/adaptive-learning/SKILL.md
.claude/skills/api-design/SKILL.md
.claude/skills/code-review/SKILL.md
.claude/skills/security/SKILL.md
.claude/skills/test-driven-development/SKILL.md
.claude/skills/test-writing/SKILL.md
.claude/agents/learning/learning-metrics.md
```

### Outdated File References:
- `commands/git/commit.md:371` references old `.claude/PIV-METHODOLOGY.md` path

---

## Fix Strategy

### Recommended Fix: Multi-Pronged Approach

**Option 1: Comprehensive Installer Overhaul** (RECOMMENDED)

Fix all three issues in one update.

### Implementation Steps:

#### Step 1: Fix `detect_changes()` to scan reference subdirectories

**File:** `scripts/install/update.sh`

```bash
detect_changes() {
    local changes=""
    local upstream="$PIV_SOURCE_DIR/.claude"

    # Check framework files (commands, skills)
    for dir in commands skills; do
        if [ -d "$upstream/$dir" ]; then
            find "$upstream/$dir" -type f -name "*.md" 2>/dev/null | while read -r upstream_file; do
                local rel_path="${upstream_file#$upstream/}"
                local local_file=".claude/$rel_path"

                if [ -f "$local_file" ]; then
                    local upstream_hash=$(compute_checksum "$upstream_file")
                    local local_hash=$(compute_checksum "$local_file")
                    if [ "$upstream_hash" != "$local_hash" ]; then
                        echo "UPDATE .claude/$rel_path"
                    fi
                else
                    echo "ADD .claude/$rel_path"
                fi
            done
        fi
    done

    # NEW: Explicitly scan reference subdirectories recursively
    if [ -d "$upstream/reference" ]; then
        find "$upstream/reference" -type f -name "*.md" 2>/dev/null | while read -r upstream_file; do
            local rel_path="${upstream_file#$upstream/}"
            local local_file=".claude/$rel_path"

            if [ -f "$local_file" ]; then
                local upstream_hash=$(compute_checksum "$upstream_file")
                local local_hash=$(compute_checksum "$local_file")
                if [ "$upstream_hash" != "$local_hash" ]; then
                    echo "UPDATE .claude/$rel_path"
                fi
            else
                echo "ADD .claude/$rel_path"
            fi
        done
    fi

    # Check additive files (rules)
    find "$upstream/rules" -type f -name "*.md" 2>/dev/null | while read -r rule_file; do
        local rel_path="${rule_file#$upstream/}"
        local local_file=".claude/$rel_path"
        if [ ! -f "$local_file" ]; then
            echo "ADD .claude/$rel_path"
        fi
    done
}
```

#### Step 2: Update `classify_file()` to handle reference paths

**File:** `scripts/install/update.sh:83-109`

```bash
classify_file() {
    local file_path="$1"
    local clean_path="${file_path#.claude/}"

    case "$clean_path" in
        # Framework files - always update (include reference subdirectories)
        commands/*|skills/*|reference/*/*)
            echo "framework"
            ;;
        # User files - never touch
        CLAUDE.md|settings.local.json)
            echo "user"
            ;;
        agents/context/*|agents/learning/*|agents/plans/*|agents/reviews/*|agents/code-reviews/*)
            echo "user"
            ;;
        # Additive files - add new only
        rules/*|rules/*/*)
            echo "additive"
            ;;
        *)
            echo "unknown"
            ;;
    esac
}
```

#### Step 3: Add comprehensive UX feedback

**File:** `scripts/install/unified.sh`

Add before line 98:
```bash
# Stage tracking
STAGE_NUM=0
TOTAL_STAGES=6

next_stage() {
    STAGE_NUM=$((STAGE_NUM + 1))
    print_header "Stage $STAGE_NUM of $TOTAL_STAGES: $1"
}
```

Replace silent operations with:
```bash
next_stage "Environment Detection"
print_info "Checking system prerequisites..."
# ... detection code ...
print_success "Prerequisites met"

next_stage "Project Analysis"
print_info "Analyzing project structure..."
# ... technology detection ...
print_success "Detected: $TECHNOLOGIES"

next_stage "Installation Mode"
print_info "Determining installation mode..."
# ... mode selection ...
print_success "Mode: $MODE"

next_stage "Backup"
print_info "Creating backup of existing files..."
# ... backup ...
print_success "Backup: $BACKUP_PATH"

next_stage "Installation"
print_info "Installing PIV framework files..."
# ... installation with file counting ...
print_success "Installed: $X commands, $Y rules, $Z reference files"

next_stage "Verification"
print_info "Verifying installation..."
# ... verification ...
print_success "All checks passed"

print_header "Installation Complete"
echo ""
print_success "PIV framework installed successfully!"
echo ""
echo "Summary:"
echo "  Commands:    .claude/commands/"
echo "  Rules:       .claude/rules/"
echo "  Reference:   .claude/reference/"
echo "  Skills:      .claude/skills/"
echo ""
echo "Backup saved to: $BACKUP_PATH"
echo ""
echo "Next steps:"
echo "  1. Run '/piv_loop:prime' to load context"
echo "  2. Run '/validation:validate' to verify setup"
echo ""
```

#### Step 4: Add file count logging to installer

**File:** `scripts/install/core.sh`

Add to file copy operations:
```bash
copy_file_with_feedback() {
    local src="$1"
    local dest="$2"
    local current="$3"
    local total="$4"

    if [ -n "$total" ] && [ "$total" -gt 10 ]; then
        # Only show progress for larger batches
        printf "\r  [%d/%d] Installing: %s" "$current" "$total" "$(basename "$src")"
    fi

    copy_file "$src" "$dest"
}
```

### Files to Modify:
- `scripts/install/update.sh` - Fix `detect_changes()`, `classify_file()`
- `scripts/install/unified.sh` - Add stage notifications and summary
- `scripts/install/core.sh` - Add progress feedback functions
- `scripts/install/verify.sh` - Add reference file validation
- `scripts/install/merge-mode.sh` - Add progress feedback

### Testing Strategy:
1. **Unit test:** Verify `detect_changes()` outputs reference files
2. **Integration test:** Run installer on clean project
3. **Regression test:** Run installer on existing project
4. **UX test:** Verify clear feedback at each stage

### Validation:
- Run `--fix` mode on snap-to-calendar
- Verify all 16 reference files are present
- Verify all 3 learning commands are present
- Count files before/after to confirm
- Check installer output for clear feedback

---

## Impact Assessment

### Current Impact:
- **Users affected:** Anyone who ran PIV installer since commit 894ee4d
- **Projects affected:** All projects using merge-legacy mode
- **Files missing:** 26 files (~130KB documentation)
- **UX impact:** Users don't know what was installed or if it succeeded

### Potential Side Effects:
- Fix will add ~130KB to target projects
- May need to handle merge conflicts if users manually added files
- Installer will be more verbose (positive change)

---

## Prevention Measures

### Code Changes:
1. ✅ Add `test-verify-reference-files.sh` to check reference directories
2. ✅ Add installer test that counts files copied
3. ✅ Add UX test that verifies feedback messages
4. ✅ Add `--fix` flag to repair incomplete installations

### Process Changes:
1. ✅ Update code review checklist to verify file copy logic
2. ✅ Add "file count verification" to installer output
3. ✅ Require UX review for installer changes
4. ✅ Add installer output snapshot tests

### Documentation:
1. ✅ Document expected file counts in installer
2. ✅ Add troubleshooting section for missing files
3. ✅ Document installer stages and expected output

---

## Additional Findings

### Skills Directory:
The framework has `.claude/skills/` with SKILL.md files for each skill. These are also not being copied to target projects. This is lower priority but should be addressed.

### Agent Learning Directories:
Empty subdirectories exist in framework:
- `.claude/agents/learning/applied/` (empty)
- `.claude/agents/learning/insights/` (empty)
- `.claude/agents/learning/suggestions/` (empty)

These are intentionally empty (populated during runtime) but installer creates them silently.

---

## Next Steps

1. ✅ Implement fix using: `/bug_fix:implement-fix <this-rca>`
2. ✅ Run installer with `--fix` on snap-to-calendar
3. ✅ Run `/validation:validate` to verify all files present
4. ✅ Add installer UX tests
5. ✅ Update documentation with troubleshooting section

---

## RCA Status

**Status:** Implementation Ready
**Complexity:** Medium (requires careful update to change detection + UX improvements)
**Priority:** High (core documentation missing)
**Estimated effort:** 2-3 hours for implementation + testing

---

**Prepared by:** Claude (bug_fix:rca skill)
**Reviewed by:** Pending
**Approved by:** Pending
