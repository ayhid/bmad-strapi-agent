# Generate Tests - Comprehensive Test Suite Generation

<critical>This skill is typically invoked by another Strapi Plugin Forge skill or agent - it expects plugin code as input. If any input is missing, ask the user for it before proceeding.</critical>
<critical>Communicate in {communication_language} throughout execution</critical>

<workflow>

<step n="1" goal="Analyze plugin code for test requirements">
<action>Receive inputs from the invoking skill or agent:
- Plugin architecture ({{plugin_architecture}})
- Backend code path ({{backend_code_path}})
- Frontend code path ({{frontend_code_path}})
- Output path ({{output_path}})
- Code quality level ({code_quality_level})

Analyze code to identify:
- Services to test (methods, logic paths)
- Controllers to test (endpoints, request/response handling)
- API routes to test (integration tests)
- UI components to test (rendering, interactions)
- Utilities and helpers to test
- Edge cases and error scenarios
</action>

<action>Store test specifications as {{test_specs}}</action>
</step>

<step n="2" goal="Generate test configuration">
<action>Generate jest.config.js:

```javascript
module.exports = {
  testEnvironment: 'node',
  coverageDirectory: './coverage',
  collectCoverageFrom: [
    'server/**/*.js',
    'admin/src/**/*.{js,jsx}',
    '!**/node_modules/**',
    '!**/tests/**',
  ],
  testMatch: [
    '**/tests/**/*.test.js',
    '**/tests/**/*.test.jsx',
  ],
  setupFilesAfterEnv: ['<rootDir>/tests/setup.js'],
  transform: {
    '^.+\\.jsx?$': 'babel-jest',
  },
  moduleNameMapper: {
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
  },
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80,
    },
  },
};
```

Generate to: {{output_path}}/jest.config.js
</action>

<action>Generate tests/setup.js:

```javascript
// Test setup and global mocks

// Document Service API mock (Strapi v5) — shared instance so tests can
// configure and assert on the same jest.fn() calls
const documentsApi = {
  findMany: jest.fn(),
  findOne: jest.fn(),
  create: jest.fn(),
  update: jest.fn(),
  delete: jest.fn(),
};

global.strapi = {
  log: {
    error: jest.fn(),
    warn: jest.fn(),
    info: jest.fn(),
    debug: jest.fn(),
  },
  documents: jest.fn(() => documentsApi),
  plugin: jest.fn(() => ({
    service: jest.fn(),
    controller: jest.fn(),
  })),
};
```
</action>
</step>

<step n="3" goal="Generate unit tests for services">
<action>For each service:

```javascript
const {{serviceName}} = require('../../../server/services/{{serviceName}}');

describe('{{serviceName}} Service', () => {
  let service;

  beforeEach(() => {
    jest.clearAllMocks();
    service = {{serviceName}}({ strapi: global.strapi });
  });

  {{for each method}}
  describe('{{methodName}}', () => {
    it('should {{expected_behavior}}', async () => {
      // Arrange
      const {{input_data}} = {{test_data}};
      const {{expected_output}} = {{expected_data}};

      global.strapi.documents().{{operation}}.mockResolvedValue({{expected_output}});

      // Act
      const result = await service.{{methodName}}({{input_data}});

      // Assert
      expect(result).toEqual({{expected_output}});
      expect(global.strapi.documents().{{operation}}).toHaveBeenCalledWith(
        {{expected_args}}
      );
    });

    it('should handle errors when {{error_scenario}}', async () => {
      // Arrange
      const error = new Error('{{error_message}}');
      global.strapi.documents().{{operation}}.mockRejectedValue(error);

      // Act & Assert
      await expect(service.{{methodName}}({{input}})).rejects.toThrow(error);
    });

    {{additional_test_cases}}
  });
  {{endfor}}
});
```

Generate to: {{output_path}}/tests/unit/services/{{serviceName}}.test.js
</action>

<action>Store service tests count as {{service_tests_count}}</action>
</step>

<step n="4" goal="Generate unit tests for controllers">
<action>For each controller:

