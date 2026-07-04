# New Plugin Creation Workflow

**Complete Strapi v5 plugin generation from concept to functional prototype**

## Purpose

This is the flagship workflow of the Strapi Plugin Forge module. It orchestrates all specialized agents to generate a complete, production-quality Strapi v5 plugin including backend code, admin UI, tests, code review, and documentation.

## How to Invoke

### From Plugin Architect Agent (Nadir)
```
/load plugin-architect
/new-plugin
```

### Direct Invocation
```
workflow {project-root}/.bmad/custom/modules/strapi-plugin-forge/workflows/new-plugin-creation/
```

## Expected Inputs

**User provides during execution:**
- Plugin concept and requirements
- Architectural decisions and preferences
- Feedback at key milestones

**From module configuration:**
- `strapi_project_path` - Strapi v5 project for testing
- `plugin_output_path` - Where to generate plugins
- `code_quality_level` - standard/strict/enterprise

## Generated Outputs

**Complete plugin structure:**
```
{plugin_output_path}/{plugin-name}/
├── server/              # Backend (Rashid - Backend Developer)
│   ├── content-types/
│   ├── services/
│   ├── controllers/
│   └── routes/
├── admin/               # Frontend (Jamil - Frontend Developer)
│   ├── src/
│   │   ├── pages/
│   │   └── components/
├── tests/               # Tests (Kamal - Test Engineer)
│   ├── unit/
│   ├── integration/
│   └── fixtures/
├── README.md            # Docs (Muin - Technical Writer)
├── package.json
└── LICENSE
```

## Workflow Phases

1. **Discovery & Requirements** - Collaborative exploration of plugin concept
2. **Architecture Planning** - Design data models, services, UI structure
3. **Backend Generation** - Rashid generates Strapi v5 server code
4. **Frontend Generation** - Jamil creates admin UI with Design System
5. **Test Generation** - Kamal builds comprehensive test suite
6. **Code Review** - Basir validates quality and security
7. **Documentation** - Muin generates complete docs
8. **Assembly & Testing** - Final plugin ready for installation

## Agent Orchestration

This meta-workflow coordinates:
- **Nadir** (Plugin Architect) - Orchestrates the entire process
- **Rashid** (Backend Developer) - Server-side code generation
- **Jamil** (Frontend Developer) - Admin UI components
- **Kamal** (Test Engineer) - Test suite generation
- **Basir** (Code Reviewer) - Quality assurance
- **Muin** (Technical Writer) - Documentation generation

## Interaction Style

**Primary Style:** Intent-Based (Adaptive)
- Open-ended discovery in requirements and architecture phases
- Collaborative decision-making throughout
- Adapts to user expertise and communication style

**Interactivity Level:** High (Collaborative)
- Constant user involvement in key decisions
- Iterative refinement of requirements and architecture
- Review checkpoints after each generation phase

## Expected Duration

Approximately 20-30 minutes for complete plugin generation, depending on complexity.

## Requirements

- Strapi v5 project configured in module settings
- Node.js 18+ installed
- All agent workflows available (will be created in Phase 1)

## Quality Standards

Generated plugins meet these standards:
- Production-quality code (not scaffolding)
- Strapi v5 best practices compliance
- TypeScript with strict typing
- >80% test coverage
- Complete documentation
- Security validated
- Performance optimized

## Special Requirements

**Agent Workflows (To Be Created):**
- `generate-backend/workflow.yaml` (Rashid)
- `generate-ui/workflow.yaml` (Jamil)
- `generate-tests/workflow.yaml` (Kamal)
- `review-plugin/workflow.yaml` (Basir)
- `generate-docs/workflow.yaml` (Muin)

These agent-specific workflows will be created as part of Phase 1 MVP completion.

## Notes

- This is a meta-workflow that coordinates other workflows
- Requires all 5 MVP agent workflows to be functional
- Highly interactive - user guides the plugin design
- Produces ready-to-install Strapi v5 plugins
- Plugin registry tracking enabled
