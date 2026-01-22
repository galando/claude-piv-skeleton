# Tasks: Template Distribution for Plugin Users

**Feature ID:** template-distribution-for-plugin-users

## Task Breakdown

---

## Story 1: Constitution Template Embedded

### Tasks
- [ ] 1.1 [P] Read `.claude/commands/piv_loop/constitution.md` and `.claude/memory/constitution.template.md`
- [ ] 1.2 Update constitution.md: embed template content in "Template" section (file: `.claude/commands/piv_loop/constitution.md`)

---

## Story 2: Spec-Kit Templates Embedded

### Tasks
- [ ] 2.1 [P] Read `.claude/reference/execution/plan-feature.md` and all `.claude/specs/.templates/*.md`
- [ ] 2.2 Add "Spec-Kit Templates" section to plan-feature.md with all 4 embedded templates (file: `.claude/reference/execution/plan-feature.md`)

---

## Story 3: Documentation Updated

### Tasks
- [ ] 3.1 Update README.md with "Plugin vs Repo" section (file: `README.md`)
- [ ] 3.2 Update CHANGELOG.md (file: `CHANGELOG.md`)

---

## Cross-Cutting

- [ ] D.1 Verify `/piv-speckit:constitution` works with embedded template
- [ ] D.2 Verify `/piv-speckit:plan-feature` generates artifacts correctly
- [ ] O.1 Bump plugin.json version
