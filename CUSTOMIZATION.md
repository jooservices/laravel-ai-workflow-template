# Integrating This Documentation Submodule

This guide explains how to adopt and customize this Laravel documentation submodule in your own project.

## Placeholder Reference

The documentation uses a few generic placeholders in examples. When you create **your own modules, plans, ADRs, and guides in your main project**, use these as patterns and replace them with your real names.

### Project Information (for your parent project)

- `{ProjectName}` → Your project name (e.g., "MyLaravelApp")  
- `{CompanyName}` → Your company name (default examples use "JOOservices Ltd")  
- `{OwnerEmail}` → Your email (default examples use "jooservices@gmail.com")

### Module & Domain Names

- `{DomainModule}` → Domain module name in your app (e.g., "Product", "Order", "Payment")
- `{DomainModule1}`, `{DomainModule2}`, `{DomainModule3}` → Multiple domain modules
- `{BusinessDomain}` → Business domain name (e.g., "E-commerce", "Inventory")
- `{BusinessDomain1}`, `{BusinessDomain2}` → Multiple business domains

### Service & Entity Names

- `{ServiceName}` → External service name (e.g., "Stripe", "PayPal", "WordPress")
- `{ExternalService}` → Generic external service reference
- `{Entity}` / `{entity}` / `{entities}` / `{Entities}` → Example entity/model names

### Technology References

- `{DocumentationTool}` → Any documentation automation tool you may use (e.g., "LM Studio", "GitBook")
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

## How to Use This Submodule in Your Project

1. **Add as Git Submodule**
   - Add this repository under your main project, for example:
     - `<project-root>/docs/` (recommended) or
     - `<project-root>/documentation/`
   - Treat the root of this submodule as your documentation root.

2. **Decide What to Keep vs. Ignore**
   - Keep core docs that are generally useful for any Laravel project:
     - `architecture/`, `development/`, `reference/`, `guides/` (most of them)
   - Mark or ignore optional/stack‑specific docs in your parent project:
     - WordPress‑specific examples
     - LM Studio / AI‑specific workflow docs
     - Inertia.js / Vue 3 frontend patterns (if not using them)

3. **Create Your Own Project-Specific Docs in the Parent Repo**
   - Add **plans** in your main repo (preferably under `docs/plans/` in the parent):
     - `plans/features/` – feature/product plans
     - `plans/technical/` – refactors, infra, technical debt
   - Add **decisions** (ADRs) in the parent project’s `docs/decisions/`.
   - Add **retrospectives** in the parent project’s `docs/retrospectives/`.

4. **Use Placeholders as Patterns, Not Required Replacements**
   - When you copy examples into your main project:
     - Replace `{DomainModule}`, `{ServiceName}`, `{Entity}`, etc. with your real names.
   - You generally do **not** need to bulk‑search/replace these placeholders inside this submodule itself.

5. **Prune What You Don’t Need (Optional)**
   - If you fork this repo instead of using it read‑only:
     - You may remove guides for technologies you will never use (e.g., WordPress‑only sections).
     - You can add your own domain‑specific guides under `guides/`.

## File Structure (Recommended Layout in Parent Project)

```text
<project-root>/
└── docs/                      # Your project’s documentation root (contains this submodule)
    ├── README.md              # Main documentation index (from this repo or overridden)
    ├── CUSTOMIZATION.md       # This file (integration guide)
    ├── ai-workflow.md         # AI development workflow (optional)
    ├── architecture/          # Architecture principles and flow
    ├── development/           # Development guidelines and code quality
    ├── guides/                # Step-by-step implementation guides
    ├── reference/             # Quick lookup standards
    ├── decisions/             # Architecture Decision Records (ADRs)
    ├── retrospectives/        # Post-mortems and lessons learned
    └── plans/                 # Your project-specific plans (in parent project)
```

## Integration Checklist

In your main project, verify:

- [ ] This submodule is mounted under a clear path (e.g., `docs/`).
- [ ] You know which guides are **optional** (WordPress, LM Studio, Inertia.js, etc.).
- [ ] Project-specific plans are added under `docs/plans/` in the parent repo.
- [ ] Project-specific decisions are added under `docs/decisions/` in the parent repo.
- [ ] Project-specific retrospectives are added under `docs/retrospectives/` in the parent repo.

## Support

For questions or issues with this template:
- **Author:** Viet Vu <jooservices@gmail.com>
- **Company:** JOOservices Ltd

---

**Copyright (c) 2025 Viet Vu <jooservices@gmail.com>**  
**Company: JOOservices Ltd**  
Licensed under the MIT License.

