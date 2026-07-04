# Strapi Plugin Forge

A [BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD) community module that accelerates **Strapi v5 plugin development** from concept to functional prototype — AI-powered specialist agents and workflows covering architecture, backend, admin UI, tests, code review, and documentation.

- **Speed**: plugin prototyping in ~30 minutes instead of hours
- **Quality**: production-grade TypeScript following official Strapi v5 patterns, not just scaffolding
- **Completeness**: backend, admin panel UI (Strapi Design System), tests, and docs generated together

## Install

In a project that already uses BMAD v6 (or fresh), run the BMAD installer and point it at this repository:

```bash
npx bmad-method install --custom-source https://github.com/ayhid/bmad-strapi-agent
```

Or interactively: run `npx bmad-method install` and add this repo URL as a custom source when the wizard asks.

Then, from your coding agent (Claude Code, Codex, ...), run the setup skill once:

```
> use the strapi-forge-setup skill
```

It asks where your Strapi v5 project lives (optional, used for testing), where generated plugins should be written, and the code-review strictness level (`standard` | `strict` | `enterprise`), then registers the module in `_bmad/config.yaml` and the help catalog.

## Use

Talk to the Plugin Architect and let him orchestrate everything:

```
> use the strapi-forge-agent-architect skill
```

Or invoke a workflow skill directly:

| Skill | What it does |
|-------|--------------|
| `strapi-forge-create-plugin` | Full pipeline: concept → architecture → backend → UI → tests → review → docs |
| `strapi-forge-quick-scaffold` | Fast minimal plugin skeleton (< 5 min) |
| `strapi-forge-generate-backend` | Content types, services, controllers, routes, policies |
| `strapi-forge-generate-ui` | Admin panel UI with Strapi Design System components |
| `strapi-forge-generate-tests` | Unit + integration test suites (Jest) |
| `strapi-forge-review-plugin` | Code review against the configured quality level |
| `strapi-forge-generate-docs` | README, API reference, guides, examples |
| `strapi-forge-plugin-audit` | Deep audit of an existing plugin |
| `strapi-forge-custom-field` | Custom field plugin end-to-end |
| `strapi-forge-admin-panel` | Advanced admin pages and navigation |
| `strapi-forge-document-plugin` | Analyze and document an existing plugin |

### Agents

| Skill | Agent | Role |
|-------|-------|------|
| `strapi-forge-agent-architect` | Nadir | Plugin architecture and orchestration |
| `strapi-forge-agent-backend-dev` | Rashid | Strapi v5 server-side code |
| `strapi-forge-agent-frontend-dev` | Jamil | Admin panel UI (Design System) |
| `strapi-forge-agent-test-engineer` | Kamal | Test suites |
| `strapi-forge-agent-code-reviewer` | Basir | Quality, security, performance review |
| `strapi-forge-agent-tech-writer` | Muin | Documentation |

## Repository layout

```
skills/                       # the module — one folder per skill (SKILL.md + assets)
├── strapi-forge-setup/       # module registration: config + help entries
├── strapi-forge-agent-*/     # 6 specialist agents (SKILL.md + customize.toml)
└── strapi-forge-*/           # 11 workflow skills
.claude-plugin/marketplace.json  # discovery manifest for the BMAD installer
module.yaml                   # module identity + config schema (copy of setup assets)
docs/                         # module brief from the module's design phase
```

Every agent and workflow is customizable per project without touching this repo: overrides live in `{project-root}/_bmad/custom/<skill-name>.toml` (team) and `<skill-name>.user.toml` (personal), merged over each skill's `customize.toml`.

## Requirements

- BMAD Method v6 (current, skills-based) installed in the target project — the skills read shared config from `_bmad/config.yaml`
- A Strapi **v5** project for testing generated plugins (optional but recommended)

## License

MIT — see [LICENSE](LICENSE). The setup merge scripts are adapted from [bmad-loop](https://github.com/bmad-code-org/bmad-loop) (MIT).
