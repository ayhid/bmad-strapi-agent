# Generate Backend - Strapi v5 Server-Side Code Generation

<critical>This skill is typically invoked by the `strapi-forge-create-plugin` skill and expects an architecture blueprint as input - if run standalone, gather the inputs listed in Step 1 from the user first</critical>
<critical>Communicate in {communication_language} throughout execution</critical>

<workflow>

<step n="1" goal="Load architecture and requirements">
<action>Receive inputs from the calling skill (or gather from the user when run standalone):
- Plugin requirements ({{plugin_requirements}})
- Architecture blueprint ({{plugin_architecture}})
- Output path ({{output_path}})
- Code quality level ({code_quality_level})
- Strapi version: v5

Parse architecture blueprint to extract:
- Content-types definitions (schemas, fields, relations)
- Component definitions
- Service layer specifications (services, methods)
- Controller specifications (endpoints, operations)
- Route definitions (paths, methods, handlers)
- Middleware requirements
- Policy requirements
- Plugin lifecycle hooks needed
</action>

<action>Store parsed specifications for code generation</action>
</step>

<step n="2" goal="Generate content-type schemas">
<action>For each content-type in the architecture:

Generate schema.json file following Strapi v5 conventions:

```json
{
  "kind": "collectionType",
  "collectionName": "{{collection_name}}",
  "info": {
    "singularName": "{{singular_name}}",
    "pluralName": "{{plural_name}}",
    "displayName": "{{display_name}}",
    "description": "{{description}}"
  },
  "options": {
    "draftAndPublish": {{draft_publish}},
    "comment": "{{comment}}"
  },
  "pluginOptions": {},
  "attributes": {
    {{for each field}}
    "{{field_name}}": {
      "type": "{{field_type}}",
      {{additional_field_properties}}
    }
    {{endfor}}
  }
}
```

Key considerations:
- Use correct Strapi v5 field types (string, text, richtext, email, enumeration, integer, biginteger, float, decimal, date, datetime, time, boolean, json, relation, component, dynamiczone, media, uid)
- Set proper validation rules (required, unique, min, max, minLength, maxLength, regex)
- Configure relations correctly (oneToOne, oneToMany, manyToOne, manyToMany, oneWay, manyWay)
- Use proper UIDs and naming conventions
- Set default values where appropriate

Generate to: {{output_path}}/server/content-types/{{content-type-name}}/schema.json
</action>

<action>For each content-type, also generate index.js:

```javascript
'use strict';

module.exports = {
  schema: require('./schema.json'),
  // Add lifecycle hooks if specified in architecture
};
```
</action>

<action>Store generated content-types list as {{content_types_generated}}</action>
</step>

<step n="3" goal="Generate component definitions">
<action if="components exist in architecture">For each component:

Generate component schema.json:

```json
{
  "collectionName": "components_{{category}}_{{component_name}}",
  "info": {
    "displayName": "{{display_name}}",
    "icon": "{{icon}}",
    "description": "{{description}}"
  },
  "options": {},
  "attributes": {
    {{field_definitions}}
  }
}
```

Generate to: {{output_path}}/server/components/{{category}}/{{component-name}}.json
</action>
</step>

<step n="4" goal="Generate service layer">
<action>For each service in the architecture:

Generate service implementation following Strapi v5 patterns:

```javascript
'use strict';

/**
 * {{service_name}} service
 */

module.exports = ({ strapi }) => ({
  {{for each method}}
  /**
   * {{method_description}}
   * @param {Object} ctx - Context object
   * @returns {Promise} Result
   */
  async {{method_name}}({{parameters}}) {
    try {
      // Business logic implementation based on architecture spec

      {{method_implementation}}

      return result;
    } catch (error) {
      strapi.log.error(`Error in {{service_name}}.{{method_name}}:`, error);
      throw error;
    }
  },
  {{endfor}}
});
```

Service implementation patterns:
- Use the Document Service API (strapi.documents) for content-type operations — entityService is deprecated in Strapi v5
- Use strapi.db.query for complex queries
- Implement proper error handling
- Add logging for debugging
- Follow single responsibility principle
- Keep services focused and testable
- Use TypeScript types if applicable

Generate to: {{output_path}}/server/services/{{service-name}}.js
</action>

<action>Generate services/index.js to export all services:

```javascript
'use strict';

const {{service1}} = require('./{{service1}}');
const {{service2}} = require('./{{service2}}');

module.exports = {
  {{service1}},
  {{service2}},
};
```
</action>

<action>Store generated services list as {{services_generated}}</action>
</step>

<step n="5" goal="Generate controller layer">
<action>For each controller in the architecture:

Generate controller implementation:

```javascript
'use strict';

/**
 * {{controller_name}} controller
 */

const { createCoreController } = require('@strapi/strapi').factories;

module.exports = createCoreController('plugin::{{plugin_name}}.{{content_type}}', ({ strapi }) => ({
  {{for each action}}
  /**
   * {{action_description}}
   * @param {Object} ctx - Koa context
   */
  async {{action_name}}(ctx) {
    try {
      // Extract parameters from request
      const { {{parameters}} } = ctx.request.body;
      const { {{query_params}} } = ctx.query;
      const { {{path_params}} } = ctx.params;

      // Validate input
      {{validation_logic}}

      // Call service layer
      const result = await strapi
        .plugin('{{plugin_name}}')
        .service('{{service_name}}')
        .{{method_name}}({{args}});

      // Return response
      ctx.body = result;
    } catch (error) {
      ctx.throw({{status_code}}, error.message);
    }
  },
  {{endfor}}
}));
```

