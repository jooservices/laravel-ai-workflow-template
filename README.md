# Laravel Documentation Hub

Reusable documentation hub for Laravel projects following the **Principle → Guideline → Standard** hierarchy.

> **Usage as Submodule**  
> Add this repository as a Git submodule under your main project’s `docs/` (or similar) directory, and customize only where needed (domain‑specific guides, examples, and branding).
>
> **Usage for AI Agents**  
> Any AI assistant (and human) working on the project **must read and follow** these documents before making changes or suggesting actions.

---

## 🎯 Purpose

- **Documentation for AI & humans**  
  This repo defines how work should be done in a Laravel codebase: architecture, module structure, quality gates, testing, and commit discipline. AI agents are expected to treat it as the **source of truth** for behavior and implementation patterns.

- **Reusable in any Laravel project**  
  The docs are framework‑level, not app‑specific. You can mount this repo under `docs/` in any Laravel project and layer your own plans/ADRs/retrospectives on top.

- **Modules are optional, but strongly recommended**  
  The architecture and examples assume:
  - A **Core** area for cross‑cutting technical concerns (logging, responses, base services, etc.).
  - **One module per business domain** (e.g., Billing, Catalog, Accounts), each with its own controllers, services, repositories, and models.  
  If your project doesn’t use a formal modules package, you can still follow the same **modular boundaries** in your folder structure.

---

## 🎯 Core Documentation (Start Here)

### 📐 Architecture

- [**Principles**](architecture/principles.md)  
  **What & Why** – Engineering principles with embedded guidelines and standards:
  - Type safety and PHP version policy
  - Modular architecture (core vs domain modules)
  - Service / repository / SDK layering
  - Testing, quality gates, and coverage
  - Security, performance, and documentation standards

- [**Flow**](architecture/flow.md)  
  **How the app works** – Request/response flow and high‑level patterns:
  - HTTP → Middleware → FormRequest → Controller → Service → Repository/SDK → Database/External API
  - When to use Resources vs raw JSON
  - Example end‑to‑end flows across modules

### 🛠️ Development

- [**Guidelines**](development/guidelines.md)  
  **How to implement** – Step‑by‑step workflows and examples for:
  - Adding strict types and improving type safety
  - Git & commit workflow (atomic commits, review flow, AI commit rules)
  - Using the quality pipeline (Pint, PHPCS, PHPMD, PHPStan)
  - Module creation and API development patterns
  - Frontend patterns (Vue/TypeScript/Inertia if used)

- [**Code Quality**](development/code-quality.md)  
  **Tools & configuration** – How to run and configure quality tooling:
  - Tool stack (Pint, PHPCS, PHPMD, PHPStan, PHPUnit)
  - Command shortcuts and recommended usage patterns
  - Non‑negotiable rules around violations and suppressions

### 📚 Reference

- [**Standards**](reference/standards.md)  
  **Quick lookup** – All concrete rules and exact requirements:
  - Type safety rules and PHPStan configuration
  - Coverage targets by layer
  - Naming conventions and module standards
  - API response envelope and logging standards
  - Git/commit message format, AI commit metadata, and pre‑commit checks

---

## 📖 How to Use This Documentation

### When You Need to Know...

| Question | Document | Purpose |
|----------|----------|---------|
| **"What must we do?"** | `architecture/principles.md` | Understand the engineering standards |
| **"Why do we do this?"** | `architecture/principles.md` | Learn the rationale behind requirements |
| **"How do I implement this?"** | `development/guidelines.md` | Get step‑by‑step procedures |
| **"What's the exact requirement?"** | `reference/standards.md` | Quick lookup for numbers/configs |
| **"How does a request flow?"** | `architecture/flow.md` | See layer responsibilities & examples |

### Quick Start Workflow

1. **New to the project?** → Read `architecture/principles.md` to understand what we do and why.  
2. **Need to implement something?** → Use `development/guidelines.md` for step‑by‑step instructions.  
3. **Need specific numbers?** → Check `reference/standards.md` for quick lookup.  
4. **Understanding the architecture?** → Review `architecture/flow.md` for request/response and module patterns.

### Documentation Hierarchy

```
🎯 Principle (What & Why)
├─ 📋 Guidelines (How – Approach / Workflow)
└─ ⚙️ Standards (How – Exact / Numbers / Config)
```

**Example:**
- **Principle:** Type Safety – All code must be type‑safe with no implicit coercion.  
- **Guideline:** Use strict types, explicit declarations, readonly dependencies.  
- **Standard:** `declare(strict_types=1);` in **all** PHP files, PHPStan at your chosen max level, no unsuppressed errors.

---

## 📖 Specialized Documentation

### 🤖 AI Development (Optional)

- [**AI Workflow**](ai-workflow.md) – **START HERE for AI agents.** Multi‑agent pipeline, commit rules, and plan update protocol.  
- [**AI Infrastructure Setup**](ai-infrastructure-setup.md) – Example local/hybrid AI dev setup (optional; adapt or ignore as needed).

### 📖 Guides

Step‑by‑step tutorials and how‑to documentation (enable what you need per project):

- [**Writing Plans**](guides/writing-plans.md) – Creating implementation plans with DoD, risks, and metrics.  
- [**RESTful API Design**](guides/restful-api-design.md) – RESTful API design, UUIDs, status codes, pagination, filtering.  
- [**Laravel Components & Patterns**](guides/laravel-components-patterns.md) – All Laravel components (FormRequest, Resource, Service, Repository, Job, Event, Command, etc.).  
- [**Testing Patterns**](guides/testing-patterns.md) – Unit/feature testing strategies and patterns.  
- [**Security Best Practices**](guides/security-best-practices.md) – Validation, CSRF, rate limiting, credential management.  
- [**Performance Optimization**](guides/performance-optimization.md) – N+1 prevention, caching, async processing.  
- [**Module Creation**](guides/module-creation.md) – How to create and structure modules (core vs domain).  
- [**Frontend Patterns**](guides/frontend-patterns.md) – Vue 3 + TS + Inertia + Bootstrap dark theme (optional; only if your stack uses it).  
- [**Directory Structure**](guides/directory-structure.md) – How the documentation is organized.

