# New Plugin Creation - Complete Strapi v5 Plugin Generation

<critical>The workflow execution engine is governed by: {project-root}/.bmad/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {project-root}/.bmad/custom/modules/strapi-plugin-forge/workflows/new-plugin-creation/workflow.yaml</critical>
<critical>Communicate in {communication_language} throughout the workflow execution process</critical>
<critical>This workflow orchestrates multiple agent workflows to generate a complete, production-quality Strapi v5 plugin</critical>

<workflow>

<step n="0" goal="Welcome and context setting">
<action>Greet {user_name} and explain the workflow:

This workflow will guide you through creating a complete Strapi v5 plugin from concept to functional prototype. We'll work together through several phases:

1. **Discovery & Requirements** - Understand your plugin vision
2. **Architecture Planning** - Design the structure and components
3. **Backend Generation** - Create server-side code (Rashid)
4. **Frontend Generation** - Build admin UI (Jamil)
5. **Test Generation** - Comprehensive test coverage (Kamal)
6. **Code Review** - Quality assurance (Basir)
7. **Documentation** - Complete docs (Muin)
8. **Assembly & Testing** - Final plugin ready for use

The process is highly collaborative - your input shapes the plugin at every stage.

Expected outcome: A production-ready Strapi v5 plugin with backend, frontend, tests, and documentation.
</action>

<action>Verify configuration:
- Plugin output path: {plugin_output_path}
- Strapi project for testing: {strapi_project_path}
- Code quality level: {code_quality_level}
- Strapi version: {strapi_version}
</action>

<ask>Ready to begin? [y/n]</ask>
</step>

<step n="1" goal="Discovery and requirements gathering">
<action>Engage in collaborative discovery to understand the plugin vision:

**Core Plugin Concept:**
- What problem does this plugin solve?
- Who are the primary users (content editors, developers, admins)?
- What's the unique value this plugin provides?

**Key Questions to Explore:**
- What functionality is essential vs. nice-to-have?
- Are there existing Strapi plugins this is similar to or extends?
- What data needs to be stored (content-types, components)?
- What admin UI is needed (custom pages, custom fields, settings)?
- Does it integrate with external services (APIs, webhooks)?
- What's the plugin scope - simple utility or complex feature?

**Listen for clues about:**
- Data models and relationships
- API endpoints needed
- UI components and workflows
- Integration requirements
- Performance considerations
- Security requirements

Adapt your depth based on the user's responses:
- If brief answers, dig deeper with follow-ups
- If highly technical, explore implementation details
- If conceptual, help them think through specifics
- Use examples from popular Strapi plugins to clarify

Document the plugin concept comprehensively.
</action>

<action>Capture essential metadata:
- Plugin name (kebab-case, must start with "strapi-plugin-")
- Plugin display name (for admin UI)
- Short description (one sentence)
- Plugin category (content-management, integration, utility, seo, media, etc.)
</action>

<action>Store requirements as {{plugin_requirements}} including:
- Plugin name and metadata
- Core functionality description
- User stories or use cases
- Data model needs
- UI requirements
- Integration needs
- Special considerations
</action>
</step>

<step n="2" goal="Architecture planning and design">
<action>Collaborate on plugin architecture using the requirements from Step 1:

**Data Model Design:**
- What content-types are needed? (e.g., "scheduled-publication", "seo-meta")
- What components are needed? (repeatable structures)
- What relations exist between entities?
- What fields and field types for each content-type?
- Any custom field types needed?

**Service Layer Design:**
- What business logic is required?
- What services need to be created? (e.g., "scheduling-service", "notification-service")
- What operations does each service expose?
- Any lifecycle hooks needed? (beforeCreate, afterUpdate, etc.)

**API Layer Design:**
- What custom routes/endpoints beyond standard CRUD?
- Any custom controllers?
- What permissions and policies?
- Any middleware needed?

**Admin UI Design:**
- Custom admin pages needed? (dashboard, settings, tools)
- Custom fields for content editor?
- Settings panel configuration?
- Any special UI components or views?

**Integration Points:**
- External APIs to integrate?
- Webhooks to handle?
- Cron jobs or scheduled tasks?
- Email notifications?

Work through each area systematically. For complex plugins, sketch out the architecture collaboratively. For simple plugins, this can be quick.

Use architectural best practices:
- Single responsibility per service
- Clean separation of concerns
- Proper error handling patterns
- Strapi v5 conventions
</action>

<action>Create detailed architecture blueprint as {{plugin_architecture}} including:
- Complete data model (content-types, components, relations)
- Service layer design (services, methods, responsibilities)
- API layer design (routes, controllers, policies)
- Admin UI structure (pages, components, navigation)
- Integration architecture (external services, webhooks, jobs)
- File/folder structure for the plugin
</action>