```javascript
const {{controllerName}} = require('../../../server/controllers/{{controllerName}}');

describe('{{controllerName}} Controller', () => {
  let controller;
  let ctx;

  beforeEach(() => {
    jest.clearAllMocks();

    // Mock Koa context
    ctx = {
      request: {
        body: {},
        query: {},
      },
      params: {},
      throw: jest.fn(),
    };

    controller = {{controllerName}}({ strapi: global.strapi });
  });

  {{for each action}}
  describe('{{actionName}}', () => {
    it('should {{expected_behavior}}', async () => {
      // Arrange
      ctx.request.body = {{request_body}};
      ctx.params = {{params}};

      const mockServiceResponse = {{mock_response}};
      global.strapi.plugin().service().{{serviceMethod}}.mockResolvedValue(mockServiceResponse);

      // Act
      await controller.{{actionName}}(ctx);

      // Assert
      expect(ctx.body).toEqual({{expected_response}});
      expect(global.strapi.plugin().service().{{serviceMethod}}).toHaveBeenCalledWith(
        {{expected_service_args}}
      );
    });

    it('should throw error with {{status_code}} when {{error_scenario}}', async () => {
      // Arrange
      ctx.request.body = {{invalid_request}};

      // Act
      await controller.{{actionName}}(ctx);

      // Assert
      expect(ctx.throw).toHaveBeenCalledWith({{status_code}}, {{error_message}});
    });

    {{additional_test_cases}}
  });
  {{endfor}}
});
```

Generate to: {{output_path}}/tests/unit/controllers/{{controllerName}}.test.js
</action>

<action>Store controller tests count as {{controller_tests_count}}</action>
</step>

<step n="5" goal="Generate integration tests for API endpoints">
<action>For each API endpoint group:

```javascript
const request = require('supertest');

describe('{{endpoint_group}} API Endpoints', () => {
  let app;

  beforeAll(async () => {
    // Setup test Strapi instance
    app = await setupStrapi();
  });

  afterAll(async () => {
    await teardownStrapi();
  });

  {{for each endpoint}}
  describe('{{method}} {{path}}', () => {
    it('should return {{expected_status}} and {{expected_response}}', async () => {
      const response = await request(app)
        .{{method}}('{{full_path}}')
        .send({{request_body}})
        .expect({{expected_status}});

      expect(response.body).toMatchObject({{expected_response_shape}});
    });

    it('should return {{error_status}} when {{error_condition}}', async () => {
      const response = await request(app)
        .{{method}}('{{full_path}}')
        .send({{invalid_request_body}})
        .expect({{error_status}});

      expect(response.body.error).toBeDefined();
    });

    {{if_auth_required}}
    it('should return 401 when not authenticated', async () => {
      await request(app)
        .{{method}}('{{full_path}}')
        .expect(401);
    });
    {{endif}}

    {{additional_endpoint_tests}}
  });
  {{endfor}}
});
```

Generate to: {{output_path}}/tests/integration/api/{{endpoint-group}}.test.js
</action>

<action>Store integration tests count as {{integration_tests_count}}</action>
</step>

<step n="6" goal="Generate component tests for UI">
<action if="frontend code exists">For each React component:

```jsx
import React from 'react';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import { IntlProvider } from 'react-intl';
import { ThemeProvider } from '@strapi/design-system';
import {{ComponentName}} from '../../../admin/src/components/{{ComponentName}}';

const renderComponent = (props = {}) => {
  return render(
    <ThemeProvider>
      <IntlProvider locale="en" messages={{}}>
        <{{ComponentName}} {...props} />
      </IntlProvider>
    </ThemeProvider>
  );
};

describe('{{ComponentName}} Component', () => {
  it('should render correctly with default props', () => {
    renderComponent();

    expect(screen.getByText('{{expected_text}}')).toBeInTheDocument();
  });

  it('should handle {{user_interaction}}', async () => {
    const mockHandler = jest.fn();
    renderComponent({ {{handler_prop}}: mockHandler });

    // Simulate user interaction
    fireEvent.{{event_type}}(screen.getByRole('{{role}}'));

    await waitFor(() => {
      expect(mockHandler).toHaveBeenCalledWith({{expected_args}});
    });
  });

  it('should display validation error when {{error_condition}}', () => {
    renderComponent({ error: '{{error_message}}' });

    expect(screen.getByText('{{error_message}}')).toBeInTheDocument();
  });

  it('should be accessible', () => {
    const { container } = renderComponent();

    // Check ARIA attributes
    expect(screen.getByRole('{{role}}')).toHaveAttribute('aria-label', '{{label}}');

    // Check keyboard navigation
    const element = screen.getByRole('{{role}}');
    element.focus();
    expect(element).toHaveFocus();
  });

  {{additional_component_tests}}
});
```

