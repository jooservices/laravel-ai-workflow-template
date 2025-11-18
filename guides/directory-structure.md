# Documentation Directory Structure Guide

## Overview

This guide explains the purpose and organization of each directory within the documentation root, following the **Principle → Guideline → Standard** hierarchy.

> **When used as a Git submodule:**  
> In a consuming Laravel project, this repository is typically mounted under `<project-root>/docs/`. In that setup, the root of this repo **is** the `docs/` folder for your project.

---

## 📐 `/docs/architecture/` - Long-Lived Design Decisions

**Purpose:** Documents that define **What & Why** - engineering principles and architectural patterns.

**Contents:**
- `principles.md` - 24 engineering principles with embedded guidelines and standards
- `flow.md` - Request/response flow patterns, service layer architecture

**When to Use:**
- Understanding engineering standards and rationale
- Learning why decisions were made
- Reference for architectural patterns

**Characteristics:**
- Long-lived (rarely change)
- High-level (What & Why)
- Foundation for all other docs

---

## 🛠️ `/docs/development/` - Workflow & Tooling

**Purpose:** Documents that explain **How to Implement** - step-by-step procedures and tooling.

**Contents:**
- `guidelines.md` - Step-by-step implementation workflows and examples
- `code-quality.md` - Quality pipeline configuration and tooling setup

**When to Use:**
- Need step-by-step implementation instructions
- Understanding quality pipeline tools
- Learning implementation patterns

**Characteristics:**
- Practical (How-to)
- Examples and workflows
- Tool-specific configurations

---

## 📚 `/docs/reference/` - Quick Lookup

**Purpose:** Documents for **Quick Reference** - exact requirements, numbers, and configurations.

**Contents:**
- `standards.md` - All concrete rules, coverage targets, tool configs, exact requirements

**When to Use:**
- Need exact numbers (coverage targets, tool versions)
- Quick lookup for specific rules
- Finding exact format requirements

**Characteristics:**
- Quick lookup format
- Exact specifications
- Numbers and configs
- Single source of truth for rules

---

## 📖 `/docs/guides/` - Step-by-Step Tutorials

**Purpose:** Feature-specific tutorials with complete code examples.

**Contents:**
- `restful-api-design.md` - RESTful API design patterns
- `laravel-components-patterns.md` - Laravel components and naming
- `writing-plans.md` - How to write technical implementation plans
- `directory-structure.md` - This guide

**When to Use:**
- Learning how to use specific SDKs
- Following complete working examples
- Understanding best practices for specific features

**Characteristics:**
- Tutorial format
- Complete code examples
- Feature-specific
- Best practices and patterns

**Note:** Guides show **HOW** to build, while Plans define **WHAT** to build.

---

## 📋 `/docs/plans/` - Implementation Plans (Template Structure Only)

> **Important:** The `docs/plans/` folder in the submodule is a **template structure only** (examples).  
> Your **actual project plans** must be located in `doc/plans/` (local, outside submodule).

**Purpose:** Template structure showing how to organize project planning documents.

**Structure:**
```
plans/
├── technical/      # Technical implementation plans (template examples)
└── features/       # Feature and product plans (template examples)
```

### `/docs/plans/technical/` - Technical Plans (Template)

**Purpose:** Template examples for implementation-focused plans:
- Code refactoring and improvements
- Infrastructure changes
- Developer tooling
- Quality improvements
- Technical debt resolution

**File Naming:** `YYYY-MM-DD-{plan-name}.md`

**Note:** These are template examples. Your actual technical plans go in `doc/plans/technical/` (local).

### `/docs/plans/features/` - Feature Plans (Template)

**Purpose:** Template examples for product-focused plans:
- New user features
- UI/UX improvements
- Business logic changes
- Integration features
- User-facing functionality

**File Naming:** `YYYY-MM-DD-{plan-name}.md`

**Note:** These are template examples. Your actual feature plans go in `doc/plans/features/` (local).

**Plan Structure (applies to both template and actual plans):**
- Status, Owner, Created/Updated dates
- Summary and Objectives
- Tasks with Definition of Done
- Dependencies and Risks

---

## 📝 `/docs/decisions/` - Architecture Decision Records (ADRs) - Template/Examples

> **Note:** The `docs/decisions/` folder in the submodule contains template/examples.  
> Your **actual project ADRs** go in `doc/decisions/` (local, outside submodule).

**Purpose:** Template examples showing how to document key technical decisions and their rationale.

**Contents (in submodule):**
- Example ADRs showing format and structure
- `README.md` - ADR format and guidelines

**When to Use:**
- Understanding why architectural choices were made
- Documenting major technical decisions
- Recording alternatives considered

**ADR Format:**
- Status (Proposed | Accepted | Deprecated | Superseded)
- Context (problem statement)
- Decision (what was decided)
- Consequences (tradeoffs)
- Alternatives Considered

**File Naming:** `YYYY-MM-DD-decision-title.md`

**Note:** Your actual project ADRs go in `doc/decisions/` (local).

---

## 🔍 `/docs/retrospectives/` - Post-Mortems & Lessons Learned - Template/Examples

> **Note:** The `docs/retrospectives/` folder in the submodule contains general lessons/templates.  
> Your **actual project-specific retrospectives** go in `doc/retrospectives/` (local, outside submodule).

**Purpose:** Template examples and general lessons learned from past issues.

**Contents (in submodule):**
- Example retrospectives showing format and structure
- `README.md` - Retrospective template and guidelines
- General lessons applicable to any project

