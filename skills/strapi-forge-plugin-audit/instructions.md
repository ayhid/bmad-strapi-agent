# Plugin Audit & Optimization - Review Existing Plugins

<critical>Complete the On Activation steps in SKILL.md (customization resolved, config loaded) before executing this workflow</critical>
<critical>Communicate in {communication_language} throughout the workflow execution process</critical>
<critical>For {{date}}: ask or derive the current date when stamping documents</critical>

<workflow>

<step n="1" goal="Locate and load plugin">
<ask>Path to the Strapi plugin to audit?</ask>
<action>Store as {{plugin_path}}</action>

<action>Verify the path is valid and contains a Strapi plugin:
- Check for package.json with "strapi" field
- Check for server/ and/or admin/ directories
- Identify plugin structure and version
</action>

<action>Load and analyze plugin structure:
- Read package.json
- Identify all server-side code (content-types, services, controllers, routes)
- Identify all admin-side code (pages, components)
- Check for tests
- Check for documentation
- List all dependencies
</action>

<action>Store plugin metadata as {{plugin_metadata}}:
- Plugin name and version
- Strapi version compatibility
- File/folder structure
- Code statistics (LOC, files count)
- Dependencies list
</action>
</step>

<step n="2" goal="Perform comprehensive code review">
<invoke-skill name="strapi-forge-review-plugin">
Invoke Basir (the `strapi-forge-review-plugin` skill) to perform deep analysis.

Pass to Basir:
- Plugin path: {{plugin_path}}
- Plugin metadata: {{plugin_metadata}}
- Code quality level: {code_quality_level}
- Strapi version target: Strapi v5
- Review scope: Full audit (quality, security, performance, best practices)

Basir performs:
- Strapi v5 best practices compliance check
- Security vulnerability scan (SQL injection, XSS, auth issues)
- Performance analysis (N+1 queries, memory leaks, inefficient code)
- Code quality assessment (maintainability, readability, complexity)
- Design System compliance (if admin UI present)
- Test coverage analysis
- Documentation quality check
- Dependency audit (outdated, vulnerable packages)
</invoke-skill>

<action>Basir produces comprehensive audit report with:
- Executive summary
- Overall quality score (0-100)
- Critical issues (must fix)
- Major issues (should fix)
- Minor issues (nice to fix)
- Security vulnerabilities with severity
- Performance bottlenecks
- Best practices violations
- Recommendations with priorities
- Code examples for each issue
</action>

<action>Store audit report as {{audit_report}}</action>
</step>

<step n="3" goal="Analyze plugin architecture">
<action>Review plugin architecture for design issues:

**Architecture Analysis:**
- Service layer design (responsibilities, separation of concerns)
- Controller complexity (thin vs. fat controllers)
- Data model design (schema structure, relations)
- API design (RESTful patterns, endpoint naming)
- Admin UI architecture (component structure, state management)
- Code organization (folder structure, file naming)
- Dependency management (coupling, cohesion)

Identify architectural smells:
- God classes/services doing too much
- Circular dependencies
- Tight coupling between layers
- Missing abstractions
- Violation of SOLID principles
- Duplicate code/logic
</action>

<action>Add architecture analysis to {{audit_report}}</action>
</step>

<step n="4" goal="Test coverage analysis">
<action>Analyze testing quality:

**Test Coverage:**
- Unit test coverage percentage
- Integration test coverage
- Component test coverage (UI)
- Critical paths tested
- Edge cases covered
- Test quality (meaningful vs shallow tests)

Identify testing gaps:
- Untested services/controllers
- Missing error scenario tests
- No integration tests for critical flows
- UI components without tests
- Test fixtures inadequate

Assess test code quality:
- Test maintainability
- Test isolation
- Test performance (flaky tests)
</action>

<action>Add test analysis to {{audit_report}}</action>
</step>

<step n="5" goal="Documentation review">
<action>Evaluate documentation completeness:

**Documentation Assessment:**
- README quality (installation, usage, examples)
- API documentation (endpoints, parameters, responses)
- Code comments (inline documentation)
- Configuration documentation
- Troubleshooting guides
- Contribution guidelines
- Changelog maintenance

Identify documentation gaps:
- Missing or outdated README sections
- Undocumented API endpoints
- Complex code without comments
- No usage examples
- Missing configuration options
</action>

<action>Add documentation analysis to {{audit_report}}</action>
</step>

<step n="6" goal="Dependency and security audit">
<action>Audit dependencies for security and maintenance:

**Dependency Analysis:**
- Outdated packages (check versions)
- Security vulnerabilities (known CVEs)
- Deprecated dependencies
- Unused dependencies
- License compatibility
- Bundle size impact

Check:
- npm audit results
- Strapi compatibility
- Breaking changes in updates
- Alternative package recommendations
</action>

