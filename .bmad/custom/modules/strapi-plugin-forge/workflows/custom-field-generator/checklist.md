# Custom Field Generator - Validation Checklist

## Field Specification

- [ ] Field name defined (kebab-case)
- [ ] Display name clear and descriptive
- [ ] Purpose and use case documented
- [ ] Data storage format specified
- [ ] Validation rules defined
- [ ] Default value specified (if applicable)

## UI Component Design

- [ ] UI interaction type clearly defined
- [ ] Strapi Design System components identified
- [ ] Component structure designed
- [ ] User experience considered
- [ ] Accessibility requirements documented

## Server-Side Code

- [ ] Field registration code created (server/register.js)
- [ ] Field type correctly specified
- [ ] Field configuration complete
- [ ] Server-side code follows Strapi v5 patterns

## React Component

- [ ] Field component created (FieldComponent.jsx)
- [ ] Uses Strapi Design System components
- [ ] Props handled correctly (value, onChange, error, etc.)
- [ ] Component is accessible (ARIA, keyboard nav)
- [ ] Visual design matches specification
- [ ] Component handles edge cases gracefully

## Admin Registration

- [ ] Admin registration file complete (admin/src/index.js)
- [ ] Custom field registered correctly
- [ ] i18n labels defined
- [ ] Icon specified
- [ ] Field options configured (base, advanced)

## Validation

- [ ] Client-side validation implemented
- [ ] Server-side validation implemented
- [ ] Validation messages clear and helpful
- [ ] Required field logic works
- [ ] Format/range validation works
- [ ] Custom validation rules work

## Tests

- [ ] Component render tests created
- [ ] User interaction tests created
- [ ] Validation tests created
- [ ] Accessibility tests created
- [ ] All tests pass

## Documentation

- [ ] README.md complete with usage instructions
- [ ] Installation steps documented
- [ ] Field options documented
- [ ] Data format example provided
- [ ] Example usage code included
- [ ] Development and testing instructions included

## Integration

- [ ] package.json has correct dependencies
- [ ] Field appears in Content-Type Builder
- [ ] Field can be added to content-types
- [ ] Field works in content editor
- [ ] Data saves and loads correctly
- [ ] No console errors

## Quality

- [ ] Code follows {code_quality_level} standards
- [ ] Design System used correctly
- [ ] Component is reusable
- [ ] Code is maintainable
- [ ] Performance is acceptable
