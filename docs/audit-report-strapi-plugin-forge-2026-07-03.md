# Workflow Audit Report — strapi-plugin-forge Module

**Module:** strapi-plugin-forge (`.bmad/custom/modules/strapi-plugin-forge`)
**Workflows Audited:** 11
**Audit Date:** 2026-07-03
**Auditor:** Audit Workflow (BMAD v6) — executed by BMad Master for Ayoub
**Workflow Types:** All 11 are action workflows (`template: false`)

---

## Executive Summary

**Overall Status:** 🔴 NEEDS ATTENTION — one module-level blocker affects every workflow

| Severity | Count | Summary |
|----------|-------|---------|
| CRITICAL | 2 | Missing module `config.yaml` (blocks all 11); `output_folder` absent from standard config block in 10 of 11 |
| IMPORTANT | 3 | Agent delegation in prose without `invoke-workflow` tags; inconsistent plugin path prefixing; Strapi v4 API patterns in v5 code templates |
| BLOAT/CLEANUP | 15 unused fields | 15 of 38 non-standard yaml fields (39%) are never referenced by their instructions |

**What is healthy:** All 11 workflows correctly declare `config_source`, `user_name`, `communication_language`, and `date: system-generated`. All declared files (instructions, checklists) exist on disk. Zero nested-tag-reference violations and zero self-closing `check` antipatterns were found — the XML structure is clean throughout. `plugin-audit` step 9 uses the multi-action `check` wrapper pattern correctly.

---

## 1. CRITICAL Findings (Module-Level)

### 1.1 Module config file does not exist

Every workflow declares:

```yaml
config_source: '{project-root}/.bmad/custom/modules/strapi-plugin-forge/config.yaml'
```

**That file does not exist.** Only `_module-installer/install-config.yaml` (the installer question definition) is present. At runtime, every `{config_source}:...` reference — `plugin_output_path`, `code_quality_level`, `strapi_version`, `template_library_path`, `strapi_project_path`, `plugin_registry_path`, `user_name`, `communication_language` — is unresolvable. **All 11 workflows are currently broken at step 1 of engine initialization.**

**Fix:** Generate the module config (re-run the BMAD installer for this module), or hand-author `.bmad/custom/modules/strapi-plugin-forge/config.yaml` from the fields defined in `install-config.yaml`, e.g.:

```yaml
user_name: Ayoub
communication_language: English
output_folder: '{project-root}/docs'
strapi_project_path: ''
plugin_output_path: '{project-root}/docs/strapi-plugins'
code_quality_level: standard
template_library_path: '{project-root}/.bmad/strapi-plugin-forge/data/templates'
strapi_version: v5
module_data_path: '{project-root}/.bmad/strapi-plugin-forge/data'
plugin_registry_path: '{project-root}/.bmad/strapi-plugin-forge/data/plugin-registry.json'
```

### 1.2 `output_folder` missing from standard config block

Required by the BMAD v6 standard config block, but only **plugin-audit** defines it. The other 10 workflows omit it. For pure code-generation workflows this is arguably intentional (they write to `plugin_output_path`), but the standard requires it and any workflow that ever writes a report or document will silently lack a destination.

---

## 2. IMPORTANT Findings (Cross-Cutting)

### 2.1 Agent delegation is prose, not orchestration

Five workflows define sub-workflow path variables and then never use them. The instructions instead say things like *"Invoke Jamil (Frontend Developer) to generate admin pages"* — plain prose with **no `invoke-workflow` tag**. The workflow engine has no mechanism to act on prose delegation; the LLM will improvise instead of formally executing the sub-workflow.

| Workflow | Unused sub-workflow variables |
|----------|------------------------------|
| new-plugin-creation | `backend_workflow`, `frontend_workflow`, `test_workflow`, `review_workflow`, `docs_workflow` (5) |
| admin-panel-designer | `frontend_workflow` |
| custom-field-generator | `frontend_workflow` |
| documentation-generator | `docs_workflow` |
| plugin-audit | `review_workflow` |

**Fix:** In each delegating step, replace the prose with a real invocation, e.g. in new-plugin-creation step 3:

```xml
<invoke-workflow path="{backend_workflow}">
  Pass: plugin_requirements, plugin_architecture, output_path, code_quality_level, strapi_version
</invoke-workflow>
```

### 2.2 Inconsistent plugin directory naming

- `new-plugin-creation` requires the captured name to already start with `strapi-plugin-` and writes to `{plugin_output_path}/{{plugin_name}}/`.
- `quick-scaffold`, `admin-panel-designer`, `custom-field-generator` write to `{plugin_output_path}/strapi-plugin-{{plugin_name}}/` (prefix prepended).

