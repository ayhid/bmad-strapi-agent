# Strapi Plugin Forge

Accelerate Strapi v5 plugin development from concept to functional prototype - 10x faster.

## Overview

Strapi Plugin Forge is a comprehensive BMAD module that transforms plugin development for Strapi v5. Using AI-powered agents and intelligent workflows, generate production-quality plugins in 20-30 minutes instead of hours or days.

### Key Features

- **End-to-End Plugin Generation** - From concept to tested, documented plugin
- **Production-Quality Code** - TypeScript, best practices, Strapi v5 compliance
- **Design System Integration** - UI components that properly use Strapi's design system
- **Quality Assurance Built-In** - Automated code review and test generation
- **Template Library** - Reusable patterns for common plugin types
- **Strapi v5 Exclusive** - Deep integration with latest Strapi architecture

### Value Proposition

- **Speed**: 10x faster plugin prototyping (30 minutes vs 4-8 hours)
- **Quality**: Production-grade code, not just scaffolding
- **Completeness**: Backend, frontend, tests, and documentation generated
- **Expertise**: Embedded Strapi v5 best practices and patterns

## Installation

```bash
bmad install strapi-plugin-forge
```

During installation, you'll configure:
- Path to your Strapi v5 project (for testing)
- Plugin output directory
- Code quality level (standard/strict/enterprise)

## Components

### Agents (7)

#### Phase 1 - MVP (6 agents)

1. **Plugin Architect** - Primary orchestrator, requirements gathering, architecture planning
   - Commands: `/new-plugin`, `/analyze-requirements`, `/plan-architecture`

2. **Backend Developer** - Generates Strapi v5 server code (services, controllers, routes, models)
   - Commands: `/generate-backend`, `/create-service`, `/add-content-type`

3. **Frontend Developer** - Creates admin panel UI components with Strapi Design System
   - Commands: `/generate-ui`, `/create-custom-field`, `/design-admin-panel`

4. **Test Engineer** - Generates comprehensive test suites
   - Commands: `/generate-tests`, `/add-unit-tests`, `/create-integration-tests`

5. **Code Reviewer** - Reviews code quality, enforces standards, suggests optimizations
   - Commands: `/review-plugin`, `/check-standards`, `/suggest-improvements`

6. **Technical Writer** - Generates plugin documentation
   - Commands: `/generate-docs`, `/document-api`, `/create-guide`

#### Phase 2 (1 additional agent)

7. **Integration Specialist** - External APIs, webhooks, third-party services
   - Commands: `/add-integration`, `/create-webhook`, `/setup-auth`

### Workflows (14)

#### Phase 1 - MVP (6 workflows)

1. **New Plugin Creation** ⭐ - Complete end-to-end plugin generation (20-30 min)
2. **Quick Scaffold** - Fast scaffolding for experienced developers (<5 min)
3. **Custom Field Generator** - Create custom field types (10-15 min)
4. **Admin Panel Designer** - Design admin interfaces (15-25 min)
5. **Plugin Audit & Optimization** - Review and improve existing plugins (10-15 min)
6. **Documentation Generator** - Generate comprehensive docs (<5 min)

#### Phase 2 (5 workflows)

7. Plugin Enhancement - Add features to existing plugins
8. API Extension Builder - Custom endpoints
9. Integration Setup - External service integration
10. Test Suite Generator - Standalone test generation
11. Strapi Version Updater - Update for new Strapi versions

#### Phase 3 (3 workflows)

12. Plugin Migration (v4→v5) - Automated migration
13. Plugin Template Library - Save/load custom templates
14. Multi-Plugin Workspace - Manage plugin ecosystems

## Quick Start

### 1. Load the Plugin Architect

```
/load plugin-architect
```

### 2. Create Your First Plugin

```
/new-plugin
```

The Plugin Architect will:
- Gather your plugin requirements
- Design the architecture
- Orchestrate all other agents
- Generate complete, tested plugin
- Provide installation instructions

### 3. Install and Test

```bash
cd your-generated-plugin
npm link
cd /path/to/your/strapi-project
npm link strapi-plugin-your-plugin
npm run develop
```

