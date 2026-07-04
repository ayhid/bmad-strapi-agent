# Quick Scaffold - Fast Plugin Scaffolding

<critical>The workflow execution engine is governed by: {project-root}/.bmad/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {project-root}/.bmad/custom/modules/strapi-plugin-forge/workflows/quick-scaffold/workflow.yaml</critical>
<critical>Communicate in {communication_language} throughout the workflow execution process</critical>
<critical>This workflow is optimized for speed - minimal questions, fast scaffolding</critical>

<workflow>

<step n="1" goal="Quick metadata collection">
<ask>Plugin name (without strapi-plugin- prefix)?</ask>
<action>Store as {{plugin_name}}, prepend "strapi-plugin-" if not present</action>

<ask>Plugin type?
1. Content Management (content-types, CRUD)
2. Custom Field (single custom field type)
3. Admin Panel Extension (custom pages/UI)
4. API Extension (custom endpoints)
5. Integration (external service integration)
6. Utility (helper functions, tools)

Select number (1-6):</ask>
<action>Store as {{plugin_type}}</action>

<ask>Brief description (one sentence)?</ask>
<action>Store as {{plugin_description}}</action>
</step>

<step n="2" goal="Select template and scaffold">
<action>Based on {{plugin_type}}, select appropriate template from {template_library_path}:

- Content Management → basic-crud template
- Custom Field → custom-field template
- Admin Panel → admin-panel template
- API Extension → api-extension template
- Integration → integration template
- Utility → utility template

If template doesn't exist yet, use minimal base template with standard Strapi {strapi_version} structure.
</action>

<action>Scaffold plugin structure at {plugin_output_path}/{{plugin_name}}/:

**Standard Structure:**
```
{{plugin_name}}/
├── server/
│   ├── content-types/
│   ├── services/
│   ├── controllers/
│   ├── routes/
│   └── index.js
├── admin/
│   └── src/
│       └── index.js
├── package.json
├── README.md
└── .gitignore
```

Populate with:
- Basic package.json (name, version, description, dependencies)
- Minimal server/index.js (plugin registration)
- Minimal admin/src/index.js (admin registration)
- Basic README.md with plugin name and description
- Standard .gitignore for Node.js projects
</action>

<action>If {{plugin_type}} requires specific files, add them:
- Content Management → sample content-type schema
- Custom Field → custom field component skeleton
- Admin Panel → sample page component
- API Extension → sample route and controller
- Integration → sample service with external API structure
- Utility → sample utility function
</action>
</step>

<step n="3" goal="Generate package.json">
<action>Create complete package.json (derive the @strapi/strapi peer dependency major version from {strapi_version}):

```json
{
  "name": "{{plugin_name}}",
  "version": "0.1.0",
  "description": "{{plugin_description}}",
  "strapi": {
    "displayName": "{{plugin_name}}",
    "name": "{{plugin_name}}",
    "description": "{{plugin_description}}",
    "kind": "plugin"
  },
  "dependencies": {},
  "peerDependencies": {
    "@strapi/strapi": "^5.0.0"
  },
  "author": {
    "name": "{user_name}"
  },
  "maintainers": [
    {
      "name": "{user_name}"
    }
  ],
  "engines": {
    "node": ">=18.0.0 <=22.x.x",
    "npm": ">=6.0.0"
  },
  "license": "MIT"
}
```
</action>
</step>

<step n="4" goal="Generate README">
<action>Create basic README.md:

```markdown
# {{plugin_name}}

{{plugin_description}}

## Installation

\`\`\`bash
npm install {{plugin_name}}
\`\`\`

## Configuration

Add to your Strapi config:

\`\`\`javascript
// config/plugins.js
module.exports = {
  '{{plugin_name}}': {
    enabled: true,
  },
};
\`\`\`

## Usage

[Add usage instructions here]

## Development

\`\`\`bash
npm install
npm run develop
\`\`\`

## License

MIT

---

Generated with Strapi Plugin Forge on {{date}}
```
</action>
</step>

<step n="5" goal="Finalize and report">
<action>Create .gitignore:
```
node_modules/
.env
.cache/
build/
dist/
*.log
.DS_Store
```
</action>

<action>Report to {user_name}:

**Plugin scaffolded successfully! 🚀**

**Location:** {plugin_output_path}/{{plugin_name}}/

**Type:** {{plugin_type}}

**What's included:**
- ✅ Basic plugin structure
- ✅ Server and admin registration files
- ✅ package.json with dependencies
- ✅ README.md with installation instructions
- ✅ .gitignore

**Next steps:**
1. `cd {plugin_output_path}/{{plugin_name}}`
2. Add your custom logic to server/ and admin/
3. Test by linking to Strapi project
4. Build and publish when ready

**Ready for manual development!**
</action>
</step>

</workflow>
