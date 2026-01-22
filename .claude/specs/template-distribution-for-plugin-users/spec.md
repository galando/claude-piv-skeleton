# Spec: Template Distribution for Plugin Users

**ID:** template-distribution-for-plugin-users
**Status:** DRAFT
**Created:** 2025-01-22

## Overview

Commands in the PIV-SpecKit plugin reference template files that are NOT included in the plugin distribution. When users install the plugin from GitHub, these template files don't exist, causing commands to fail or reference missing files.

## User Stories

### Story 1: Constitution Command Template Availability
**As a** plugin user
**I want** the `/piv-speckit:constitution` command to work without manual setup
**So that** I can create a project constitution without errors

**Acceptance Criteria:**
- [ ] Constitution command contains embedded template content
- [ ] No external file references for templates
- [ ] Command works immediately after plugin install

### Story 2: Spec-Kit Templates Available for Planning
**As a** plugin user
**I want** the `/piv-speckit:plan-feature` command to access templates
**So that** split artifacts (spec/plan/tasks/quickstart) can be generated

**Acceptance Criteria:**
- [ ] Plan-feature command can generate all 4 artifacts
- [ ] Templates are accessible during plan generation
- [ ] No "template file not found" errors

### Story 3: Clear Documentation for Plugin Users
**As a** plugin user
**I want** to understand which commands work out-of-the-box
**So that** I know what to expect when using the plugin

**Acceptance Criteria:**
- [ ] README documents which features require local files
- [ ] Clear distinction between plugin and repo-only features
- [ ] Migration guide for users wanting full functionality

## Functional Requirements

| ID | Requirement | Priority |
|----|-------------|----------|
| FR1 | Embed constitution template in command file | HIGH |
| FR2 | Embed Spec-Kit templates in plan-feature execution reference | HIGH |
| FR3 | Update command documentation to reflect embedded templates | MEDIUM |
| FR4 | Document plugin limitations in README | MEDIUM |

## User Workflows

### Workflow 1: Creating Constitution (Plugin User)

1. User installs plugin: `/plugin install galando/piv-speckit`
2. User runs: `/piv-speckit:constitution`
3. Claude reads embedded template from command file
4. Claude generates constitution at `.claude/memory/constitution.md`

### Workflow 2: Planning Feature (Plugin User)

1. User runs: `/piv-speckit:plan-feature "my feature"`
2. Claude reads `.claude/reference/execution/plan-feature.md`
3. Claude generates split artifacts using embedded templates
4. Artifacts created at `.claude/specs/{XXX-feature-name}/`

## Edge Cases

- User has local repo clone with existing templates (should still work)
- User installs plugin in multiple projects (templates work in all)
- Templates need updates (plugin version required)

## Non-Functional Requirements

- Performance: No performance impact (templates are small text)
- Security: No security implications (templates are documentation)
- Accessibility: Templates must be readable by any AI tool

## Out of Scope

- Template versioning/migration (deferred)
- Template customization by users (can edit local files)
- Dynamic template loading (not supported by Claude Code plugin spec)
