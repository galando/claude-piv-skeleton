# Quickstart: Template Distribution for Plugin Users

**Feature ID:** template-distribution-for-plugin-users

## What This Feature Does

Embeds template content directly in command files so templates travel with the plugin distribution.

## Quick Reference

**Files Modified:**
- `.claude/commands/piv_loop/constitution.md` - Embed constitution template
- `.claude/reference/execution/plan-feature.md` - Embed Spec-Kit templates
- `README.md` - Document plugin vs local repo differences

**Test Commands:**
```bash
# Test constitution command
claude --plugin-dir . piv-speckit:constitution

# Test plan generation
claude --plugin-dir . piv-speckit:plan-feature "test feature"
```

## Key Implementation Notes

- Templates embedded as Markdown code blocks within command files
- Keep existing `.template.md` files for reference (not distributed)
- No functional changes - only content reorganization

## Artifacts

- **Spec**: `spec.md` - WHAT we're building
- **Plan**: `plan.md` - HOW we'll build it
- **Tasks**: `tasks.md` - Step-by-step implementation