<action>Review architecture with user and refine until approved</action>
</step>

<step n="3" goal="Generate backend code">
<action>Explain that Rashid (Backend Developer) will now generate the server-side code based on the architecture</action>

<invoke-workflow path="{backend_workflow}">
Pass to Rashid (generate-backend workflow):
- Plugin requirements: {{plugin_requirements}}
- Architecture blueprint: {{plugin_architecture}}
- Output path: {plugin_output_path}/{{plugin_name}}/server/
- Code quality level: {code_quality_level}
- Strapi version: {strapi_version}
</invoke-workflow>

<action>Rashid generates:
- Content-type definitions (schema.json files)
- Component definitions
- Service layer (services/*.js with business logic)
- Controller layer (controllers/*.js for API endpoints)
- Routes (routes/*.js defining endpoints)
- Plugin configuration (strapi-server.js bootstrap, lifecycle hooks)
- Middleware if needed
- Policies if needed
</action>

<action>Store backend generation output as {{backend_code_path}}</action>

<action>Report to user:
- Number of content-types created
- Number of services created
- Number of controllers created
- Number of routes defined
- Any special configurations added
</action>
</step>

<step n="4" goal="Generate frontend UI code">
<action>Explain that Jamil (Frontend Developer) will now generate the admin panel UI using Strapi Design System</action>

<invoke-workflow path="{frontend_workflow}">
Pass to Jamil (generate-ui workflow):
- Plugin requirements: {{plugin_requirements}}
- Architecture blueprint: {{plugin_architecture}}
- Backend structure: {{backend_code_path}}
- Output path: {plugin_output_path}/{{plugin_name}}/admin/
- Code quality level: {code_quality_level}
</invoke-workflow>

<action>Jamil generates:
- Admin panel pages (src/pages/*.jsx)
- Custom field components (if needed)
- Settings panel (if needed)
- Reusable UI components (src/components/*.jsx)
- Admin routing configuration
- State management setup
- Strapi Design System integration
- Plugin admin configuration (strapi-admin.js)
</action>

<action>Store frontend generation output as {{frontend_code_path}}</action>

<action>Report to user:
- Number of pages created
- Number of components created
- Custom fields created
- Settings panels added
- Design System components used
</action>
</step>

<step n="5" goal="Generate comprehensive test suite">
<action>Explain that Kamal (Test Engineer) will now generate tests for the plugin</action>

<invoke-workflow path="{test_workflow}">
Pass to Kamal (generate-tests workflow):
- Plugin requirements: {{plugin_requirements}}
- Architecture blueprint: {{plugin_architecture}}
- Backend code: {{backend_code_path}}
- Frontend code: {{frontend_code_path}}
- Output path: {plugin_output_path}/{{plugin_name}}/tests/
- Code quality level: {code_quality_level}
</invoke-workflow>

<action>Kamal generates:
- Unit tests for services (tests/unit/services/*.test.js)
- Unit tests for controllers (tests/unit/controllers/*.test.js)
- Integration tests for API endpoints (tests/integration/api/*.test.js)
- Component tests for UI (tests/components/*.test.jsx)
- Test fixtures and mocks (tests/fixtures/*)
- Test configuration (jest.config.js, test setup)
</action>

<action>Store test generation output as {{test_code_path}}</action>

<action>Report to user:
- Test coverage percentage target
- Number of unit tests created
- Number of integration tests created
- Number of component tests created
- Test fixtures provided
</action>
</step>

<step n="6" goal="Code review and quality assurance">
<action>Explain that Basir (Code Reviewer) will now review all generated code for quality, security, and best practices</action>

<invoke-workflow path="{review_workflow}">
Pass to Basir (review-plugin workflow):
- Complete plugin code at: {plugin_output_path}/{{plugin_name}}/
- Code quality level: {code_quality_level}
- Architecture blueprint: {{plugin_architecture}}
- Review scope: Full plugin (backend, frontend, tests)
</invoke-workflow>

<action>Basir performs:
- Strapi v5 best practices validation
- Security audit (SQL injection, XSS, auth issues)
- Performance analysis (N+1 queries, memory leaks)
- Code quality check (maintainability, readability)
- Design System compliance (UI components)
- Test coverage validation
- Configuration correctness
</action>

<action>Basir produces review report with:
- Overall quality score
- Issues found (critical, major, minor)
- Security vulnerabilities identified
- Performance bottlenecks
- Recommendations for improvements
- Required fixes vs. optional improvements
</action>

<action>Store code review report as {{code_review_report}}</action>

<action>If critical or major issues found:
- Present issues to user
- Ask if they want to fix now or note for later
- If fix now: Apply fixes and re-review affected areas
</action>

<action>Report final code quality status to user</action>
</step>

<step n="7" goal="Generate documentation">
<action>Explain that Muin (Technical Writer) will now generate complete plugin documentation</action>

<invoke-workflow path="{docs_workflow}">
Pass to Muin (generate-docs workflow):
- Plugin requirements: {{plugin_requirements}}
- Architecture blueprint: {{plugin_architecture}}
- Complete plugin code: {plugin_output_path}/{{plugin_name}}/
- Code review report: {{code_review_report}}
- Output path: {plugin_output_path}/{{plugin_name}}/
</invoke-workflow>

<action>Muin generates:
- README.md (overview, installation, quick start)
- API documentation (endpoints, parameters, responses)
- Usage guides (common use cases, examples)
- Configuration guide (settings, options)
- Troubleshooting section (common issues, solutions)
- Development guide (for contributors)
- Changelog.md (initial version entry)
- package.json with proper metadata
- LICENSE file
</action>

<action>Store documentation output as {{documentation_path}}</action>

<action>Report to user:
- Documentation files created
- API endpoints documented
- Usage examples included
- Configuration options documented
</action>
</step>

<step n="8" goal="Plugin assembly and finalization">
<action>Assemble the complete plugin:

**Plugin Structure Created:**
```
{plugin_output_path}/{{plugin_name}}/
├── server/              # Backend code (Rashid)
│   ├── content-types/
│   ├── services/
│   ├── controllers/
│   ├── routes/
│   └── index.js
├── admin/               # Frontend code (Jamil)
│   ├── src/
│   │   ├── pages/
│   │   ├── components/
│   │   └── index.js
├── tests/               # Test suite (Kamal)
│   ├── unit/
│   ├── integration/
│   └── fixtures/
├── README.md            # Documentation (Muin)
├── package.json
├── LICENSE
└── .gitignore
```
</action>

<action>Generate final package.json with:
- Correct plugin name
- Version (1.0.0)
- Dependencies (Strapi v5 peer dependencies)
- Scripts (test, build, etc.)
- Author: {user_name}
- Keywords for Strapi plugin registry
</action>

<action>Create .gitignore appropriate for Strapi plugins</action>

<action>Update plugin registry at {plugin_registry_path}:
- Add entry for this plugin
- Record creation date
- Store metadata (name, type, agents used)
</action>

<action>Generate installation instructions for user:

**Installation & Testing:**

1. **Link plugin to your Strapi project:**
   ```bash
   cd {plugin_output_path}/{{plugin_name}}
   npm install
   npm link

   cd {strapi_project_path}
   npm link {{plugin_name}}
   ```

2. **Start Strapi in development mode:**
   ```bash
   cd {strapi_project_path}
   npm run develop
   ```

3. **Access the plugin:**
   - Navigate to Strapi admin panel
   - Plugin should appear in the sidebar
   - Test all functionality

4. **Run tests:**
   ```bash
   cd {plugin_output_path}/{{plugin_name}}
   npm test
   ```

**Next Steps:**
- Test all plugin features in Strapi admin
- Review generated documentation
- Customize as needed
- Consider publishing to npm registry
</action>

<action>Present final summary to {user_name}:

**Plugin Generation Complete! 🎉**

**Generated Plugin:** {{plugin_name}}
**Location:** {plugin_output_path}/{{plugin_name}}/

**What was created:**
- ✅ Backend (Content-types, Services, Controllers, Routes)
- ✅ Frontend (Admin UI, Components, Pages)
- ✅ Tests (Unit, Integration, Component tests)
- ✅ Documentation (README, API docs, Guides)
- ✅ Quality Review ({{code_review_report}} overall score)

**Code Quality:** {code_quality_level}
**Strapi Version:** {strapi_version}

**Installation instructions** provided above.

The plugin is ready for testing and deployment!
</action>
</step>

<step n="9" goal="Post-generation support" optional="true">
<ask>Would you like help with:
1. Testing the plugin in your Strapi project
2. Making modifications or adjustments
3. Publishing the plugin to npm
4. Creating additional features
5. Exit (plugin generation complete)

Select option (1-5):
</ask>

<action if="option 1">Guide user through testing process and troubleshooting</action>
<action if="option 2">Identify modifications needed and invoke appropriate agent workflows</action>
<action if="option 3">Provide npm publishing guide and checklist</action>
<action if="option 4">Return to Step 1 for additional features planning</action>
<action if="option 5">Thank user and conclude workflow</action>
</step>

</workflow>
