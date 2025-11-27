# Implementation Guides

Feature-specific tutorials and complete code examples.

## Available Guides

### Optional Technology Guides

> **Note:** The following guides are for projects using specific packages. They are optional - use them if your project uses these technologies, or skip them if not.

- **nwidart/laravel-modules** - If your project uses this package, see module creation guide for patterns
- **Inertia.js** - If your project uses this framework, see frontend patterns guide for integration patterns

### Development Patterns
- [**RESTful API Design**](restful-api-design.md) - Complete guide to RESTful API design with UUID identifiers, HTTP methods, status codes, pagination, filtering, and security
- [**Laravel Components & Patterns**](laravel-components-patterns.md) - Complete guide to all Laravel components (FormRequest, Policy, Middleware, Resource, Event, Job, Command, etc.) with naming conventions and decision trees
- [**Testing Patterns**](testing-patterns.md) - Unit tests, feature tests, and testing strategies
- [**Error Handling**](error-handling.md) - Exception handling, error responses, and logging
- [**Frontend Patterns**](frontend-patterns.md) - Vue 3, TypeScript, and Inertia.js patterns
- [**Module Creation**](module-creation.md) - Step-by-step module creation workflow
- [**API Versioning**](api-versioning.md) - Semantic versioning and backward compatibility
- [**Security Best Practices**](security-best-practices.md) - Input validation, CSRF, rate limiting
- [**Performance Optimization**](performance-optimization.md) - Database optimization, caching, async calls
- [**Database Migrations**](database-migrations.md) - Migration standards, naming conventions, rollbacks, indexes, and foreign keys
- [**Database Transactions**](database-transactions.md) - Transaction boundaries, rollback handling, deadlock handling
- [**Seeders & Factories**](seeders-factories.md) - Database seeding patterns and factory usage for test data generation
- [**Queue Jobs**](queue-jobs.md) - Queue configuration, retry strategies, failed job handling, and monitoring

### Planning & Process
- [**Writing Plans**](writing-plans.md) - How to write technical implementation plans
- [**Directory Structure**](directory-structure.md) - Documentation directory organization and purpose

## Guide vs Plan

**Plans** (in `doc/plans/` - local) define **WHAT** to build:
- Task breakdown with Definition of Done
- Success metrics and acceptance criteria
- Time estimates and dependencies

**Guides** (this directory) show **HOW** to build:
- Complete code examples
- Best practices and patterns
- Common pitfalls and solutions

**Example:**
- **Plan:** "Implement {ServiceName} SDK with {FeatureName} endpoint"
- **Guide:** Full working code for {FeatureName} with all parameters

## Contributing

When creating a new guide:
1. Use clear, action-oriented title: "How to X", "Writing Y", "Implementing Z"
2. Include complete code examples with expected output
3. Show both success and error cases
4. Link to related architecture docs and existing guides
5. Add entry to this README with brief description

---

**Copyright (c) 2025 Viet Vu <jooservices@gmail.com>**  
**Company: JOOservices Ltd**  
Licensed under the MIT License.