**When to Use:**
- Learning from past issues (both global and local)
- Understanding failure modes
- Preventing similar problems

**Retrospective Format:**
- Date, Severity, Impact
- What Happened
- Root Cause
- Fix
- Lessons Learned
- Action Items

**Note:** 
- Global retrospectives (`docs/retrospectives/`) = General lessons/templates
- Local retrospectives (`doc/retrospectives/`) = Project-specific issues

---

## 📚 `/docs/stories/` - User Stories & Demos

**Purpose:** User stories, demos, and narrative documentation.

**Contents:**
- `lmstudio-streaming-demo.md` - Demo scenarios and user stories

**When to Use:**
- Understanding user-facing scenarios
- Demo documentation
- Narrative documentation

---

## 📄 Root Level Documentation Files

### `/docs/README.md` - Documentation Index

**Purpose:** Main entry point and navigation for all documentation.

**Contents:**
- Documentation hierarchy explanation
- Quick start workflows
- Common scenarios and navigation
- Links to all major docs

**When to Use:**
- First time reading docs
- Finding the right document
- Understanding documentation structure

---

### `/docs/ai-workflow.md` - AI Development Workflow

**Purpose:** ⭐ **START HERE** for all AI agents - multi-agent pipeline workflow.

**Contents:**
- Mandatory reading order
- Multi-agent pipeline (5 stages)
- Quality gates
- Commit authorization rules
- Plan file update protocol

**When to Use:**
- Starting work as AI agent
- Understanding AI workflow
- Learning commit authorization

**Critical:** All AI agents MUST read this first.

---

### `/docs/ai-infrastructure-setup.md` - AI Infrastructure

**Purpose:** Personal hardware/tools configuration for AI development.

**When to Use:**
- Setting up development environment
- Configuring AI tools

---

### `/docs/ai-models.md` - AI Model Selection

**Purpose:** AI model selection and role assignments.

**When to Use:**
- Understanding which AI model to use
- Role assignments

---

## Documentation Hierarchy Flow

```
📐 Architecture (What & Why)
    ↓
🛠️ Development (How - Approach)
    ↓
📚 Reference (How - Exact)
    ↓
📖 Guides (Tutorials)
```

**Example Flow:**
1. **Principles** → "We need type safety" (What & Why)
2. **Guidelines** → "How to add strict types" (How - Approach)
3. **Standards** → "`declare(strict_types=1);` in ALL files" (How - Exact)
4. **Guides** → "Complete example with all parameters" (Tutorial)

---

## Decision Rules: Where to Put New Documentation?

### 1. Is it a long-lived design decision?
- **Yes** → `/docs/architecture/`
- **No** → Continue

### 2. Is it a step-by-step implementation guide?
- **Yes** → `/docs/development/`
- **No** → Continue

### 3. Is it exact requirements/numbers/configs?
- **Yes** → `/docs/reference/`
- **No** → Continue

### 4. Is it a feature-specific tutorial with examples?
- **Yes** → `/docs/guides/`
- **No** → Continue

### 5. Is it an implementation plan?
- **Yes** → `doc/plans/technical/` or `doc/plans/features/` (local, not in submodule)
- **Note:** `docs/plans/` in submodule is template only. Actual plans go in `doc/plans/` (local).
- **No** → Continue

### 6. Is it an architectural decision record?
- **Yes** → `doc/decisions/` (local, not in submodule)
- **Note:** `docs/decisions/` in submodule is template only. Actual ADRs go in `doc/decisions/` (local).
- **No** → Continue

### 7. Is it a post-mortem or lesson learned?
- **Yes** → `doc/retrospectives/` (local, not in submodule)
- **Note:** `docs/retrospectives/` in submodule contains general lessons. Project-specific retrospectives go in `doc/retrospectives/` (local).
- **No** → Continue

### 8. Is it a user story or demo?
- **Yes** → `/docs/stories/`
- **No** → Root level or create new category

---

## Quick Reference Table

| Directory | Purpose | Question Answered | When to Use |
|-----------|---------|-------------------|-------------|
| `architecture/` | What & Why | "What must we do?" / "Why?" | Understanding principles |
| `development/` | How to Implement | "How do I implement?" | Step-by-step workflows |
| `reference/` | Quick Lookup | "What's the exact requirement?" | Exact numbers/rules |
| `guides/` | Tutorials | "How do I use X?" | Feature-specific examples |
| `doc/plans/technical/` | Technical Plans | "What technical work is planned?" | Implementation planning (local) |
| `doc/plans/features/` | Feature Plans | "What features are planned?" | Product planning (local) |
| `docs/plans/` | Template Structure | "How to structure plans?" | Template examples only (global) |
| `doc/decisions/` | ADRs | "Why did we choose X?" | Project decisions (local) |
| `docs/decisions/` | ADR Templates | "How to write ADRs?" | Template examples only (global) |
| `doc/retrospectives/` | Lessons Learned | "What went wrong?" | Project-specific issues (local) |
| `docs/retrospectives/` | General Lessons | "General lessons?" | General lessons/templates (global) |
| `stories/` | User Stories | "How does user interact?" | Demo scenarios |

---

## Related Documentation

- [Documentation README](../README.md) - Main documentation index
- [AI Workflow](../ai-workflow.md) - Multi-agent development workflow
- [Writing Plans Guide](writing-plans.md) - How to write plans
- [Architecture Principles](../architecture/principles.md) - Engineering principles


---

**Copyright (c) 2025 Viet Vu <jooservices@gmail.com>**
**Company: JOOservices Ltd**  
Licensed under the MIT License.
