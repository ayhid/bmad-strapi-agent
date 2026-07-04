# Generate Docs - Plugin Documentation Generation

<critical>This skill is typically invoked by another Strapi Plugin Forge skill or agent - it expects plugin info as input. If any input is missing, ask the user for it before proceeding.</critical>
<critical>Communicate in {communication_language} throughout execution</critical>
<critical>When stamping documents with {{date}}, ask or derive the current date</critical>

<workflow>

<step n="1" goal="Gather documentation inputs">
<action>Receive inputs from the invoking skill or agent:
- Plugin requirements ({{plugin_requirements}})
- Architecture blueprint ({{plugin_architecture}})
- Plugin code path ({{plugin_path}})
- Code review report ({{code_review_report}}) - optional
- Output path ({{output_path}})

Extract documentation data from:
- package.json (metadata, dependencies)
- Content-type schemas (data models)
- Services (business logic methods)
- Controllers (API endpoints)
- Routes (API paths)
- Admin components (UI features)
- Tests (usage examples)
</action>

<action>Store documentation data as {{docs_data}}</action>
</step>

<step n="2" goal="Generate README.md">
<action>Create comprehensive README.md:

```markdown
# {{plugin_name}}

{{plugin_description}}

{{if_badges}}
![Version](https://img.shields.io/npm/v/{{plugin_name}})
![License](https://img.shields.io/npm/l/{{plugin_name}})
{{endif}}

## Features

{{feature_bullets}}

## Requirements

- Node.js >=18.0.0
- npm >=6.0.0
- Strapi v5.x

## Installation

\`\`\`bash
npm install {{plugin_name}}
\`\`\`

## Configuration

Add to your Strapi project configuration:

\`\`\`javascript
// config/plugins.js
module.exports = {
  '{{plugin_id}}': {
    enabled: true,
    config: {
      // Plugin configuration options
      {{config_example}}
    },
  },
};
\`\`\`

See [Configuration Guide](./docs/CONFIGURATION.md) for all options.

## Quick Start

{{quick_start_steps}}

## Usage

### Basic Usage

{{basic_usage_example}}

### Advanced Usage

{{advanced_usage_examples}}

## API Reference

{{if_api_endpoints}}
### Available Endpoints

{{api_endpoints_table}}

See [API Documentation](./docs/API.md) for complete reference.
{{endif}}

## Data Models

{{if_content_types}}
### Content Types

{{content_types_summary}}

See [Data Model Documentation](./docs/DATA_MODELS.md) for details.
{{endif}}

## Admin Interface

{{if_admin_ui}}
{{admin_ui_description}}

Features:
{{admin_features_list}}
{{endif}}

## Examples

### Example 1: {{example_1_title}}

\`\`\`javascript
{{example_1_code}}
\`\`\`

### Example 2: {{example_2_title}}

\`\`\`javascript
{{example_2_code}}
\`\`\`

More examples in [docs/EXAMPLES.md](./docs/EXAMPLES.md).

## Testing

\`\`\`bash
# Run all tests
npm test

# Run with coverage
npm run test:coverage

# Run specific test suite
npm run test:unit
\`\`\`

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md) for development setup and guidelines.

## Troubleshooting

Common issues and solutions:

{{troubleshooting_items}}

For more help, see [docs/TROUBLESHOOTING.md](./docs/TROUBLESHOOTING.md).

## License

{{license}}

## Support

- 📖 [Documentation](./docs/)
- 🐛 [Issue Tracker]({{issue_url}})
- 💬 [Discussions]({{discussion_url}})

## Credits

Created by {{author}} using [Strapi Plugin Forge](https://github.com/...)

---

**Made with ❤️ for the Strapi Community**

_Generated on {{date}}_
```

Generate to: {{output_path}}/README.md
</action>
</step>

<step n="3" goal="Generate API documentation">
<action if="API endpoints exist">Create docs/API.md:

```markdown
# API Reference

Complete API documentation for {{plugin_name}}.

## Base URL

\`\`\`
{{base_url}}
\`\`\`

## Authentication

{{auth_description}}

{{for each endpoint}}
---

## {{endpoint_title}}

### {{method}} {{path}}

{{endpoint_description}}

**Endpoint:**
\`\`\`
{{method}} {{full_path}}
\`\`\`

**Parameters:**

| Name | Type | Location | Required | Description |
|------|------|----------|----------|-------------|
{{parameters_table_rows}}

{{if_request_body}}
**Request Body:**

\`\`\`json
{{request_body_schema}}
\`\`\`
{{endif}}

**Response ({{success_status}}):**

\`\`\`json
{{success_response_example}}
\`\`\`

**Error Responses:**

| Status | Description |
|--------|-------------|
{{error_responses_rows}}

**Example Request:**

\`\`\`javascript
// Using fetch
const response = await fetch('{{full_url}}', {
  method: '{{method}}',
  headers: {
    'Content-Type': 'application/json',
    {{auth_headers}}
  },
  body: JSON.stringify({{request_example}})
});

const data = await response.json();
\`\`\`

\`\`\`bash
# Using curl
curl -X {{method}} {{full_url}} \\
  -H 'Content-Type: application/json' \\
  {{auth_header_curl}} \\
  -d '{{request_json}}'
\`\`\`

{{endfor}}

---

## Error Handling

{{error_handling_description}}

\`\`\`json
{{error_response_format}}
\`\`\`
```

Generate to: {{output_path}}/docs/API.md
</action>
</step>

<step n="4" goal="Generate data model documentation">
<action if="content types exist">Create docs/DATA_MODELS.md:

```markdown
# Data Models

Data structure documentation for {{plugin_name}}.

{{for each content_type}}
## {{content_type_name}}

{{content_type_description}}

**UID:** \`{{uid}}\`

### Fields

| Field | Type | Required | Unique | Description |
|-------|------|----------|--------|-------------|
{{fields_table_rows}}

{{if_relations}}
### Relations

{{relations_description}}
{{endif}}

### Example Data

\`\`\`json
{{example_entity}}
\`\`\`

### Usage

\`\`\`javascript
// Query {{content_type_name}} (Document Service API)
const entries = await strapi.documents('{{uid}}').findMany({
  fields: {{fields_array}},
  filters: {{filter_example}},
});

// Create {{content_type_name}}
const entry = await strapi.documents('{{uid}}').create({
  data: {{create_example}},
});
\`\`\`

{{endfor}}
```

Generate to: {{output_path}}/docs/DATA_MODELS.md
</action>
</step>

<step n="5" goal="Generate configuration documentation">
<action>Create docs/CONFIGURATION.md:

```markdown
# Configuration Guide

Complete configuration options for {{plugin_name}}.

## Basic Configuration

Minimum required configuration:

\`\`\`javascript
// config/plugins.js
module.exports = {
  '{{plugin_id}}': {
    enabled: true,
  },
};
\`\`\`

## Full Configuration

All available options with defaults:

\`\`\`javascript
// config/plugins.js
module.exports = {
  '{{plugin_id}}': {
    enabled: true,
    config: {
      {{full_config_with_defaults}}
    },
  },
};
\`\`\`

## Configuration Options

{{for each config_option}}
### \`{{option_name}}\`

- **Type:** {{type}}
- **Default:** {{default_value}}
- **Required:** {{required}}

{{option_description}}

**Example:**

\`\`\`javascript
{
  {{option_name}}: {{example_value}}
}
\`\`\`

{{endfor}}

## Environment Variables

{{env_vars_documentation}}

## Advanced Configuration

{{advanced_config_examples}}
```

Generate to: {{output_path}}/docs/CONFIGURATION.md
</action>
</step>

<step n="6" goal="Generate usage examples">
<action>Create docs/EXAMPLES.md:

```markdown
# Usage Examples

Practical examples for {{plugin_name}}.

{{for each use_case}}
## {{use_case_title}}

{{use_case_description}}

### Setup

{{setup_steps}}

### Code

\`\`\`javascript
{{example_code}}
\`\`\`

### Expected Result

{{expected_output}}

---

{{endfor}}

## Common Patterns

### Pattern 1: {{pattern_title}}

{{pattern_description}}

\`\`\`javascript
{{pattern_code}}
\`\`\`

## Integration Examples

### With Strapi Lifecycle Hooks

\`\`\`javascript
{{lifecycle_integration_example}}
\`\`\`

### With Custom Controllers

\`\`\`javascript
{{controller_integration_example}}
\`\`\`
```

Generate to: {{output_path}}/docs/EXAMPLES.md
</action>
</step>

<step n="7" goal="Generate troubleshooting guide">
<action>Create docs/TROUBLESHOOTING.md:

```markdown
# Troubleshooting Guide

Common issues and solutions for {{plugin_name}}.

## Installation Issues

### Issue: Plugin not appearing in admin panel

**Symptoms:**
- Plugin installed but not visible in sidebar
- No errors in console

**Solution:**
{{solution_steps}}

## Configuration Issues

{{config_issues_and_solutions}}

## API Issues

{{api_issues_and_solutions}}

## Performance Issues

{{performance_issues_and_solutions}}

## Getting Help

If you can't find a solution:

1. Check [GitHub Issues]({{issues_url}})
2. Search [Discussions]({{discussions_url}})
3. Ask in Strapi Discord: #plugin-help
4. Create a new issue with:
   - Strapi version
   - Plugin version
   - Node.js version
   - Steps to reproduce
   - Error messages/logs
```

Generate to: {{output_path}}/docs/TROUBLESHOOTING.md
</action>
</step>

<step n="8" goal="Generate contributing guide">
<action>Create CONTRIBUTING.md:

```markdown
# Contributing to {{plugin_name}}

Thank you for your interest in contributing!

## Development Setup

### Prerequisites

- Node.js >=18.0.0
- npm >=6.0.0
- Strapi v5 project for testing

### Installation

\`\`\`bash
git clone {{repo_url}}
cd {{plugin_name}}
npm install
\`\`\`

### Link to Strapi Project

\`\`\`bash
npm link
cd /path/to/strapi/project
npm link {{plugin_name}}
\`\`\`

### Development Workflow

\`\`\`bash
# Start in watch mode
npm run develop

# Run tests
npm test

# Run linter
npm run lint
\`\`\`

## Project Structure

\`\`\`
{{project_structure}}
\`\`\`

## Coding Standards

- Use TypeScript where possible
- Follow Strapi v5 conventions
- Add tests for new features
- Update documentation
- Follow semantic commit messages

## Testing

All contributions must include tests:

\`\`\`bash
# Add tests in tests/
npm test

# Check coverage
npm run test:coverage
\`\`\`

## Submitting Changes

1. Fork the repository
2. Create a feature branch (\`git checkout -b feature/amazing-feature\`)
3. Commit changes (\`git commit -m 'feat: add amazing feature'\`)
4. Push to branch (\`git push origin feature/amazing-feature\`)
5. Open a Pull Request

### Pull Request Guidelines

- Clear description of changes
- Reference related issues
- Include tests
- Update documentation
- Pass all CI checks

## Code Review Process

{{code_review_process}}

## Questions?

Feel free to open an issue for any questions!
```

Generate to: {{output_path}}/CONTRIBUTING.md
</action>
</step>

<step n="9" goal="Generate changelog">
<action>Create CHANGELOG.md:

```markdown
# Changelog

All notable changes to {{plugin_name}} will be documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [{{version}}] - {{date}}

### Added
{{initial_features}}

### Changed
- Initial release

### Fixed
- N/A

[Unreleased]: {{repo_url}}/compare/v{{version}}...HEAD
[{{version}}]: {{repo_url}}/releases/tag/v{{version}}
```

Generate to: {{output_path}}/CHANGELOG.md
</action>
</step>

<step n="10" goal="Finalize documentation">
<action>Create docs/index.md (documentation hub):

```markdown
# {{plugin_name}} Documentation

Complete documentation for the {{plugin_name}} Strapi plugin.

## Getting Started

- [README](../README.md) - Overview and quick start
- [Installation & Configuration](./CONFIGURATION.md)

## Guides

- [Usage Examples](./EXAMPLES.md) - Practical examples
- [Troubleshooting](./TROUBLESHOOTING.md) - Common issues

## Reference

- [API Documentation](./API.md) - Complete API reference
- [Data Models](./DATA_MODELS.md) - Content-type schemas

## Contributing

- [Contributing Guide](../CONTRIBUTING.md) - Development setup
- [Changelog](../CHANGELOG.md) - Version history
```

Generate to: {{output_path}}/docs/index.md
</action>

<action>Report documentation generation results:

**Documentation Generated! 📚**

**Generated Files:**
- ✅ README.md (comprehensive overview)
- ✅ docs/API.md ({{endpoints_count}} endpoints documented)
- ✅ docs/DATA_MODELS.md ({{content_types_count}} models documented)
- ✅ docs/CONFIGURATION.md ({{config_options_count}} options)
- ✅ docs/EXAMPLES.md ({{examples_count}} examples)
- ✅ docs/TROUBLESHOOTING.md
- ✅ CONTRIBUTING.md
- ✅ CHANGELOG.md
- ✅ docs/index.md (documentation hub)

**Location:** {{output_path}}/

**Documentation Quality:** Developer-friendly, comprehensive, example-driven

Documentation complete! ✅
</action>

<action>Return documentation status to the invoking skill or agent</action>
</step>

</workflow>