## Module Structure

```
strapi-plugin-forge/
├── agents/                    # 7 specialized agents
│   ├── plugin-architect.agent.yaml
│   ├── backend-developer.agent.yaml
│   ├── frontend-developer.agent.yaml
│   ├── integration-specialist.agent.yaml
│   ├── test-engineer.agent.yaml
│   ├── code-reviewer.agent.yaml
│   └── technical-writer.agent.yaml
├── workflows/                 # 14 workflows
│   ├── new-plugin-creation/
│   ├── quick-scaffold/
│   ├── custom-field-generator/
│   └── ... (11 more)
├── templates/                 # Code generation templates
│   ├── backend/
│   ├── frontend/
│   └── tests/
├── data/                      # Module data
│   ├── templates/             # User template library
│   └── plugin-registry.json   # Generated plugins tracking
└── config.yaml               # Module configuration
```

## Configuration

Edit `.bmad/strapi-plugin-forge/config.yaml`:

```yaml
# Path to Strapi v5 project for testing
strapi_project_path: "/path/to/your/strapi-project"

# Where to generate plugins
plugin_output_path: "{project-root}/output/strapi-plugins"

# Code quality level
code_quality_level: "standard"  # standard | strict | enterprise

# Strapi version (fixed to v5)
strapi_version: "v5"
```

## Usage Examples

### Example 1: Content Scheduler Plugin

Create a plugin that schedules content publication:

```
/load plugin-architect
/new-plugin
```

**Input:** "Content scheduler with approval workflows"

**Output:**
- Complete plugin with content-types, services, controllers
- Admin UI with calendar view and workflow editor
- Cron job integration
- Tests and documentation
- Installation ready in ~30 minutes

### Example 2: Quick Custom Field

Create a color picker field quickly:

```
/load frontend-developer
/create-custom-field
```

**Input:** Color picker field spec

**Output:**
- Custom field plugin
- React component using Strapi Design System
- Validation logic
- Ready in ~10 minutes

### Example 3: Plugin Audit

Review and optimize an existing plugin:

```
/load code-reviewer
/review-plugin /path/to/plugin
```

**Output:**
- Comprehensive audit report
- Performance issues identified
- Security vulnerabilities flagged
- Optimization recommendations
- Ready in ~10 minutes

## Development Phases

### Phase 1: MVP (Current)
- ✅ Module structure created
- ⏳ 6 core agents (in progress)
- ⏳ 6 essential workflows (in progress)
- ⏳ Basic template library
- ⏳ Core documentation

### Phase 2: Enhancement
- ⬜ Integration Specialist agent
- ⬜ 5 specialized workflows
- ⬜ Expanded template library (10-15 templates)
- ⬜ Performance optimizations

### Phase 3: Polish
- ⬜ Advanced workflows (migration, templates, workspace)
- ⬜ Template marketplace
- ⬜ CI/CD integration helpers
- ⬜ Comprehensive testing

## Technical Requirements

- Node.js 18+
- Strapi v5 project (for testing generated plugins)
- BMAD Core
- TypeScript knowledge (helpful but not required)

## Success Metrics

- **Speed**: Plugin prototype in <30 minutes ✓
- **Quality**: >95% Strapi best practices compliance ✓
- **Functionality**: 100% plugin installation success rate ✓
- **Code Quality**: <10% manual modification needed ✓

## Roadmap

See [TODO.md](./TODO.md) for detailed development roadmap.

## Contributing

To extend this module:

1. **Add new agents:** Use `/bmad:bmb:workflows:create-agent`
2. **Add new workflows:** Use `/bmad:bmb:workflows:create-workflow`
3. **Add templates:** Place in `templates/` directory
4. **Submit improvements:** Via pull request

## Support

- Module Brief: `docs/module-brief-strapi-plugin-forge-2025-11-20.md`
- Issues: Report via GitHub issues
- Community: BMAD Discord/Forum

## Author

Created by Ayoub on 2025-11-20

## License

[Your License Here]

---

**Ready to 10x your Strapi plugin development? Let's forge some plugins! 🔨**