> **Note:** Modules and Inertia.js are optional, but the documentation assumes a **modular architecture** and provides guidance for it. Use or adapt depending on your project.

### 📋 Planning & Implementation

#### Planning Resources

- [**Writing Plans Guide**](guides/writing-plans.md) – How to write implementation plans.

In each project, keep your own plans under `docs/plans/` in the parent repo:

- `plans/features/` – Feature/product plans.  
- `plans/technical/` – Technical/refactor/infrastructure plans.

### 📝 Decisions

Architecture Decision Records (ADRs) documenting major architectural choices.  
Keep project‑specific ADRs under `docs/decisions/` in the parent repo.

### 🔍 Retrospectives

Post‑mortems and lessons learned from production issues.  
Keep project‑specific retrospectives under `docs/retrospectives/` in the parent repo.

---

## 💡 Common Scenarios

### "I need to add a new feature"

1. **Understand requirements** → `architecture/principles.md` for engineering standards.  
2. **Follow workflow** → `development/guidelines.md` for step‑by‑step implementation.  
3. **Check exact rules** → `reference/standards.md` for coverage targets, naming conventions.  
4. **Understand data flow** → `architecture/flow.md` for Controller → Service → Repository/SDK patterns and module boundaries.

### "I'm getting quality pipeline errors"

1. **Run tools in order** → `reference/standards.md` for tool execution sequence.  
2. **Fix common issues** → `development/guidelines.md` for fixing quality pipeline errors.  
3. **Understand tool config** → `development/code-quality.md` for detailed tooling setup.

### "I need to create a new module (or domain)"

1. **Decide module placement** → `architecture/principles.md` modular architecture section (core vs domain).  
2. **Follow creation steps** → `guides/module-creation.md` for module creation workflow.  
3. **Check naming rules** → `reference/standards.md` (module and component naming).  
4. **Wire services & repositories** → `architecture/flow.md` and `guides/laravel-components-patterns.md`.

### "I'm writing tests"

1. **Understand coverage requirements** → `reference/standards.md` for coverage targets by layer.  
2. **Follow test patterns** → `guides/testing-patterns.md` for unit and feature test examples.  
3. **Learn testing principles** → `architecture/principles.md` comprehensive testing section.

### "I need to integrate with external services"  

1. **Follow integration patterns** → `architecture/principles.md` third‑party integration section.  
2. **Use SDK contracts** → `architecture/flow.md` for SDK patterns (no raw HTTP in services).  
3. **Check exact requirements** → `reference/standards.md` (integration and security standards).

---

## 🏗️ Architecture Overview

The platform follows these core patterns:

### Request Flow

```text
HTTP Request → Middleware → FormRequest → Controller → Service → Repository/SDK → Database/External API
```

### Module Organization (Recommended)

```text
Core (Technical Infrastructure)
├─ Logging / audit helpers
├─ API response helpers
└─ Shared base services / support

Domain Modules (Business Logic)
├─ {DomainModule1} (e.g. Billing)
├─ {DomainModule2} (e.g. Catalog)
└─ {DomainModule3} (e.g. Accounts)
```

> **Modules Optional, But Recommended:**  
> Even if you don’t use a modules package, follow the same principle: keep shared infrastructure in a “core” area and separate business domains into clearly bounded areas with their own services/controllers/repos.

### Quality Pipeline

```text
Pint → PHPCS → PHPMD → PHPStan
```

### Testing Strategy

```text
Unit Tests (many) → Integration Tests (fewer) → E2E Tests (minimal)
```

---

## ⚡ Quick Commands

### Development

```bash
composer install
npm install
php artisan serve          # Laravel dev server
npm run dev                # Frontend dev build (if applicable)
```

### Quality Tools

```bash
composer lint              # Run quality pipeline (Pint, PHPCS, PHPMD, PHPStan)
composer test              # Run tests
composer test:coverage-check  # Enforce coverage thresholds
npm run typecheck          # TypeScript validation (if applicable)
npm run build              # Production build
```

### Module Management (If Using a Modules Package)

```bash
php artisan module:make Blog   # Create a new domain module (example)
php artisan migrate            # Run migrations
```

---

## 📋 Conventions

- **Architecture** – Long‑lived system design documents (principles, flow).  
- **Development** – Workflow and tooling documentation (guidelines, code quality).  
- **Reference** – Quick lookup specifications (standards, APIs, naming).  
- **Guides** – Step‑by‑step tutorials (REST APIs, modules, testing, security, performance, frontend).  
- **Features / Technical (in parent project)** – Implementation plans for user‑facing features and technical work.  
- **Decisions (in parent project)** – ADRs documenting architectural choices.  
- **Retrospectives (in parent project)** – Post‑mortems and lessons learned.

**Documentation Quality Standards:**

- All examples should be realistic and runnable (or clearly marked as pseudo‑code).  
- Include both positive and negative examples (✅/❌) where helpful.  
- Explain the “why” behind requirements, not just the “what”.  
- Provide quick reference sections and link related documents instead of duplicating rules.  

---

Copyright (c) 2025 Viet Vu  
Company: JOOservices Ltd  
Licensed under the MIT License.
