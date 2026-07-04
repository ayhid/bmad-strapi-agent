# Review Plugin - Code Quality and Security Review

<critical>The workflow execution engine is governed by: {project-root}/.bmad/core/tasks/workflow.xml</critical>
<critical>You MUST have already loaded and processed: {project-root}/.bmad/custom/modules/strapi-plugin-forge/workflows/review-plugin/workflow.yaml</critical>
<critical>This workflow is invoked by other workflows - expects plugin code path as input</critical>
<critical>Communicate in {communication_language} throughout execution</critical>

<workflow>

<step n="1" goal="Load plugin code and metadata">
<action>Receive inputs from calling workflow:
- Plugin code path ({{plugin_path}})
- Architecture blueprint ({{plugin_architecture}})
- Code quality level ({code_quality_level})
- Strapi version ({strapi_version})

Load all plugin code:
- Server-side code (content-types, services, controllers, routes)
- Admin-side code (components, pages, hooks)
- Tests
- Configuration files
- Documentation
</action>

<action>Initialize review checklist based on {code_quality_level}:
- Standard: Essential checks
- Strict: All standard + enhanced quality checks
- Enterprise: All strict + security/performance deep analysis
</action>
</step>

<step n="2" goal="Review Strapi v5 best practices compliance">
<action>Validate compliance with Strapi v5 conventions:

**Content-Type Schemas:**
- Proper schema.json structure
- Correct field types and validation
- Proper UID format
- Relations configured correctly
- Lifecycle hooks properly implemented

**Service Layer:**
- Services use the Document Service API (strapi.documents) correctly — flag deprecated entityService usage
- Services follow single responsibility
- Proper error handling
- Logging implemented
- Business logic isolated from controllers

**Controller Layer:**
- Controllers are thin (logic in services)
- Proper use of createCoreController factory
- Request validation present
- Appropriate HTTP status codes
- Consistent response formats

**Routes:**
- Proper route structure (admin vs content-api)
- RESTful conventions followed
- Policies and middlewares correctly applied
- Route permissions configured

**Admin UI:**
- Design System components used correctly
- Proper plugin registration
- Translation files present
- Accessibility features implemented

Document violations as {{best_practices_violations}}
</action>
</step>

<step n="3" goal="Security vulnerability scan">
<action>Scan for common security vulnerabilities:

**SQL Injection:**
- Check for raw queries without sanitization
- Verify proper use of the Document Service API / query builder
- No string concatenation in queries

**XSS (Cross-Site Scripting):**
- User input properly sanitized in admin UI
- Output encoding in place
- No dangerous HTML rendering (dangerouslySetInnerHTML)

**Authentication/Authorization:**
- Routes have proper permissions
- Policies enforce access control
- No hardcoded credentials
- Sensitive data not exposed in API responses

**Data Validation:**
- Input validation on all endpoints
- Schema validation enforced
- File upload validation (if applicable)
- Rate limiting considerations

**Dependency Vulnerabilities:**
- Check for known vulnerabilities in dependencies
- Outdated packages identified
- Unused dependencies removed

**Configuration Security:**
- No secrets in code
- Environment variables used for sensitive config
- Proper CORS configuration

Document security issues as {{security_issues}} with severity (critical/high/medium/low)
</action>
</step>

<step n="4" goal="Performance analysis">
<action>Analyze performance patterns:

**Database Queries:**
- No N+1 query problems
- Proper use of populate/select
- Indexes suggested where needed
- Pagination implemented for lists

**Memory Management:**
- No memory leaks in event listeners
- Proper cleanup in lifecycle hooks
- Large file handling optimized

**API Performance:**
- Response payload size optimized
- Caching strategies considered
- Bulk operations available where needed

**Admin UI Performance:**
- Component lazy loading used
- React.memo/useMemo used appropriately
- No unnecessary re-renders
- Large lists virtualized

Document performance issues as {{performance_issues}}
</action>
</step>

<step n="5" goal="Code quality assessment">
<action>Evaluate code quality and maintainability:

**Code Structure:**
- Proper separation of concerns
- DRY principle followed (no duplication)
- Functions have single responsibility
- Appropriate abstraction levels

**Naming:**
- Clear, descriptive names
- Consistent naming conventions
- No abbreviations or unclear acronyms

**Error Handling:**
- Try-catch blocks where needed
- Errors logged appropriately
- User-friendly error messages
- Proper error propagation

**Code Complexity:**
- Functions not too long (< 50 lines typically)
- Cyclomatic complexity reasonable
- Nested conditionals minimized
- Complex logic documented

**Type Safety:**
- PropTypes or TypeScript types present
- Type checking enforced (if applicable)
- No implicit any types (TypeScript)

Document code quality issues as {{code_quality_issues}}
</action>
</step>

