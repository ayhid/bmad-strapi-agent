# Generate UI - Strapi Admin Panel Components

<critical>This skill is typically invoked by the `strapi-forge-create-plugin` skill and expects UI requirements as input - if run standalone, gather the inputs listed in Step 1 from the user first</critical>
<critical>Communicate in {communication_language} throughout execution</critical>

<workflow>

<step n="1" goal="Load UI requirements and architecture">
<action>Receive inputs from the calling skill (or gather from the user when run standalone):
- Plugin requirements ({{plugin_requirements}})
- Architecture blueprint ({{plugin_architecture}})
- Backend structure ({{backend_structure}})
- Output path ({{output_path}})
- Code quality level ({code_quality_level})

Parse UI specifications:
- Admin pages needed (dashboards, lists, forms, settings)
- Custom field components
- Reusable UI components
- Routing structure
- State management needs
- Data fetching patterns
</action>

<action>Store parsed UI specs for component generation</action>
</step>

<step n="2" goal="Generate admin plugin registration">
<action>Generate admin/src/index.js (plugin entry point):

```javascript
import pluginPkg from '../../package.json';
import pluginId from './pluginId';
import Initializer from './components/Initializer';
import PluginIcon from './components/PluginIcon';
import { prefixPluginTranslations } from './utils/prefixPluginTranslations';

const name = pluginPkg.strapi.name;

export default {
  register(app) {
    // Register plugin
    app.addMenuLink({
      to: `/plugins/${pluginId}`,
      icon: PluginIcon,
      intlLabel: {
        id: `${pluginId}.plugin.name`,
        defaultMessage: name,
      },
      Component: async () => {
        const component = await import('./pages/App');
        return component;
      },
      permissions: [], // TODO: Add permissions
    });

    // Register custom fields if any
    {{custom_field_registrations}}

    app.registerPlugin({
      id: pluginId,
      initializer: Initializer,
      isReady: false,
      name,
    });
  },

  bootstrap(app) {
    // Bootstrap logic
  },

  async registerTrads({ locales }) {
    const importedTrads = await Promise.all(
      locales.map((locale) => {
        return import(`./translations/${locale}.json`)
          .then(({ default: data }) => {
            return {
              data: prefixPluginTranslations(data, pluginId),
              locale,
            };
          })
          .catch(() => {
            return {
              data: {},
              locale,
            };
          });
      })
    );

    return Promise.resolve(importedTrads);
  },
};
```

Generate to: {{output_path}}/admin/src/index.js
</action>

<action>Generate admin/src/utils/prefixPluginTranslations.js (local replacement for the util removed with @strapi/helper-plugin in Strapi v5):

```javascript
const prefixPluginTranslations = (trad, pluginId) =>
  Object.keys(trad).reduce((acc, current) => {
    acc[`${pluginId}.${current}`] = trad[current];
    return acc;
  }, {});

export { prefixPluginTranslations };
```
</action>

<action>Generate pluginId.js:

```javascript
const pluginId = '{{plugin_name}}';

export default pluginId;
```
</action>
</step>

<step n="3" goal="Generate page components">
<action>For each page in the UI specifications:

Generate page component using Strapi Design System:

```jsx
import React, { useState } from 'react';
import { useIntl } from 'react-intl';
import { Layouts } from '@strapi/strapi/admin';
import { Button, Box, Typography } from '@strapi/design-system';
import { Plus } from '@strapi/icons';

const {{PageName}} = () => {
  const { formatMessage } = useIntl();
  const [data, setData] = useState([]);
  const [isLoading, setIsLoading] = useState(false);

  // Data fetching logic
  {{data_fetching}}

  return (
    <Layouts.Root>
      <Layouts.Header
        title="{{page_title}}"
        subtitle="{{page_subtitle}}"
        primaryAction={
          <Button
            startIcon={<Plus />}
            onClick={{action_handler}}
          >
            {{action_label}}
          </Button>
        }
      />
      <Layouts.Content>
        {{page_content}}
      </Layouts.Content>
    </Layouts.Root>
  );
};

export default {{PageName}};
```

Page types to generate:

**List/Table Page:**
- Use Table component from Design System
- Add filters, search, pagination
- Row actions (edit, delete)
- Bulk actions