Generate to: {{output_path}}/tests/components/{{ComponentName}}.test.jsx
</action>

<action>Store component tests count as {{component_tests_count}}</action>
</step>

<step n="7" goal="Generate test fixtures and mocks">
<action>Create test fixtures for common data:

```javascript
// tests/fixtures/{{entityName}}.js

module.exports = {
  valid{{EntityName}}: {
    {{field_values}}
  },

  invalid{{EntityName}}: {
    {{invalid_field_values}}
  },

  {{entityName}}List: [
    {{multiple_entities}}
  ],
};
```

Generate to: {{output_path}}/tests/fixtures/
</action>

<action>Create mock utilities:

```javascript
// tests/mocks/strapi.js

const createMockStrapi = () => {
  const documentsApi = {
    findMany: jest.fn(),
    findOne: jest.fn(),
    create: jest.fn(),
    update: jest.fn(),
    delete: jest.fn(),
  };

  return {
    documents: jest.fn(() => documentsApi),
    plugin: jest.fn((name) => ({
      service: jest.fn((service) => ({
        {{service_methods}}
      })),
      controller: jest.fn(),
    })),
    log: {
      error: jest.fn(),
      warn: jest.fn(),
      info: jest.fn(),
    },
  };
};

module.exports = { createMockStrapi };
```
</action>
</step>

<step n="8" goal="Generate test helpers">
<action>Create test helper utilities:

```javascript
// tests/helpers/setup.js

const setupStrapi = async () => {
  // Initialize Strapi instance for integration tests
  const Strapi = require('@strapi/strapi');
  const app = await Strapi().load();
  await app.server.mount();
  return app;
};

const teardownStrapi = async () => {
  const app = strapi.server.httpServer;
  await strapi.destroy();
  app.close();
};

const authenticateRequest = (request) => {
  // Add authentication token to request
  return request.set('Authorization', `Bearer ${testToken}`);
};

module.exports = {
  setupStrapi,
  teardownStrapi,
  authenticateRequest,
};
```
</action>
</step>

<step n="9" goal="Update package.json with test scripts">
<action>Add test scripts to package.json:

```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "test:unit": "jest tests/unit",
    "test:integration": "jest tests/integration",
    "test:components": "jest tests/components"
  },
  "devDependencies": {
    "@testing-library/react": "^13.0.0",
    "@testing-library/jest-dom": "^5.16.0",
    "@testing-library/user-event": "^14.0.0",
    "jest": "^29.0.0",
    "babel-jest": "^29.0.0",
    "supertest": "^6.3.0",
    "identity-obj-proxy": "^3.0.0"
  }
}
```
</action>
</step>

<step n="10" goal="Validate and finalize test suite">
<action>Validate generated tests:
- All critical code paths have tests
- Tests cover success and error scenarios
- Tests are isolated and independent
- Tests use proper mocking
- Tests have clear descriptions
- Coverage target achievable (>80%)
- No flaky tests
- Tests follow {code_quality_level} standard
</action>

<action>Report generation results:

**Test Suite Generated! 🧪**

**Generated:**
- ✅ {{service_tests_count}} service unit tests
- ✅ {{controller_tests_count}} controller unit tests
- ✅ {{integration_tests_count}} API integration tests
- ✅ {{component_tests_count}} component tests
- ✅ Test fixtures and mocks
- ✅ Test configuration (Jest)
- ✅ Test helper utilities

**Location:** {{output_path}}/tests/

**Coverage Target:** >80% meaningful coverage
**Testing Frameworks:** Jest, React Testing Library
**Quality:** {code_quality_level}

**Test Commands:**
- `npm test` - Run all tests
- `npm run test:coverage` - With coverage report
- `npm run test:watch` - Watch mode

Test suite complete and ready! ✅
</action>
</step>

</workflow>