<action>Add dependency audit to {{audit_report}}</action>
</step>

<step n="7" goal="Generate improvement roadmap">
<action>Based on all analyses, create prioritized improvement roadmap:

**Roadmap Structure:**

**Immediate (Critical - Fix Now):**
- Security vulnerabilities
- Breaking bugs
- Strapi incompatibilities
- Critical performance issues

**Short-term (Major - Fix Soon):**
- Significant code quality issues
- Missing critical tests
- Performance bottlenecks
- Major best practices violations

**Medium-term (Minor - Plan to Fix):**
- Code refactoring opportunities
- Documentation improvements
- Test coverage gaps
- Minor optimizations

**Long-term (Enhancement - Consider):**
- Architecture improvements
- Feature additions
- Advanced optimizations
- Nice-to-have improvements

For each item:
- Issue description
- Impact assessment (severity, affected areas)
- Effort estimate (complexity)
- Implementation suggestions
- Code examples or references
</action>

<action>Add improvement roadmap to {{audit_report}}</action>
</step>

<step n="8" goal="Present audit report">
<action>Generate comprehensive audit document:

Save audit report to: {output_folder}/plugin-audit-{{plugin_name}}-{{date}}.md

**Report Structure:**
```markdown
# Plugin Audit Report: {{plugin_name}}

**Date:** {{date}}
**Audited by:** {user_name} (via Strapi Plugin Forge)
**Strapi Version Target:** Strapi v5
**Quality Standard:** {code_quality_level}

## Executive Summary

{{overall_assessment}}

**Overall Score:** {{quality_score}}/100

**Issue Counts:**
- Critical: {{critical_count}}
- Major: {{major_count}}
- Minor: {{minor_count}}

## Plugin Overview

{{plugin_metadata}}

## Detailed Findings

### 1. Code Quality (Score: X/100)

{{code_quality_findings}}

### 2. Security (Score: X/100)

{{security_findings}}

### 3. Performance (Score: X/100)

{{performance_findings}}

### 4. Architecture (Score: X/100)

{{architecture_findings}}

### 5. Testing (Score: X/100)

{{testing_findings}}

### 6. Documentation (Score: X/100)

{{documentation_findings}}

### 7. Dependencies (Score: X/100)

{{dependency_findings}}

## Improvement Roadmap

### Immediate Actions (Critical)

{{critical_improvements}}

### Short-term Actions (Major)

{{major_improvements}}

### Medium-term Actions (Minor)

{{minor_improvements}}

### Long-term Enhancements

{{enhancement_suggestions}}

## Recommendations

{{specific_recommendations}}

---

_Generated by Strapi Plugin Forge on {{date}}_
```
</action>

<action>Present summary to {user_name}:

**Plugin Audit Complete! 📊**

**Plugin:** {{plugin_name}}
**Overall Score:** {{quality_score}}/100

**Issues Found:**
- 🔴 Critical: {{critical_count}}
- 🟡 Major: {{major_count}}
- 🟢 Minor: {{minor_count}}

**Top 3 Issues:**
1. {{top_issue_1}}
2. {{top_issue_2}}
3. {{top_issue_3}}

**Report saved to:** {output_folder}/plugin-audit-{{plugin_name}}-{{date}}.md
</action>
</step>

<step n="9" goal="Offer automated improvements" optional="true">
<ask>Would you like to:
1. Apply automated fixes for critical issues
2. Apply automated fixes for all fixable issues
3. Generate improvement plan (no auto-fixes)
4. Exit audit

Select option (1-4):
</ask>

<check if="option 1 or option 2">
<action>Apply automated fixes:

**Auto-fixable Issues:**
- Code formatting (Prettier)
- Linting errors (ESLint auto-fix)
- Dependency updates (safe updates)
- Simple refactorings (variable naming, unused imports)
- Documentation generation (missing sections)

For each fixable issue:
- Create backup of original file
- Apply fix
- Verify no breaking changes
- Document change made

Generate fix report with:
- Issues fixed automatically
- Issues requiring manual intervention
- Files modified
- Testing recommendations
</action>

<action>Save fix report to: {output_folder}/plugin-audit-fixes-{{plugin_name}}-{{date}}.md</action>

<action>Report fixes applied:

**Automated Fixes Applied! 🔧**

**Fixed:**
- {{fix_count}} issues resolved automatically
- {{file_count}} files modified

**Still Requires Manual Fix:**
- {{manual_count}} issues need human review

**Backup created at:** {{backup_path}}

**Next steps:**
1. Review changes
2. Run tests
3. Test plugin in Strapi
4. Commit changes if satisfied
</action>
</check>

<check if="option 3">
<action>Generate detailed improvement plan without applying fixes</action>
</check>

<check if="option 4">
<action>Thank user and complete audit workflow</action>
</check>
</step>

</workflow>
