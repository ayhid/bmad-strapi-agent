# Documentation Generator - Auto-generate Plugin Documentation

<critical>Complete the On Activation steps in SKILL.md (customization resolved, config loaded) before executing this workflow</critical>
<critical>Communicate in {communication_language} throughout the workflow execution process</critical>

<workflow>

<step n="1" goal="Locate and analyze plugin">
<ask>Path to the Strapi plugin to document?</ask>
<action>Store as {{plugin_path}}</action>

<action>Analyze plugin structure:
- Load package.json for metadata
- Identify server-side code (content-types, services, controllers, routes)
- Identify admin-side code (pages, components, custom fields)
- Discover configuration options
- Find existing documentation
- Analyze code comments and JSDoc
</action>

<action>Extract plugin information as {{plugin_info}}:
- Plugin name, version, description
- Author and maintainers
- Strapi version compatibility
- Dependencies
- Features list (from code analysis)
- API endpoints discovered
- Content-types defined
- Custom fields registered
- Admin pages available
- Configuration options
</action>
</step>

<step n="2" goal="Analyze API endpoints and generate API documentation">
<action>Scan all routes and controllers to extract API documentation:

For each endpoint discovered:
- HTTP method (GET, POST, PUT, DELETE)
- Path (/api/plugin-name/resource)
- Purpose and description
- Request parameters (path, query, body)
- Request body schema
- Response format and schema
- Status codes (200, 400, 404, 500)
- Authentication requirements
- Example requests and responses
- Error responses

Extract from:
- Route definitions in server/routes/
- Controller methods in server/controllers/
- Service documentation
- JSDoc comments
- Type definitions
</action>

<action>Store API documentation as {{api_docs}}</action>
</step>

<step n="3" goal="Document content-types and data models">
<action>Analyze content-type schemas:

For each content-type:
- Content-type name and UID
- Display name and description
- All fields with:
  - Field name
  - Field type
  - Validation rules
  - Required/optional
  - Default value
  - Help text
- Relations to other content-types
- Plugins and components used
- Lifecycle hooks
- Example data structure

Generate schema documentation in readable format.
</action>

<action>Store data model docs as {{data_model_docs}}</action>
</step>

<step n="4" goal="Document custom fields and admin UI">
<action if="admin UI exists">Analyze admin interface:

**Custom Fields:**
For each custom field:
- Field name and purpose
- Data type stored
- UI component used
- Configuration options
- Validation rules
- Usage examples

**Admin Pages:**
For each admin page:
- Page name and route
- Purpose and functionality
- User interactions
- Permissions required
- Screenshots or UI description

**Admin Configuration:**
- Settings available
- Configuration options
- Default values
</action>

<action>Store admin docs as {{admin_docs}}</action>
</step>

<step n="5" goal="Extract configuration options">
<action>Document all configuration options:

Analyze:
- Plugin configuration schema
- Environment variables used
- Config file options (config/plugins.js)
- Default configurations
- Optional vs required settings
- Configuration validation

For each configuration option:
- Option name
- Type and format
- Purpose and effect
- Default value
- Valid values/range
- Example configuration
</action>

<action>Store configuration docs as {{config_docs}}</action>
</step>

<step n="6" goal="Invoke Muin to generate documentation files">
<invoke-skill name="strapi-forge-generate-docs">
Invoke Muin (the `strapi-forge-generate-docs` skill) to generate complete documentation.

Pass to Muin:
- Plugin info: {{plugin_info}}
- API documentation: {{api_docs}}
- Data model docs: {{data_model_docs}}
- Admin docs: {{admin_docs}}
- Configuration docs: {{config_docs}}
- Output path: {{plugin_path}}/

Muin generates:
- README.md (comprehensive overview)
- API.md (API reference)
- CONFIGURATION.md (configuration guide)
- CONTRIBUTING.md (development guide)
- CHANGELOG.md (version history template)
- LICENSE (if missing)
- docs/ folder with detailed guides
</invoke-skill>

<action>Store generated docs paths as {{docs_generated}}</action>
</step>

<step n="7" goal="Finalize and report">
<action>Report to {user_name}:

**Documentation Generated! 📚**

**Plugin:** {{plugin_name}}
**Location:** {{plugin_path}}/

**Documentation created:**
- ✅ README.md (comprehensive overview)
- ✅ API.md ({{api_endpoint_count}} endpoints documented)
- ✅ CONFIGURATION.md ({{config_option_count}} options documented)
- ✅ CONTRIBUTING.md (development guide)
- ✅ CHANGELOG.md (version history)
- ✅ Additional guides in docs/ folder

**Content documented:**
- {{content_type_count}} content-types
- {{api_endpoint_count}} API endpoints
- {{custom_field_count}} custom fields
- {{admin_page_count}} admin pages
- {{config_option_count}} configuration options

**Next steps:**
1. Review generated documentation
2. Add custom examples if needed
3. Update with project-specific details
4. Keep documentation in sync with code updates

Documentation complete and ready! 🚀
</action>
</step>

</workflow>
