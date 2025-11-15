# Laravel Documentation Template

Complete documentation template for Laravel projects following the **Principle → Guideline → Rule/Standard** hierarchy.

> **Note:** This is a template. Replace `{ProjectName}` with your actual project name throughout the documentation.

---

## 🎯 Core Documentation (Start Here)

### 📐 Architecture
- [**Principles**](architecture/principles.md) - **What & Why** - Engineering principles with embedded guidelines and standards
- [**Flow**](architecture/flow.md) - Request/response flow, service layer pattern, repository usage

### 🛠️ Development  
- [**Guidelines**](development/guidelines.md) - **How to Implement** - Step-by-step workflows and examples
- [**Code Quality**](development/code-quality.md) - Quality pipeline configuration and tooling

### 📚 Reference
- [**Standards**](reference/standards.md) - **Quick Lookup** - All concrete rules, coverage targets, tool configs

---

## 📖 How to Use This Documentation

### When You Need to Know...

| Question | Document | Purpose |
|----------|----------|---------|
| **"What must we do?"** | [Principles](architecture/principles.md) | Understand the engineering standards |
| **"Why do we do this?"** | [Principles](architecture/principles.md) | Learn the rationale behind requirements |
| **"How do I implement this?"** | [Guidelines](development/guidelines.md) | Get step-by-step procedures |
| **"What's the exact requirement?"** | [Standards](reference/standards.md) | Quick lookup for numbers/configs |

### Quick Start Workflow

1. **New to the project?** → Read [Principles](architecture/principles.md) to understand what we do and why
2. **Need to implement something?** → Use [Guidelines](development/guidelines.md) for step-by-step instructions  
3. **Need specific numbers?** → Check [Standards](reference/standards.md) for quick lookup
4. **Understanding the architecture?** → Review [Flow](architecture/flow.md) for request/response patterns

### Documentation Hierarchy

```
🎯 Principle (What & Why)
├─ 📋 Guidelines (How - Approach)  
└─ ⚙️ Rules/Standards (How - Exact)
```

**Example:**
- **Principle:** Type Safety - All code must be type-safe with no implicit coercion
- **Guideline:** Use strict types, explicit declarations, readonly dependencies
- **Standard:** `declare(strict_types=1);` in ALL PHP files, PHPStan level: max

---

## 📖 Specialized Documentation

### 🤖 AI Development

- [**AI Workflow**](ai-workflow.md) - ⭐ **START HERE** for all AI agents (multi-agent pipeline)
- [**AI Infrastructure Setup**](ai-infrastructure-setup.md) - Personal hardware/tools configuration

### 📖 Guides
Step-by-step tutorials and how-to documentation.

- [**Writing Plans**](guides/writing-plans.md) - Creating implementation plans
- [**RESTful API Design**](guides/restful-api-design.md) - Complete RESTful API design guide
- [**Laravel Components & Patterns**](guides/laravel-components-patterns.md) - All Laravel components guide

> **Note:** This template includes guides for `nwidart/laravel-modules` and `Inertia.js`. These are optional - use them if your project uses these packages, or skip them if not.

### 📋 Planning & Implementation

#### Planning Resources
- [**Writing Plans Guide**](guides/writing-plans.md) - How to write implementation plans

> **Note:** Create your own `plans/` directory following the planning guide structure.

### 📝 Decisions
Architecture Decision Records (ADRs) documenting major architectural choices.

> **Note:** Document your project's architectural decisions in the `decisions/` directory.

### 🔍 Retrospectives
Post-mortems and lessons learned from production issues.

> **Note:** Document lessons learned and post-mortems in the `retrospectives/` directory.

---

## 💡 Common Scenarios

### "I need to add a new feature"
1. **Understand requirements** → [Principles](architecture/principles.md) for engineering standards
2. **Follow workflow** → [Guidelines](development/guidelines.md) for step-by-step implementation
3. **Check exact rules** → [Standards](reference/standards.md) for coverage targets, naming conventions
4. **Understand data flow** → [Flow](architecture/flow.md) for Controller → Service → Repository pattern

### "I'm getting quality pipeline errors"
1. **Run tools in order** → [Standards](reference/standards.md) for tool execution sequence
2. **Fix common issues** → [Guidelines](development/guidelines.md) for fixing quality pipeline errors
3. **Understand tool config** → [Code Quality](development/code-quality.md) for detailed tooling setup

### "I need to create a new module"
1. **Decide module placement** → [Principles](architecture/principles.md) modular architecture section
2. **Follow creation steps** → [Guidelines](development/guidelines.md) module creation workflow
3. **Check naming rules** → [Standards](reference/standards.md) module standards

### "I'm writing tests"
1. **Understand coverage requirements** → [Standards](reference/standards.md) for coverage targets by layer
2. **Follow test patterns** → [Guidelines](development/guidelines.md) for unit and feature test examples
3. **Learn testing principles** → [Principles](architecture/principles.md) comprehensive testing section

### "I need to integrate with external services"  
1. **Follow integration patterns** → [Principles](architecture/principles.md) third-party integration section
2. **Use SDK contracts** → [Architecture Flow](architecture/flow.md) for SDK patterns
3. **Check exact requirements** → [Standards](reference/standards.md) integration standards

---

## 🏗️ Architecture Overview

The platform follows these core patterns:

### Request Flow
```
HTTP Request → Controller → Service → Repository/SDK → Database/External API
```

### Module Organization
```
Core Module (Technical Infrastructure)
├─ ActionLogger (generic audit)
├─ ApiResponse (generic responses)
└─ BaseService (generic base class)

Domain Modules (Business Logic)
├─ {DomainModule1} ({BusinessDomain1} business)
├─ {DomainModule2} ({BusinessDomain2} business)  
└─ {DomainModule3} ({BusinessDomain3} business)
```

### Quality Pipeline
```
Pint → PHPCS → PHPMD → PHPStan
```

### Testing Strategy
```
Unit Tests (many) → Integration Tests (fewer) → E2E Tests (minimal)
```

---

## ⚡ Quick Commands

### Development
```bash
composer dev                 # Start full dev stack
composer lint                # Run quality pipeline  
composer test:coverage-check # Test with coverage enforcement
npm run typecheck            # TypeScript validation
```

### Quality Tools
```bash
composer lint:pint           # Auto-fix style
composer lint:phpcs          # PSR-12 validation
composer analyze:phpmd       # Design quality
composer analyze:phpstan     # Static analysis
```

### Module Management  
```bash
php artisan module:make WordPress  # Create module
php artisan migrate                # Run migrations
```

---

## 📋 Conventions

- **Architecture** - Long-lived system design documents (principles, flow)
- **Development** - Workflow and tooling documentation (guidelines, code quality)
- **Reference** - Quick lookup specifications (standards, APIs)
- **Guides** - Step-by-step tutorials (WordPress SDK, writing plans)
- **Features** - Implementation plans for user-facing features
- **Technical** - Infrastructure and improvement plans  
- **Decisions** - ADRs documenting architectural choices
- **Retrospectives** - Post-mortems and lessons learned

**Documentation Quality Standards:**
- All examples must be working code
- Include both positive and negative examples (✅/❌)
- Explain the "why" behind requirements
- Provide quick reference sections
- Link related documents

---

**Copyright (c) 2025 Viet Vu <jooservices@gmail.com>**  
**Company: JOOservices Ltd**  
All rights reserved.
