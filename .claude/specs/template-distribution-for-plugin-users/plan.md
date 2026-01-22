# Plan: Template Distribution for Plugin Users

**Feature ID:** template-distribution-for-plugin-users
**Status:** DRAFT
**Created:** 2025-01-22

## Technical Approach

**Embed templates directly in command files** rather than referencing external files. This ensures templates travel with the plugin since command files ARE distributed.

## Architecture

### Current State (Broken)

```
┌─────────────────┐      references      ┌──────────────────┐
│  constitution.md│ ───────────────────▶ │ constitution.tplt│
│  (distributed)  │                      │ (NOT distributed)│
└─────────────────┘                      └──────────────────┘

┌─────────────────┐      references      ┌──────────────────┐
│ plan-feature.md │ ───────────────────▶ │  .templates/     │
│  (distributed)  │                      │ (NOT distributed)│
└─────────────────┘                      └──────────────────┘
```

### Future State (Fixed)

```
┌─────────────────────────────────────────────────────────┐
│              constitution.md (with embedded template)    │
│  ┌─────────────────────────────────────────────────┐    │
│  │ ## Template (embedded content)                    │    │
│  │ ```markdown                                      │    │
│  │ # Project Constitution                           │    │
│  │ ...                                              │    │
│  │ ```                                              │    │
│  └─────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────┐
│         plan-feature.md (refers to execution guide)     │
│                      │                                   │
│                      ▼                                   │
│  ┌─────────────────────────────────────────────────┐    │
│  │ .claude/reference/execution/plan-feature.md      │    │
│  │ (contains embedded Spec-Kit templates)           │    │
│  └─────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────┘
```

### Technology Choices

| Component | Technology | Rationale |
|-----------|------------|-----------|
| Template storage | Embedded Markdown | Travels with plugin, no external deps |
| Template format | Markdown with placeholders | Compatible with all AI tools |
| Template invocation | Direct code block inclusion | Simplest solution, no processing |

## Data Model

No database changes required. Templates are static content.

### Template Content Structure

```markdown
## Template

Use this structure when creating constitution:

```markdown
# Project Constitution

**Last Updated:** {DATE}
**Project:** {PROJECT_NAME}
...
```
```

## API Design

No API endpoints. This is documentation/command structure only.

## External Dependencies

| Dependency | Version | Purpose |
|------------|---------|---------|
| None | - | All content is internal |

## Security Considerations

- None: Templates are static documentation
- No user input processing
- No code execution

## Performance Considerations

- Minimal: Templates are small (< 5KB each)
- One-time read per command invocation
- No runtime overhead

## Migration Strategy

1. **Embed templates** in command/execution files
2. **Keep `.template.md` files** in repo for reference/editing
3. **Update documentation** to reflect embedded approach
4. **Backward compatible**: Local repo users still have templates

## Rollback Strategy

Revert command files to reference external templates if needed.
