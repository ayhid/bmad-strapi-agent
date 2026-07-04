# Admin Panel Designer - Custom Admin Interfaces

<critical>The workflow execution engine is governed by: {project-root}/.bmad/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {project-root}/.bmad/custom/modules/strapi-plugin-forge/workflows/admin-panel-designer/workflow.yaml</critical>
<critical>Communicate in {communication_language} throughout the workflow execution process</critical>

<workflow>

<step n="1" goal="Define admin panel requirements">
<action>Collaborate with user to understand admin panel needs:

**Panel Purpose:**
- What functionality should the admin panel provide?
- Who are the users? (admins, content editors, managers)
- What tasks will they perform?

**Panel Types to Consider:**
- **Dashboard:** Overview/stats/metrics display
- **Management Interface:** CRUD operations for custom data
- **Settings Panel:** Configuration and preferences
- **Tool/Utility:** Specialized functionality (import/export, bulk operations)
- **Analytics/Reports:** Data visualization and reporting
- **Workflow Manager:** Process management and automation

**Key Questions:**
- What pages/views are needed?
- What data do they display/manipulate?
- Any real-time updates needed?
- Permissions/roles required?
- Integration with Strapi content-types?
</action>

<action>Store panel requirements as {{panel_requirements}} including:
- Panel purpose and type
- Target users and roles
- Main functionality areas
- Pages/views needed
- Data sources and operations
- Special features (real-time, charts, etc.)
</action>
</step>

<step n="2" goal="Design navigation and routing">
<action>Design the navigation structure:

**Navigation Architecture:**
- Where should plugin appear in Strapi sidebar?
- Top-level menu item or nested?
- Icon for the plugin
- How many routes/pages?

**Routing Structure:**
Example:
```
/plugins/{{plugin_name}}/
├── / (main dashboard)
├── /list (list view)
├── /create (create form)
├── /:id (detail view)
└── /settings (settings page)
```

**Breadcrumbs and Navigation:**
- What breadcrumb trail?
- Back navigation needed?
- Tab navigation within pages?

Work with user to map out complete navigation flow.
</action>

<action>Store navigation design as {{navigation_design}} including:
- Sidebar menu structure
- Plugin icon
- All routes and paths
- Breadcrumb hierarchy
- Navigation patterns
</action>
</step>

<step n="3" goal="Design page layouts and components">
<action>For each page in the admin panel, design the layout:

**Layout Components:**
Use Strapi Design System:
- Layout: Layouts.Root, Layouts.Header, Layouts.Content (from @strapi/strapi/admin)
- Content: Box, Flex, Grid for structure
- Typography: Typography for text hierarchy
- Tables: Table for data lists
- Forms: TextInput, Select, Checkbox, etc.
- Actions: Button, IconButton
- Feedback: Alert, Toast for notifications

**Page Types:**

**Dashboard/Overview:**
- Cards with stats/metrics
- Charts or visualizations
- Quick actions
- Recent activity

**List/Table View:**
- Data table with columns
- Filters and search
- Pagination
- Bulk actions
- Row actions (edit, delete)

**Form/Editor:**
- Form fields with validation
- Submit/cancel actions
- Autosave or draft support
- Field hints and errors

**Detail View:**
- Header with title/actions
- Tabbed content sections
- Related data display
- Action buttons

**Settings:**
- Settings sections
- Form inputs for configuration
- Save/reset actions
- Help text

For each page, specify:
- Layout structure (header, content areas)
- Components needed
- Data display format
- User interactions
- State management needs
</action>

<action>Store layout designs as {{layout_designs}} with:
- Each page's layout structure
- Design System components used
- Data flow and state
- Interactive elements
</action>
</step>

<step n="4" goal="Design data fetching and state management">
<action>Plan how data flows through the admin panel:

**Data Sources:**
- Strapi API endpoints to call
- Custom plugin endpoints
- External APIs (if any)

**State Management:**
- React Query for API data fetching
- Local state for UI state
- Form state management (React Hook Form)
- Global state if needed (Context API)

**Data Operations:**
- List/fetch operations
- Create operations
- Update operations
- Delete operations
- Bulk operations
- Real-time subscriptions (if needed)

**Error Handling:**
- API error handling
- User feedback (toasts, alerts)
- Retry logic
- Loading states

Document data architecture for each page.
</action>

<action>Store data architecture as {{data_architecture}} including:
- API endpoints mapping
- State management approach
- CRUD operations
- Error handling patterns
- Loading/success/error states
</action>
</step>

<step n="5" goal="Generate admin panel pages">
<invoke-workflow path="{frontend_workflow}">
Invoke Jamil (generate-ui workflow) to generate admin pages.

Pass to Jamil:
- Panel requirements: {{panel_requirements}}
- Navigation design: {{navigation_design}}
- Layout designs: {{layout_designs}}
- Data architecture: {{data_architecture}}
- Output path: {plugin_output_path}/{{plugin_name}}/admin/src/
- Code quality: {code_quality_level}

Jamil generates for each page:
- Page component (pages/PageName/index.jsx)
- Page-specific components (components/)
- Data fetching hooks (hooks/usePageData.js)
- Form schemas and validation
- Styled with Design System
- Fully accessible
</invoke-workflow>

<action>Store generated pages path as {{pages_path}}</action>
</step>

<step n="6" goal="Generate routing configuration">
<action>Generate admin routing setup:

