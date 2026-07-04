# bmad-strapi-agent

A [BMAD v6](https://github.com/bmad-code-org/BMAD-METHOD) project providing **Strapi Plugin Forge** — a custom BMAD module that accelerates Strapi v5 plugin development from concept to functional prototype with AI-powered agents and workflows.

## What's Inside

```
.bmad/
├── core/                                # BMAD core (installed)
├── bmb/                                 # BMAD Builder module (installed)
└── custom/modules/strapi-plugin-forge/  # ⭐ The Strapi Plugin Forge module
    ├── agents/                          # 6 specialist agents
    ├── workflows/                       # 11 workflows
    ├── config.yaml                      # Module configuration
    └── _module-installer/               # Installer definition
```

## The Strapi Plugin Forge Module

### Agents

| Agent | Role |
|-------|------|
| plugin-architect | Plugin architecture and orchestration |
| backend-developer (Rashid) | Strapi v5 server-side code |
| frontend-developer (Jamil) | Admin panel UI (Design System v2) |
| test-engineer (Kamal) | Test suites (Jest) |
| code-reviewer (Basir) | Quality, security, performance review |
| technical-writer (Muin) | Documentation |

### Workflows

| Workflow | Type | Purpose |
|----------|------|---------|
| new-plugin-creation | orchestrator | Full plugin: concept → backend → UI → tests → review → docs |
| quick-scaffold | standalone | Fast minimal plugin skeleton |
| admin-panel-designer | standalone | Custom admin interfaces |
| custom-field-generator | standalone | Custom field types |
| plugin-audit | standalone | Audit an existing plugin |
| documentation-generator | standalone | Document an existing plugin |
| generate-backend | subworkflow | Content-types, services, controllers, routes |
| generate-ui | subworkflow | Admin pages, components, API client |
| generate-tests | subworkflow | Unit/integration/component tests |
| review-plugin | subworkflow | Scored code review |
| generate-docs | subworkflow | README, API docs, guides |

All generated code targets **Strapi v5**: Document Service API (`strapi.documents`), Design System v2, React Router v6, `getFetchClient` — no deprecated v4 APIs (`@strapi/helper-plugin`, `entityService`).

## Installation

### Option A — Use this project directly

```bash
git clone https://github.com/ayhid/bmad-strapi-agent.git
cd bmad-strapi-agent
```

Open the project in Claude Code (or your BMAD-compatible agent runtime). Activate BMad Master and run any workflow, e.g.:

```
/bmad:core:agents:bmad-master
```

### Option B — Install the module into an existing BMAD v6 project

1. Copy the module into your project:

   ```bash
   cp -r .bmad/custom/modules/strapi-plugin-forge /path/to/your-project/.bmad/custom/modules/
   ```

2. Create the module config at `.bmad/custom/modules/strapi-plugin-forge/config.yaml` (or re-run the BMAD installer so it processes `_module-installer/install-config.yaml`). Minimum fields:

   ```yaml
   user_name: You
   communication_language: English
   output_folder: '{project-root}/docs'
   strapi_project_path: ''                                # your Strapi v5 app (for testing plugins)
   plugin_output_path: '{project-root}/docs/strapi-plugins'
   code_quality_level: standard                           # standard | strict | enterprise
   template_library_path: '{project-root}/.bmad/strapi-plugin-forge/data/templates'
   strapi_version: v5
   module_data_path: '{project-root}/.bmad/strapi-plugin-forge/data'
   plugin_registry_path: '{project-root}/.bmad/strapi-plugin-forge/data/plugin-registry.json'
   ```

3. Run a workflow by pointing the BMAD workflow engine (`.bmad/core/tasks/workflow.xml`) at the workflow's `workflow.yaml`, or register the module with the BMAD installer to get slash commands.

## Requirements

- BMAD v6 (built against 6.0.0-alpha.11)
- Node.js >=18 <=22 (for generated plugins)
- A Strapi v5 project (to test generated plugins)

## Example Output

A plugin generated end-to-end by the `new-plugin-creation` workflow:
[ayhid/strapi-plugin-quick-notes](https://github.com/ayhid/strapi-plugin-quick-notes) — note content-type, Document Service, admin page, 8 passing tests.

## Quality

The module's 11 workflows were audited against BMAD v6 standards (config compliance, variable alignment, orchestration integrity, bloat) — see the audit reports in [docs/](./docs/). Current status: fully compliant, 0 open findings.

## License

MIT