If these workflows are chained on the same plugin, they will target **different directories** (`strapi-plugin-strapi-plugin-x` double-prefix risk). Standardize on one convention module-wide.

### 2.3 Strapi v4 patterns inside v5 code templates (content-level)

The module targets Strapi v5 (`strapi_version: v5`), yet the embedded code templates use APIs removed in v5:

- `@strapi/helper-plugin` (removed in Strapi v5) — generate-ui, admin-panel-designer, custom-field-generator
- `react-router-dom` v5 API (`Switch`, `component=`) — Strapi v5 admin uses React Router v6 (`Routes`/`element`)
- `strapi.entityService` — deprecated in favor of the Document Service API in Strapi v5

This is outside strict BMAD-structure scope but directly undermines the module's stated purpose; generated plugins will not build against Strapi v5.

---

## 3. Per-Workflow Audit Results

Legend: config block = standard variables present (excluding the module-wide findings above). Bloat % = unused ÷ non-standard yaml fields.

### 3.1 admin-panel-designer (standalone)
- **Config block:** ✅ (except `output_folder`) · **Checklist:** ✅ exists
- **Alignment:** `plugin_output_path` ✅, `code_quality_level` ✅ · **Unused:** `strapi_version`, `frontend_workflow` → **bloat 2/4 (50%)**
- **Config usage:** `{communication_language}` ✅ header · `{user_name}` ✅ step 11 · antipatterns: none

### 3.2 custom-field-generator (standalone)
- **Config block:** ✅ (except `output_folder`) · **Checklist:** ✅ exists
- **Alignment:** `plugin_output_path` ✅, `code_quality_level` ✅ · **Unused:** `strapi_version`, `frontend_workflow` → **bloat 2/4 (50%)**
- **Config usage:** ✅ · antipatterns: none

### 3.3 documentation-generator (standalone)
- **Config block:** ✅ (except `output_folder`) · **Checklist:** ✅ exists
- **Alignment:** **Unused:** `docs_workflow` → **bloat 1/1 (100%)**
- **Design note:** Steps 7–11 regenerate README/API/CONFIGURATION/CONTRIBUTING/CHANGELOG **inline**, duplicating nearly all of `generate-docs` — which step 6 claims to delegate to. Choose one: delegate (preferred) and delete steps 7–11, or drop the delegation step. ~350 lines of redundant instruction bloat.
- **Config usage:** ✅ `{user_name}` step 12, `{{date}}` used

### 3.4 generate-backend (subworkflow, standalone: false)
- **Config block:** ✅ (except `output_folder`)  · **Checklist:** none declared (consistent)
- **Alignment:** `strapi_version` ✅, `code_quality_level` ✅ · **Unused:** `template_library_path` → **bloat 1/3 (33%)**
- **Config usage:** `{user_name}` unused (acceptable for a subworkflow) · conditional `action if=` used correctly

### 3.5 generate-docs (subworkflow)
- **Config block:** ✅ (except `output_folder`) · **Alignment:** no non-standard fields → **bloat 0%** ✅ cleanest yaml in the module
- **Config usage:** ✅

### 3.6 generate-tests (subworkflow)
- **Config block:** ✅ (except `output_folder`) · **Alignment:** `code_quality_level` ✅ → **bloat 0%** ✅
- **Config usage:** ✅

### 3.7 generate-ui (subworkflow)
- **Config block:** ✅ (except `output_folder`) · **Alignment:** `code_quality_level` ✅ · **Unused:** `template_library_path` → **bloat 1/2 (50%)**
- **Content:** heaviest concentration of Strapi v4 APIs (see 2.3)

### 3.8 new-plugin-creation (standalone orchestrator)
- **Config block:** ✅ (except `output_folder`) · **Checklist:** ✅ exists · README ✅
- **Alignment:** `strapi_project_path` ✅, `plugin_output_path` ✅, `code_quality_level` ✅, `strapi_version` ✅, `plugin_registry_path` ✅ · **Unused:** `template_library_path` + all 5 sub-workflow paths → **bloat 6/11 (55%)**
- **Config usage:** ✅ exemplary `{user_name}` usage · step 9 `ask`/conditional actions well-formed
- **Key issue:** the entire orchestration premise (finding 2.1) — five specialist phases, zero `invoke-workflow` tags