**Form/Editor Page:**
- Use Form components (TextInput, Select, etc.)
- Add validation with react-hook-form or Formik
- Submit/cancel actions
- Field hints and errors

**Dashboard Page:**
- Cards with stats/metrics
- Charts (use recharts or similar)
- Quick actions
- Recent activity

**Settings Page:**
- Settings sections
- Form inputs for configuration
- Save/reset actions

Generate to: {{output_path}}/admin/src/pages/{{PageName}}/index.jsx
</action>

<action>Store generated pages list as {{pages_generated}}</action>
</step>

<step n="4" goal="Generate routing configuration">
<action>Generate App component with routing:

```jsx
import React from 'react';
import { Routes, Route } from 'react-router-dom';
import { Page } from '@strapi/strapi/admin';

// Page imports
{{page_imports}}

// React Router v6: routes are relative to the plugin mount path (/plugins/<pluginId>)
const App = () => {
  return (
    <Routes>
      {{for each route}}
      <Route path="{{route_path}}" element={<{{Component}} />} />
      {{endfor}}
      <Route path="*" element={<Page.Error />} />
    </Routes>
  );
};

export default App;
```

Generate to: {{output_path}}/admin/src/pages/App/index.jsx
</action>
</step>

<step n="5" goal="Generate reusable components">
<action>Generate shared components:

**PluginIcon Component:**
```jsx
import React from 'react';
import { {{IconName}} } from '@strapi/icons';

const PluginIcon = () => <{{IconName}} />;

export default PluginIcon;
```

**Table Component (if needed):**
```jsx
import React from 'react';
import {
  Table,
  Thead,
  Tbody,
  Tr,
  Td,
  Th,
  Typography,
  IconButton,
} from '@strapi/design-system';
import { Pencil, Trash } from '@strapi/icons';

const DataTable = ({ data, onEdit, onDelete }) => {
  return (
    <Table>
      <Thead>
        <Tr>
          {{table_headers}}
        </Tr>
      </Thead>
      <Tbody>
        {data.map((item) => (
          <Tr key={item.id}>
            {{table_cells}}
            <Td>
              <IconButton onClick={() => onEdit(item.id)} label="Edit" icon={<Pencil />} />
              <IconButton onClick={() => onDelete(item.id)} label="Delete" icon={<Trash />} />
            </Td>
          </Tr>
        ))}
      </Tbody>
    </Table>
  );
};

export default DataTable;
```

**Modal Component (if needed):**
```jsx
import React from 'react';
import { Modal, Button } from '@strapi/design-system';

const ConfirmModal = ({ open, onOpenChange, title, children, onConfirm }) => {
  return (
    <Modal.Root open={open} onOpenChange={onOpenChange}>
      <Modal.Content>
        <Modal.Header>
          <Modal.Title>{title}</Modal.Title>
        </Modal.Header>
        <Modal.Body>{children}</Modal.Body>
        <Modal.Footer>
          <Modal.Close>
            <Button variant="tertiary">Cancel</Button>
          </Modal.Close>
          <Button onClick={onConfirm}>Confirm</Button>
        </Modal.Footer>
      </Modal.Content>
    </Modal.Root>
  );
};

export default ConfirmModal;
```

Generate components to: {{output_path}}/admin/src/components/
</action>

<action>Store generated components list as {{components_generated}}</action>
</step>

<step n="6" goal="Generate custom field components">
<action if="custom fields needed">For each custom field:

```jsx
import React from 'react';
import PropTypes from 'prop-types';
import { useIntl } from 'react-intl';
import { Field, Flex } from '@strapi/design-system';

const {{FieldName}}Input = ({
  attribute,
  description,
  disabled,
  error,
  intlLabel,
  name,
  onChange,
  required,
  value,
}) => {
  const { formatMessage } = useIntl();

  const handleChange = (newValue) => {
    onChange({ target: { name, value: newValue, type: attribute.type } });
  };

  return (
    <Field.Root name={name} id={name} error={error} hint={description && formatMessage(description)}>
      <Flex direction="column" alignItems="stretch" gap={1}>
        <Field.Label action={{labelAction}} required={required}>
          {formatMessage(intlLabel)}
        </Field.Label>
        {{custom_field_input}}
        <Field.Hint />
        <Field.Error />
      </Flex>
    </Field.Root>
  );
};

{{FieldName}}Input.defaultProps = {
  description: null,
  disabled: false,
  error: null,
  labelAction: null,
  required: false,
  value: '',
};

{{FieldName}}Input.propTypes = {
  intlLabel: PropTypes.object.isRequired,
  onChange: PropTypes.func.isRequired,
  attribute: PropTypes.object.isRequired,
  name: PropTypes.string.isRequired,
  description: PropTypes.object,
  disabled: PropTypes.bool,
  error: PropTypes.string,
  labelAction: PropTypes.object,
  required: PropTypes.bool,
  value: PropTypes.any,
};

export default {{FieldName}}Input;
```

Generate to: {{output_path}}/admin/src/components/{{FieldName}}Input/
</action>
</step>

<step n="7" goal="Generate API client and hooks">
<action>Generate API client utility:

```javascript
import { getFetchClient } from '@strapi/strapi/admin';

const { get, post, put, del } = getFetchClient();

const api = {
  {{for each endpoint}}
  {{method_name}}: async ({{params}}) => {
    const { data } = await {{http_verb}}('{{endpoint_path}}'{{request_body_arg}});
    return data;
  },
  {{endfor}}
};

export default api;
```

Generate to: {{output_path}}/admin/src/utils/api.js
</action>

<action>Generate custom React hooks for data fetching:

```javascript
import { useEffect, useState } from 'react';
import { useNotification } from '@strapi/strapi/admin';
import api from '../utils/api';

export const use{{DataName}} = ({{params}}) => {
  const [data, setData] = useState(null);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState(null);
  const { toggleNotification } = useNotification();

  useEffect(() => {
    const fetchData = async () => {
      try {
        setIsLoading(true);
        const result = await api.{{api_method}}({{args}});
        setData(result);
      } catch (err) {
        setError(err);
        toggleNotification({
          type: 'danger',
          message: 'An error occurred while fetching data',
        });
      } finally {
        setIsLoading(false);
      }
    };

    fetchData();
  }, [{{dependencies}}]);

  return { data, isLoading, error };
};
```

Generate to: {{output_path}}/admin/src/hooks/
</action>
</step>

<step n="8" goal="Generate translations">
<action>Generate translation files:

```json
{
  "plugin.name": "{{plugin_display_name}}",
  "plugin.description": "{{plugin_description}}",
  {{for each translatable string}}
  "{{key}}": "{{value}}",
  {{endfor}}
}
```

Generate to: {{output_path}}/admin/src/translations/en.json
</action>
</step>

<step n="9" goal="Generate Initializer component">
<action>Generate Initializer for plugin lifecycle:

```jsx
import { useEffect, useRef } from 'react';
import PropTypes from 'prop-types';
import pluginId from '../../pluginId';

const Initializer = ({ setPlugin }) => {
  const ref = useRef();
  ref.current = setPlugin;

  useEffect(() => {
    ref.current(pluginId);
  }, []);

  return null;
};

Initializer.propTypes = {
  setPlugin: PropTypes.func.isRequired,
};

export default Initializer;
```
</action>
</step>

<step n="10" goal="Validate and finalize UI code">
<action>Validate generated UI code:
- All components use Strapi Design System correctly
- PropTypes or TypeScript types defined
- Accessibility features present (ARIA, keyboard nav)
- No console errors
- Responsive design implemented
- Code follows {code_quality_level} standard
- Follows React best practices
</action>

<action>Report generation results:

**Admin UI Generated! 🎨**

**Generated:**
- ✅ {{pages_count}} admin pages
- ✅ {{components_count}} reusable components
- ✅ {{custom_fields_count}} custom field components
- ✅ Routing configuration
- ✅ API client and data hooks
- ✅ Translation files
- ✅ Plugin registration

**Location:** {{output_path}}/admin/src/

**Design System:** Strapi Design System v2 (Strapi v5)
**Code Quality:** {code_quality_level}
**Accessibility:** WCAG 2.1 compliant

Admin UI ready for integration! ✅
</action>
</step>

</workflow>
