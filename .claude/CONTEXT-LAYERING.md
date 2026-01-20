# Context Layering Architecture

## Overview

The framework uses a layered context approach to minimize token usage while maintaining full functionality when needed.

## Problem Solved

**Before:** The plugin loaded ~500KB of inline instructions including embedded command definitions, duplicate reference files, and archived plans. This resulted in:
- `plan-feature`: 8.2k tokens per invocation
- `validate`: 4.5k tokens per invocation
- `code-review`: 4.4k tokens per invocation
- `execute`: 3.4k tokens per invocation

**After:** With layered context, each command uses <1K tokens when invoked - an ~85% reduction.

## Layers

### Core Layer (~15KB) - Always Loaded

These files are always loaded by Claude Code:

- `rules/` - Compressed development rules
- `skills/` - Minimal skill stubs (reference to full docs)
- `commands/` - Command stubs (frontmatter + reference only)

**Example command stub:**
```yaml
---
description: "Create comprehensive feature plan"
argument-hint: "<feature-name>"
---

# Plan a New Feature

## Execution
`Read .claude/reference/execution/plan-feature.md`
```

### Extended Layer (~50KB) - On-Demand

These files are loaded only when a specific command is invoked:

- `reference/execution/` - Full command instructions
  - `plan-feature.md` (~32KB) - Full planning methodology
  - `execute.md` (~13KB) - Full execution instructions
  - `validate.md` (~18KB) - Full validation commands
  - `code-review.md` (~17KB) - Full review checklist
- `reference/methodology/` - PIV methodology
- `reference/skills-full/` - Complete skill definitions

**How it works:** When a command is invoked, Claude reads the reference file and has full instructions available.

### Archive Layer (~400KB) - Manual Access

These files are NOT loaded by default:

- `agents/plans-archive/` - Completed implementation plans
- `agents/code-reviews/` - Code reviews

**Purpose:** Historical reference, loaded only when explicitly referenced.

## File Structure

```
.claude/
├── CLAUDE.md                    # Project overview (core)
├── CONTEXT-LAYERING.md          # This file (core)
│
├── rules/                       # Core - always loaded
│   ├── 00-general.md
│   ├── 05-pragmatic.md
│   ├── 10-git.md
│   ├── 20-testing.md
│   └── ...
│
├── skills/                      # Core - minimal stubs
│   └── code-review/SKILL.md     # Points to full doc
│
├── commands/                    # Core - stubs only
│   ├── piv_loop/
│   │   ├── plan-feature.md      # ~500 bytes (was 32KB!)
│   │   ├── execute.md           # ~500 bytes (was 13KB!)
│   │   └── prime.md
│   └── validation/
│       ├── validate.md          # ~500 bytes (was 18KB!)
│       └── code-review.md       # ~500 bytes (was 17KB!)
│
├── reference/                   # Extended - on-demand
│   ├── execution/
│   │   ├── plan-feature.md      # Full content (32KB)
│   │   ├── execute.md           # Full content (13KB)
│   │   ├── validate.md          # Full content (18KB)
│   │   └── code-review.md       # Full content (17KB)
│   ├── methodology/
│   │   └── PIV-METHODOLOGY.md
│   └── skills-full/
│       └── code-review-full.md
│
└── agents/
    └── plans-archive/           # Archive - manual access only
        └── README.md
```

## Maintenance

### Adding New Commands

When adding a new command:

1. **Put full instructions** in `reference/execution/[command-name].md`
2. **Put minimal stub** in `commands/[category]/[command-name].md`

**Stub template:**
```yaml
---
description: "Brief one-line description"
argument-hint: "<optional-hint>"
---

# Command Name

**Goal:** One-line summary.

## Execution

`Read .claude/reference/execution/[command-name].md`

Brief description of what the command does.
```

### Archiving Completed Work

After completing a feature:

1. **Move plan** to `agents/plans-archive/`
2. **Move review** to `agents/code-reviews/` (if applicable)

These files are NOT loaded in active context but preserved for reference.

### Updating Reference Files

When updating methodology or instructions:

1. **Edit** the file in `reference/execution/`
2. **Verify** the command stub still points correctly
3. **Test** the command to ensure it loads the reference

## Token Usage Comparison

| Command | Before | After | Reduction |
|---------|--------|-------|-----------|
| plan-feature | 8.2k | ~1k | **88%** |
| execute | 3.4k | ~500 | **85%** |
| validate | 4.5k | ~500 | **89%** |
| code-review | 4.4k | ~500 | **89%** |

**Total context:** ~500KB → ~50KB = **90% reduction**

## Benefits

1. **Faster context loading:** Less data to process on each invocation
2. **Lower token costs:** Significant reduction in per-command usage
3. **Same functionality:** Full instructions still available when needed
4. **Better UX:** Commands feel snappier with less initial overhead

## Design Rationale

**Why extract instead of inline?**
- Reduces base context from ~500KB to ~50KB
- Keeps full instructions available when needed
- One extra `Read` operation is negligible compared to token savings

**Why archive plans?**
- Completed plans don't need active context
- Preserved for historical reference
- Can be restored if needed

**Why layered architecture?**
- Core always available for basic operations
- Extended loaded on-demand for specific commands
- Archive accessible manually for historical context