Controller best practices:
- Keep controllers thin (logic in services)
- Proper parameter extraction and validation
- Use appropriate HTTP status codes
- Handle errors gracefully
- Return consistent response formats
- Add JSDoc comments
- Follow RESTful conventions

Generate to: {{output_path}}/server/controllers/{{controller-name}}.js
</action>

<action>Generate controllers/index.js:

```javascript
'use strict';

const {{controller1}} = require('./{{controller1}}');
const {{controller2}} = require('./{{controller2}}');

module.exports = {
  {{controller1}},
  {{controller2}},
};
```
</action>

<action>Store generated controllers list as {{controllers_generated}}</action>
</step>

<step n="6" goal="Generate route definitions">
<action>For each route group:

Generate route configuration:

```javascript
'use strict';

module.exports = {
  type: 'admin', // or 'content-api'
  routes: [
    {{for each route}}
    {
      method: '{{http_method}}',
      path: '/{{route_path}}',
      handler: '{{controller}}.{{action}}',
      config: {
        policies: [{{policies}}],
        middlewares: [{{middlewares}}],
        auth: {{auth_required}},
        description: '{{route_description}}',
        tag: {
          plugin: '{{plugin_name}}',
          name: '{{tag_name}}',
        },
      },
    },
    {{endfor}}
  ],
};
```

Route patterns:
- Admin routes: /admin/plugins/{{plugin_name}}/...
- Content API routes: /{{plugin_name}}/...
- Follow RESTful conventions:
  - GET /items - List
  - GET /items/:id - Get one
  - POST /items - Create
  - PUT /items/:id - Update
  - DELETE /items/:id - Delete

Generate to: {{output_path}}/server/routes/{{route-group}}.js
</action>

<action>Generate routes/index.js:

```javascript
'use strict';

const {{routeGroup1}} = require('./{{routeGroup1}}');
const {{routeGroup2}} = require('./{{routeGroup2}}');

module.exports = {
  {{routeGroup1}},
  {{routeGroup2}},
};
```
</action>

<action>Store generated routes list as {{routes_generated}}</action>
</step>

<step n="7" goal="Generate policies and middleware">
<action if="policies needed">For each policy:

```javascript
'use strict';

/**
 * {{policy_name}} policy
 */

module.exports = (policyContext, config, { strapi }) => {
  return async (ctx, next) => {
    // Policy logic
    {{policy_implementation}}

    if (allowed) {
      return await next();
    }

    ctx.forbidden('{{error_message}}');
  };
};
```

Generate to: {{output_path}}/server/policies/{{policy-name}}.js
</action>

<action if="middleware needed">For each middleware:

```javascript
'use strict';

/**
 * {{middleware_name}} middleware
 */

module.exports = (config, { strapi }) => {
  return async (ctx, next) => {
    // Middleware logic
    {{middleware_implementation}}

    await next();
  };
};
```

Generate to: {{output_path}}/server/middlewares/{{middleware-name}}.js
</action>
</step>

<step n="8" goal="Generate plugin configuration and lifecycle">
<action>Generate server/index.js (plugin entry point):

```javascript
'use strict';

const bootstrap = require('./bootstrap');
const destroy = require('./destroy');
const config = require('./config');
const contentTypes = require('./content-types');
const services = require('./services');
const controllers = require('./controllers');
const routes = require('./routes');
const middlewares = require('./middlewares');
const policies = require('./policies');

module.exports = {
  bootstrap,
  destroy,
  config,
  contentTypes,
  services,
  controllers,
  routes,
  middlewares,
  policies,
};
```
</action>

<action if="lifecycle hooks needed">Generate bootstrap.js:

```javascript
'use strict';

module.exports = async ({ strapi }) => {
  // Plugin initialization logic
  {{bootstrap_logic}}

  strapi.log.info('{{plugin_name}} plugin initialized');
};
```

Generate destroy.js:

```javascript
'use strict';

module.exports = async ({ strapi }) => {
  // Plugin cleanup logic
  {{cleanup_logic}}

  strapi.log.info('{{plugin_name}} plugin destroyed');
};
```
</action>

<action if="plugin config needed">Generate config/index.js:

```javascript
'use strict';

module.exports = {
  default: {{default_config}},
  validator: (config) => {
    // Validate plugin configuration
    {{validation_logic}}
  },
};
```
</action>
</step>

<step n="9" goal="Generate TypeScript definitions">
<action if="code_quality_level == 'strict' or 'enterprise'">Generate TypeScript type definitions:

```typescript
// server/types.d.ts

export interface {{ContentTypeName}} {
  {{field_definitions}}
}

export interface {{ServiceName}} {
  {{method_signatures}}
}

// Add complete type definitions for all content-types, services, controllers
```

Generate to: {{output_path}}/server/types.d.ts
</action>
</step>

<step n="10" goal="Validate and finalize backend code">
<action>Validate generated code:
- All content-types have valid schemas
- Services implement all specified methods
- Controllers map to correct services
- Routes reference existing controllers/actions
- No syntax errors
- Follows Strapi v5 conventions
- Code quality meets {code_quality_level} standard
</action>

<action>Report generation results:

**Backend Code Generated! ⚙️**

**Generated:**
- ✅ {{content_types_count}} content-types
- ✅ {{components_count}} components
- ✅ {{services_count}} services
- ✅ {{controllers_count}} controllers
- ✅ {{routes_count}} route groups
- ✅ {{policies_count}} policies
- ✅ {{middleware_count}} middlewares
- ✅ Plugin lifecycle hooks
- ✅ TypeScript definitions (if applicable)

**Location:** {{output_path}}/server/

**Code Quality:** {code_quality_level}
**Strapi Version:** v5

Backend code ready for integration! ✅
</action>
</step>

</workflow>