`admin/src/pages/App/index.jsx`:

```javascript
import React from 'react';
import { Routes, Route } from 'react-router-dom';
import { Page } from '@strapi/strapi/admin';

import HomePage from '../HomePage';
import ListView from '../ListView';
import DetailView from '../DetailView';
import SettingsPage from '../SettingsPage';

// React Router v6: routes are relative to the plugin mount path (/plugins/<pluginId>)
const App = () => {
  return (
    <Routes>
      <Route index element={<HomePage />} />
      <Route path="list" element={<ListView />} />
      <Route path="settings" element={<SettingsPage />} />
      <Route path=":id" element={<DetailView />} />
      <Route path="*" element={<Page.Error />} />
    </Routes>
  );
};

export default App;
```

`admin/src/index.js` (plugin registration):

```javascript
import pluginId from './pluginId';
import App from './pages/App';
import pluginIcon from './components/PluginIcon';

export default {
  register(app) {
    app.addMenuLink({
      to: `/plugins/${pluginId}`,
      icon: pluginIcon,
      intlLabel: {
        id: `${pluginId}.plugin.name`,
        defaultMessage: '{{plugin_display_name}}',
      },
      Component: async () => {
        const component = await import('./pages/App');
        return component;
      },
      permissions: [], // Add permissions if needed
    });

    app.registerPlugin({
      id: pluginId,
      name: '{{plugin_name}}',
    });
  },
};
```
</action>
</step>

<step n="7" goal="Generate reusable components">
<action>Generate shared components for the admin panel:

Common components needed:
- Table components (DataTable, TableRow, etc.)
- Card components (StatCard, InfoCard, etc.)
- Form components (FormSection, FieldGroup, etc.)
- Modal dialogs
- Confirmation dialogs
- Loading states
- Empty states
- Error boundaries

Create in `admin/src/components/`:
- Each component as separate file
- Use Design System as base
- Make reusable and composable
- Include PropTypes or TypeScript types
- Document component usage
</action>

<action>Store components path as {{components_path}}</action>
</step>

<step n="8" goal="Generate API integration">
<action>Create API client utilities:

`admin/src/utils/api.js`:

```javascript
import { getFetchClient } from '@strapi/strapi/admin';

const { get, post, put, del } = getFetchClient();

const apiClient = {
  fetchItems: async () => {
    const { data } = await get('/{{plugin_name}}/items');
    return data;
  },

  fetchItem: async (id) => {
    const { data } = await get(`/{{plugin_name}}/items/${id}`);
    return data;
  },

  createItem: async (payload) => {
    const { data } = await post('/{{plugin_name}}/items', payload);
    return data;
  },

  updateItem: async (id, payload) => {
    const { data } = await put(`/{{plugin_name}}/items/${id}`, payload);
    return data;
  },

  deleteItem: async (id) => {
    await del(`/{{plugin_name}}/items/${id}`);
  },
};

export default apiClient;
```

Create custom hooks for data fetching using React Query.
</action>
</step>

<step n="9" goal="Generate tests">
<action>Create tests for admin panel:

For each page:
- Render tests
- User interaction tests
- Data fetching tests (with mocks)
- Form submission tests
- Navigation tests

For components:
- Render with different props
- User interactions
- Accessibility tests

Use React Testing Library and Jest.
</action>

<action>Store tests path as {{tests_path}}</action>
</step>

<step n="10" goal="Generate documentation">
<action>Create admin panel documentation:

`ADMIN_PANEL.md`:

```markdown
# {{plugin_display_name}} Admin Panel

## Overview

{{panel_purpose}}

## Navigation

The plugin adds a new menu item in the Strapi sidebar: **{{plugin_display_name}}**

### Pages

{{for each page}}
- **{{page_name}}** (`/plugins/{{plugin_name}}/{{route}}`)
  - Purpose: {{page_purpose}}
  - Features: {{page_features}}
{{endfor}}

## User Guide

### {{functionality_area}}

{{usage_instructions}}

### Permissions

{{permissions_info}}

## Development

### Adding New Pages

{{instructions}}

### Customizing Components

{{instructions}}
```
</action>
</step>

<step n="11" goal="Assemble and finalize">
<action>Assemble complete admin panel plugin:

**Structure:**
```
{{plugin_name}}/
└── admin/
    └── src/
        ├── pages/
        │   ├── App/
        │   ├── HomePage/
        │   ├── ListView/
        │   ├── DetailView/
        │   └── SettingsPage/
        ├── components/
        │   ├── PluginIcon/
        │   ├── DataTable/
        │   └── ... (shared components)
        ├── hooks/
        │   └── useApiData.js
        ├── utils/
        │   └── api.js
        ├── index.js (plugin registration)
        └── pluginId.js
```
</action>

<action>Report to {user_name}:

**Admin Panel Plugin Generated! 🎨**

**Plugin:** {{plugin_display_name}}
**Location:** {plugin_output_path}/{{plugin_name}}/

**What was created:**
- ✅ {{num_pages}} admin pages with routing
- ✅ Sidebar menu integration
- ✅ {{num_components}} reusable components
- ✅ API integration and data fetching
- ✅ Complete tests
- ✅ User documentation

**Pages created:**
{{list all pages with routes}}

**Installation:**
1. Link plugin to Strapi project
2. Plugin appears in sidebar
3. Navigate to pages and test functionality

Admin panel ready for use! 🚀
</action>
</step>

</workflow>
