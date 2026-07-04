# Custom Field Generator - Create Custom Field Types

<critical>Complete the On Activation steps in SKILL.md (customization resolved, config loaded) before executing this workflow</critical>
<critical>Communicate in {communication_language} throughout the workflow execution process</critical>

<workflow>

<step n="1" goal="Define custom field requirements">
<action>Guide user to define their custom field:

**Field Specification:**

Ask about:
- **Field name:** What should this field be called? (e.g., "color-picker", "rating", "coordinates")
- **Field purpose:** What type of data does it capture?
- **Display type:** How should it appear in the content editor?
  - Text input with special formatting
  - Dropdown/select
  - Visual editor (color, date, etc.)
  - Complex component (map, chart, etc.)
- **Data structure:** What format is the stored data?
  - Simple string/number
  - JSON object
  - Array
  - Complex structure
- **Validation rules:** What constraints?
  - Required/optional
  - Format validation (regex, range, etc.)
  - Custom validation logic

**Examples to help clarify:**
- Color Picker: Stores hex color (#FF5733), visual color selector UI
- Rating: Stores number (1-5), star display UI
- Coordinates: Stores {lat, lng}, map picker UI
- JSON Editor: Stores JSON object, syntax-highlighted editor UI
</action>

<action>Store field specification as {{field_spec}} including:
- Field name (kebab-case)
- Display name (human-readable)
- Purpose and use case
- UI interaction type
- Data storage format
- Validation rules
- Default value (if any)
</action>
</step>

<step n="2" goal="Design field UI component">
<action>Collaborate on UI component design:

**Component Design:**

Based on the {{field_spec}}, design the React component:

**Input Method:**
- What UI controls are needed? (inputs, buttons, selects, sliders, etc.)
- Should it use existing Design System components?
- Any special interactions? (drag, click, keyboard shortcuts)

**Visual Design:**
- How should the field look in the content editor?
- Should it show a preview of the value?
- Any icons or visual indicators?

**User Experience:**
- Is it intuitive for content editors to use?
- Clear feedback on valid/invalid input?
- Helpful placeholder or hints?

**Strapi Design System:**
Identify which Design System components to use:
- TextInput, Select, Textarea for basic inputs
- Field.Root, Field.Label, Field.Hint for layout (Design System v2 composition)
- Box, Flex, Grid for structure
- Icon, Typography for styling
- Custom components if needed

Document the component architecture.
</action>

<action>Store UI design as {{ui_design}} including:
- Component structure
- Design System components used
- Props and state management
- User interactions
- Visual mockup or description
</action>
</step>

<step n="3" goal="Generate field registration code">
<action>Generate the server-side field registration:

Create `server/register.js`:

```javascript
module.exports = ({ strapi }) => {
  strapi.customFields.register({
    name: '{{field_name}}',
    plugin: '{{plugin_name}}',
    type: '{{data_type}}', // string, number, json, etc.
    inputSize: {
      default: 6, // Grid size (1-12)
      isResizable: true,
    },
  });
};
```

Create field configuration in `server/content-types/` if needed.
</action>

<action>Store server code path as {{server_code_path}}</action>
</step>

<step n="4" goal="Generate React field component">
<invoke-skill name="strapi-forge-generate-ui">
Invoke Jamil (the `strapi-forge-generate-ui` skill) to generate the custom field component.

Pass to Jamil:
- Field specification: {{field_spec}}
- UI design: {{ui_design}}
- Output path: {plugin_output_path}/{{plugin_name}}/admin/src/components/
- Code quality: {code_quality_level}

Jamil generates:
- Field component (FieldComponent.jsx)
- Component using Strapi Design System
- Proper props handling (value, onChange, error, etc.)
- Validation logic
- Accessibility features (ARIA labels, keyboard nav)
- Icon component if needed
</invoke-skill>

<action>Store component path as {{component_path}}</action>
</step>

<step n="5" goal="Generate admin registration">
<action>Create admin registration file:

`admin/src/index.js`:

```javascript
import pluginId from './pluginId';
import FieldComponent from './components/FieldComponent';

export default {
  register(app) {
    app.customFields.register({
      name: '{{field_name}}',
      pluginId: pluginId,
      type: '{{data_type}}',
      intlLabel: {
        id: `${pluginId}.{{field_name}}.label`,
        defaultMessage: '{{field_display_name}}',
      },
      intlDescription: {
        id: `${pluginId}.{{field_name}}.description`,
        defaultMessage: '{{field_description}}',
      },
      icon: {{field_icon}}, // Strapi icon component
      components: {
        Input: async () => import('./components/FieldComponent'),
      },
      options: {
        base: {
          // Base settings for the field
        },
        advanced: {
          // Advanced settings
        },
        validator: (args) => {
          // Custom validation
        },
      },
    });
  },
};
```
</action>
</step>

<step n="6" goal="Generate validation logic">
<action>Create validation utilities:

`admin/src/utils/validators.js`:

```javascript
export const validate{{field_name}} = (value) => {
  // Validation logic based on {{field_spec}}

  if ({{required}} && !value) {
    return 'This field is required';
  }

  // Format validation
  // Range validation
  // Custom rules

  return null; // Valid
};
```

Include both client-side (React) and server-side (Strapi) validation.
</action>
</step>

<step n="7" goal="Generate tests for custom field">
<action>Create component tests:

`admin/src/components/__tests__/FieldComponent.test.jsx`:

```javascript
import { render, screen, fireEvent } from '@testing-library/react';
import FieldComponent from '../FieldComponent';

describe('{{field_name}} Field Component', () => {
  it('renders correctly', () => {
    // Test render
  });

  it('handles value changes', () => {
    // Test onChange
  });

  it('shows validation errors', () => {
    // Test validation
  });

  it('is accessible', () => {
    // Test ARIA and keyboard nav
  });
});
```

Cover:
- Rendering with different props
- User interactions
- Validation scenarios
- Accessibility
</action>
</step>

<step n="8" goal="Generate documentation">
<action>Create field documentation:

`README.md`:

```markdown
# {{field_display_name}} Custom Field

{{field_description}}

## Installation

\`\`\`bash
npm install {{plugin_name}}
\`\`\`

## Usage

After installation, the {{field_display_name}} field will be available in the Content-Type Builder.

### Adding to Content-Type

1. Open Content-Type Builder
2. Select a content-type or create new one
3. Add a new field
4. Select "Custom" tab
5. Choose "{{field_display_name}}"
6. Configure field settings

### Field Options

- **{{option1}}:** {{description}}
- **{{option2}}:** {{description}}

### Data Format

The field stores data as:

\`\`\`json
{{data_format_example}}
\`\`\`

### Validation

{{validation_rules_description}}

### Example Usage

\`\`\`javascript
// Querying content with this field
const entries = await strapi.documents('api::article.article').findMany({
  fields: ['{{field_name}}'],
});
\`\`\`

## Development

\`\`\`bash
npm run develop
\`\`\`

## Testing

\`\`\`bash
npm test
\`\`\`
```
</action>
</step>

<step n="9" goal="Assemble and finalize">
<action>Assemble complete custom field plugin:

**Structure:**
```
{{plugin_name}}/
├── server/
│   ├── register.js (field registration)
│   └── index.js
├── admin/
│   └── src/
│       ├── components/
│       │   ├── FieldComponent.jsx
│       │   └── __tests__/
│       ├── utils/
│       │   └── validators.js
│       ├── index.js (admin registration)
│       └── pluginId.js
├── package.json
└── README.md
```
</action>

<action>Generate package.json with proper dependencies:
- @strapi/strapi ^5.0.0 (peer dependency)
- @strapi/design-system ^2.0.0 (peer dependency)
- Any additional UI libraries needed
</action>

<action>Report to {user_name}:

**Custom Field Plugin Generated! 🎨**

**Field:** {{field_display_name}} ({{field_name}})
**Location:** {plugin_output_path}/{{plugin_name}}/

**What was created:**
- ✅ Server-side field registration
- ✅ React field component with Design System
- ✅ Validation logic (client + server)
- ✅ Component tests
- ✅ Complete documentation

**Installation:**
1. Link to Strapi project
2. Field appears in Content-Type Builder under "Custom" tab
3. Add to any content-type
4. Use in content editor

**Testing:**
1. `npm test` - Run component tests
2. Add field to content-type in Strapi
3. Test in content editor

Custom field ready to use! 🚀
</action>
</step>

</workflow>
