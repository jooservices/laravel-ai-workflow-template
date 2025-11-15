# Template Customization Guide

This guide explains how to customize this Laravel documentation template for your project.

## Placeholder Replacement

Replace the following placeholders throughout the documentation:

### Project Information

- `{ProjectName}` → Your project name (e.g., "MyLaravelApp")
- `{CompanyName}` → Your company name (default: "JOOservices Ltd")
- `{OwnerEmail}` → Your email (default: "jooservices@gmail.com")

### Module & Domain Names

- `{DomainModule}` → Your domain module name (e.g., "Product", "Order", "Payment")
- `{DomainModule1}`, `{DomainModule2}`, `{DomainModule3}` → Multiple domain modules
- `{BusinessDomain}` → Business domain name (e.g., "E-commerce", "Inventory")
- `{BusinessDomain1}`, `{BusinessDomain2}` → Multiple business domains

### Service & Entity Names

- `{ServiceName}` → External service name (e.g., "Stripe", "PayPal", "WordPress")
- `{ExternalService}` → Generic external service reference
- `{Entity}` → Entity/model name (e.g., "Product", "Order", "User")
- `{entity}` → Lowercase entity name (e.g., "product", "order", "user")
- `{entities}` → Plural lowercase (e.g., "products", "orders", "users")
- `{Entities}` → Plural capitalized (e.g., "Products", "Orders", "Users")

### Technology References

- `{DocumentationTool}` → Your documentation tool (e.g., "LM Studio", "GitBook")
- `{servicename}` → Lowercase service name for channels/queues

## Copyright Information

The template includes copyright information in the footer of all markdown files:

```
Copyright (c) 2025 Viet Vu <jooservices@gmail.com>
Company: JOOservices Ltd
All rights reserved.
```

**Important:** 
- Keep "Viet Vu <jooservices@gmail.com>" as the copyright owner
- Keep "JOOservices Ltd" as the company name
- These are part of the template's copyright, not placeholders

## Optional Technologies

This template includes references to optional technologies:

### nwidart/laravel-modules

- **Purpose:** Modular architecture for Laravel applications
- **Status:** Optional guide - use if your project uses this package
- **Location:** Referenced in module creation guides and architecture principles
- **Action:** If not using, you can skip module-related guides

### Inertia.js

- **Purpose:** Adapter for connecting Laravel backend with Vue.js frontend
- **Status:** Optional guide - use if your project uses this framework
- **Location:** Referenced in frontend patterns guide
- **Action:** If not using, you can skip Inertia.js-specific sections

## Customization Steps

1. **Replace Project Name**
   - Search and replace `{ProjectName}` with your actual project name
   - Update README.md title and description

2. **Replace Domain/Module Names**
   - Search and replace `{DomainModule}` with your actual module names
   - Update examples in `guides/module-creation.md`
   - Update module references in `architecture/principles.md`

3. **Replace Service Names**
   - Search and replace `{ServiceName}` with your external service names
   - Update SDK examples in guides
   - Update integration standards in `reference/standards.md`

4. **Update Code Examples**
   - Replace `{Entity}` with your actual model names
   - Update controller, service, and repository examples
   - Update route examples

5. **Remove Unused Sections**
   - Remove guides for technologies you don't use
   - Remove optional sections that don't apply to your project

6. **Add Project-Specific Content**
   - Add your own plans in `plans/` directory
   - Add your own decisions in `decisions/` directory
   - Add your own retrospectives in `retrospectives/` directory

## File Structure

```
laravel-docs-template/
├── README.md                    # Main documentation index
├── CUSTOMIZATION.md            # This file
├── ai-workflow.md              # AI development workflow (customize for your team)
├── architecture/               # Architecture principles and flow
├── development/                # Development guidelines and code quality
├── guides/                     # Step-by-step implementation guides
├── reference/                  # Quick lookup standards
├── decisions/                  # Architecture Decision Records (ADRs)
├── retrospectives/             # Post-mortems and lessons learned
└── stories/                    # User stories and feature documentation
```

## Validation Checklist

After customization, verify:

- [ ] All `{ProjectName}` placeholders replaced
- [ ] All `{DomainModule}` placeholders replaced
- [ ] All `{ServiceName}` placeholders replaced
- [ ] All `{Entity}` placeholders replaced
- [ ] Code examples use your actual module/entity names
- [ ] Copyright footer present in all markdown files
- [ ] Optional technology references clarified (if not using)
- [ ] Project-specific plans/decisions added (if applicable)

## Support

For questions or issues with this template:
- **Author:** Viet Vu <jooservices@gmail.com>
- **Company:** JOOservices Ltd

---

**Copyright (c) 2025 Viet Vu <jooservices@gmail.com>**  
**Company: JOOservices Ltd**  
All rights reserved.