### 3.9 plugin-audit (standalone)
- **Config block:** ✅ **fully compliant — only workflow with `output_folder`** · **Checklist:** ✅ exists
- **Alignment:** `code_quality_level` ✅, `strapi_version` ✅, `output_folder` ✅ · **Unused:** `review_workflow` → **bloat 1/4 (25%)**
- **Config usage:** ✅ · step 9 `check` wrapper blocks with closing tags — **model example of the correct conditional pattern**

### 3.10 quick-scaffold (standalone)
- **Config block:** ✅ (except `output_folder`) · **Checklist:** ✅ exists
- **Alignment:** `plugin_output_path` ✅, `template_library_path` ✅ · **Unused:** `strapi_version` → **bloat 1/3 (33%)**
- **Hardcoded values:** `"@strapi/strapi": "^5.0.0"` and Node engine ranges hardcoded in the package.json template while `strapi_version` sits unused — wire the variable in
- **Syntax inconsistency:** config variable written as `{{user_name}}` (double braces, runtime-template syntax) in the package.json template; config variables use single braces

### 3.11 review-plugin (subworkflow)
- **Config block:** ✅ (except `output_folder`)
- **Alignment:** `code_quality_level` ✅, `strapi_version` ✅ → **bloat 0%** ✅
- **Config usage:** ✅ · scoring rubric (step 8) is well specified

---

## 4. Web Bundle Validation

No workflow defines a `web_bundle` section. **0 of 11.** For a locally-installed custom module this is acceptable (local-only workflows), but none of these workflows can be deployed as web bundles until the section — and its `existing_workflows` mapping for the orchestrated sub-workflows — is added. Recorded as informational, not a defect.

---

## 5. Bloat Metrics (Module Aggregate)

| Metric | Value |
|--------|-------|
| Non-standard yaml fields across 11 workflows | 38 |
| Used in instructions | 23 |
| Unused (bloat) | **15 (39%)** |
| Largest offenders | new-plugin-creation (6), admin-panel-designer (2), custom-field-generator (2) |
| Redundant instruction content | documentation-generator steps 7–11 duplicate generate-docs (~350 lines) |

Note: most "unused" fields are the sub-workflow path variables — they become *used* (and the bloat rate drops to ~13%) once finding 2.1 is fixed by adding `invoke-workflow` tags. Fixing 2.1 resolves most of section 5 automatically.

---

## 6. Template Variable Mapping

Not applicable — all 11 workflows are action workflows (`template: false`); none has a `template.md`. The `{{double_brace}}` placeholders inside instructions are runtime session variables, which is the correct pattern for action workflows.

---

## Recommendations

### Critical (Fix Immediately)
1. **Create `.bmad/custom/modules/strapi-plugin-forge/config.yaml`** (re-run installer or hand-author from `install-config.yaml`) — unblocks all 11 workflows.
2. **Add `output_folder: '{config_source}:output_folder'`** to the 10 workflows missing it.

### Important (Address Soon)
3. **Convert prose delegation to `invoke-workflow` tags** in new-plugin-creation (5×), admin-panel-designer, custom-field-generator, documentation-generator, and plugin-audit — this also eliminates 9 of the 15 bloat findings.
4. **Standardize plugin directory naming** (`strapi-plugin-` prefix handling) across quick-scaffold, new-plugin-creation, admin-panel-designer, custom-field-generator.
5. **Upgrade code templates to real Strapi v5 APIs**: replace `@strapi/helper-plugin`, React Router v5 patterns, and `entityService` with their v5 equivalents (Design System v2 / `@strapi/strapi/admin`, React Router v6, Document Service API).

### Cleanup (Nice to Have)
6. Remove genuinely unused fields: `strapi_version` (admin-panel-designer, custom-field-generator, or start using it), `template_library_path` (generate-backend, generate-ui, new-plugin-creation — or wire it into template selection).
7. Deduplicate documentation-generator vs generate-docs (delegate, then delete inline steps 7–11).
8. Fix `{{user_name}}` → `{user_name}` brace syntax in quick-scaffold; wire `strapi_version` into its hardcoded package.json version strings.
9. Add checklists (`validation:`) to the five subworkflows if validation gates are desired, and consider `web_bundle` sections if web deployment is ever intended.

---

## Validation Checklist

- [ ] Module config.yaml exists and resolves all `{config_source}:` references
- [ ] All standard config variables present in all 11 workflows
- [ ] All sub-workflow variables consumed via `invoke-workflow`
- [ ] Consistent plugin path convention module-wide
- [ ] No unused yaml fields remaining
- [ ] Re-run audit after fixes to verify improvements

---

**Audit Complete** — Generated by audit-workflow v1.0 (batch mode, 11 workflows)