<step n="6" goal="Design System compliance review">
<action if="admin UI exists">Review Design System usage:

**Component Usage:**
- Only Design System components used
- No custom styled components without justification
- Components used with correct props
- Layout components used appropriately

**Accessibility:**
- ARIA attributes present
- Keyboard navigation supported
- Focus management correct
- Color contrast meets WCAG standards

**Responsiveness:**
- UI works on different screen sizes
- Mobile-friendly layouts
- No hardcoded pixel values

Document Design System issues as {{design_system_issues}}
</action>
</step>

<step n="7" goal="Test coverage validation">
<action>Review test quality and coverage:

**Coverage Metrics:**
- Line coverage >80%
- Branch coverage >80%
- Function coverage >80%
- Statement coverage >80%

**Test Quality:**
- Tests are meaningful (not just shallow coverage)
- Critical paths tested
- Error scenarios tested
- Edge cases covered
- Tests are isolated and independent
- No flaky tests

**Test Organization:**
- Tests well-structured
- Clear test descriptions
- Proper use of setup/teardown
- Mocks used appropriately

Document test coverage gaps as {{test_coverage_gaps}}
</action>
</step>

<step n="8" goal="Calculate quality scores">
<action>Calculate scores for each category:

**Best Practices Score (0-100):**
- Based on Strapi v5 compliance
- Deduct points for violations
- Weight by severity

**Security Score (0-100):**
- Based on vulnerabilities found
- Critical: -50 points each
- High: -20 points each
- Medium: -10 points each
- Low: -5 points each

**Performance Score (0-100):**
- Based on performance issues
- Major bottlenecks: -20 points
- Minor issues: -5 points

**Code Quality Score (0-100):**
- Based on maintainability metrics
- Complexity issues: -10 points
- Structure issues: -5 points

**Design System Score (0-100):**
- Component usage: 40%
- Accessibility: 40%
- Responsiveness: 20%

**Test Coverage Score (0-100):**
- Based on coverage metrics
- Coverage <80%: proportional deduction
- Missing critical tests: -20 points

**Overall Quality Score:**
- Weighted average of all scores
- Best practices: 25%
- Security: 25%
- Performance: 15%
- Code quality: 15%
- Design System: 10%
- Test coverage: 10%

Store scores as {{quality_scores}}
</action>
</step>

<step n="9" goal="Generate review report">
<action>Create comprehensive review report:

```markdown
# Code Review Report

**Plugin:** {{plugin_name}}
**Review Date:** {{date}}
**Reviewer:** Basir (Code Reviewer Agent)
**Quality Standard:** {code_quality_level}
**Strapi Version:** {strapi_version}

## Executive Summary

**Overall Quality Score:** {{overall_score}}/100

{{overall_assessment}}

## Scores by Category

| Category | Score | Status |
|----------|-------|--------|
| Best Practices | {{bp_score}}/100 | {{bp_status}} |
| Security | {{sec_score}}/100 | {{sec_status}} |
| Performance | {{perf_score}}/100 | {{perf_status}} |
| Code Quality | {{cq_score}}/100 | {{cq_status}} |
| Design System | {{ds_score}}/100 | {{ds_status}} |
| Test Coverage | {{tc_score}}/100 | {{tc_status}} |

## Critical Issues (Must Fix)

{{critical_issues_list}}

## Major Issues (Should Fix)

{{major_issues_list}}

## Minor Issues (Nice to Fix)

{{minor_issues_list}}

## Detailed Findings

### Best Practices Compliance

{{best_practices_violations}}

### Security Vulnerabilities

{{security_issues}}

### Performance Issues

{{performance_issues}}

### Code Quality

{{code_quality_issues}}

### Design System Compliance

{{design_system_issues}}

### Test Coverage

{{test_coverage_gaps}}

## Recommendations

{{prioritized_recommendations}}

## Conclusion

{{review_conclusion}}

---

_Generated by Strapi Plugin Forge on {{date}}_
```

Store report as {{review_report}}
</action>
</step>

<step n="10" goal="Finalize and return review results">
<action>Report review results to calling workflow:

**Code Review Complete! 🔍**

**Overall Score:** {{overall_score}}/100

**Issues Found:**
- 🔴 Critical: {{critical_count}}
- 🟡 Major: {{major_count}}
- 🟢 Minor: {{minor_count}}

**Top 3 Issues:**
1. {{issue_1}}
2. {{issue_2}}
3. {{issue_3}}

**Quality Assessment:** {{quality_assessment}}

Review report returned to calling workflow for next steps.
</action>

<action>Return review data to calling workflow:
- Overall score: {{overall_score}}
- Category scores: {{quality_scores}}
- Critical issues: {{critical_issues}}
- Full report: {{review_report}}
- Recommended actions: {{recommended_actions}}
</action>
</step>

</workflow>
