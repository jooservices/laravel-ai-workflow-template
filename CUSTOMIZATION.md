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

## Documentation Hierarchy: Global vs Local

This documentation system uses a **two-tier hierarchy**:

### 🌐 Global Documentation (This Submodule)
- **Location:** This repository mounted as a Git submodule (typically at `./docs/` in consuming projects)
- **Rule:** **MUST BE FOLLOWED** - This is the source of truth
- **Contents:** `ai-workflow.md`, `architecture/`, `development/`, `reference/`, `guides/`

### 📁 Local Documentation (Your Project)
- **Location:** `./doc/` (singular) in your Laravel project - **SEPARATE directory from submodule**
- **Rule:** **CAN OVERRIDE** global documentation (submodule) for project-specific needs
- **Contents:** `plans/`, `decisions/`, `retrospectives/`, and any project-specific guides
- **Note:** If local documentation doesn't exist, follow global documentation (submodule) only
- **Important:** Local docs are in a **different directory** (`./doc/`) than the submodule (`./docs/`). They are NOT in the same directory.
- **⚠️ CRITICAL:** To override global standards, create local documentation in `./doc/`. **NEVER modify files inside `./docs/` submodule** - it is read-only.

### 🔄 Precedence
- **Global = Base/Default** - All standards MUST be followed
- **Local = Override/Extension** - Can override global when needed
- **Conflict:** Local takes precedence for that specific project

## ⚠️ CRITICAL RULE: Submodule is Read-Only

**ABSOLUTE RULE - NO EXCEPTIONS:**
- ❌ **NEVER modify, edit, delete, or add files inside the `docs/` submodule directory**
- ❌ **The submodule is READ-ONLY - NO EXCEPTIONS**
- ✅ **ONLY way to override or customize:** Use local documentation in `./doc/` directory
- ✅ **ONLY way to add project-specific content:** Create files in `./doc/` (plans, decisions, retrospectives)

**Why:**
- Submodule updates will overwrite any local changes
- Submodule is shared across projects - modifications would affect all projects
- Local documentation (`./doc/`) is the correct place for project-specific overrides

**How to Override:**
- Create ADRs in `doc/decisions/` to override global standards
- Create project-specific plans in `doc/plans/`
- Create project-specific retrospectives in `doc/retrospectives/`
- See "Local Documentation Override Example" section below

## How to Use This Submodule in Your Project

1. **Add as Git Submodule**

   **Initial Setup:**
   ```bash
   # Navigate to your Laravel project root
   cd /path/to/your/laravel-project
   
   # Add the submodule (replace <repo-url> with actual repository URL)
   # Use 'master' branch for stable version (recommended)
   git submodule add -b master <repo-url> docs
   
   # Initialize and update submodule
   git submodule update --init --recursive
   ```

   **What This Does:**
   - Creates `docs/` directory containing the submodule
   - Creates `.gitmodules` file in your project root (tracks submodule configuration)
   - The `docs/` folder becomes your **global documentation (submodule)** (MUST FOLLOW)

   **Recommended Path:**
   - `<project-root>/docs/` (recommended) - This is where the submodule will be mounted
   - Treat the root of this submodule as your global documentation root

   **Important:** Local documentation goes in a **separate directory** (`./doc/`), NOT inside the submodule directory (`./docs/`).

   **⚠️ CRITICAL RULE - NO EXCEPTIONS:**
   - ❌ **NEVER modify files inside `docs/` submodule directory**
   - ❌ **NEVER edit, delete, or add files to the submodule**
   - ❌ **NO EXCEPTIONS - This is a read-only submodule**
   - ✅ **ONLY way to override:** Use local documentation in `./doc/` directory
   - ✅ **ONLY way to customize:** Create project-specific docs in `./doc/` that override global standards

2. **Decide What to Keep vs. Ignore**
   - Keep core docs that are generally useful for any Laravel project:
     - `architecture/`, `development/`, `reference/`, `guides/` (most of them)
   - Mark or ignore optional/stack‑specific docs in your parent project:
     - WordPress‑specific examples
     - LM Studio / AI‑specific workflow docs
     - Inertia.js / Vue 3 frontend patterns (if not using them)

3. **Create Your Own Project-Specific Docs (Local Documentation)**
   
   Create a `doc/` folder (singular) in your project root for local documentation:
   
   ```bash
   mkdir -p doc/plans/features doc/plans/technical doc/decisions doc/retrospectives
   ```
   
   **Local Documentation Structure:**
   - `doc/plans/features/` – Feature/product plans (actual project plans)
   - `doc/plans/technical/` – Technical/refactor/infrastructure plans
   - `doc/decisions/` – Project-specific Architecture Decision Records (ADRs)
   - `doc/retrospectives/` – Project-specific post-mortems and lessons learned
   
   > **Important:** 
   > - The `docs/plans/` folder in the submodule is a **template structure only** (examples)
   > - Your **actual project plans** go in `doc/plans/` (local, outside submodule)
   > - These local docs can override global standards when needed
   > - If `doc/` doesn't exist, follow global documentation only

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
├── docs/                      # 🌐 Global Documentation (Submodule)
│   ├── README.md              # From submodule
│   ├── CUSTOMIZATION.md       # From submodule
│   ├── ai-workflow.md         # From submodule (MUST FOLLOW)
│   ├── architecture/          # From submodule
│   ├── development/           # From submodule
│   ├── guides/                # From submodule
│   ├── reference/             # From submodule
│   └── plans/                 # Template structure only (examples)
└── doc/                       # 📁 Local Documentation (Project-Specific)
    ├── plans/                 # Actual project plans
    │   ├── features/          # Feature plans
    │   └── technical/         # Technical plans
    ├── decisions/             # Project-specific ADRs
    └── retrospectives/        # Project-specific post-mortems
```

### 📊 Visual Directory Structure

```
Project Root
│
├── docs/  ────────────────────────┐
│   │                              │
│   └── [Submodule Contents]      │  🌐 GLOBAL
│       • ai-workflow.md          │  (Submodule)
│       • architecture/          │  MUST FOLLOW
│       • development/            │
│       • reference/              │
│       • guides/                 │
│       • plans/ (template)       │
│                                 │
└── doc/  ────────────────────────┘
    │                              │
    └── [Project Contents]        │  📁 LOCAL
        • plans/ (actual)         │  (Project)
        • decisions/              │  CAN OVERRIDE
        • retrospectives/         │
```

**Key Points:**
- **Separate Directories:** `docs/` (global, submodule) and `doc/` (local, project) are **completely separate**
- **NOT in same directory:** Local documentation is NOT inside the submodule directory
- **Different purposes:** Global = reusable template, Local = project-specific

**Legend:**
- **Global Documentation (Submodule):** Reusable documentation that MUST be followed
- **Local Documentation (Project):** Project-specific documentation that can override global

**Important Notes:**
- `docs/plans/` in submodule = Template structure only (examples)
- `doc/plans/` in project = Actual project plans (local)
- If `doc/` doesn't exist, follow global documentation (submodule) only
- Local docs are in a **separate directory** (`./doc/`), NOT inside `./docs/` submodule

## Submodule Maintenance

### Updating the Submodule

When new versions of the documentation template are available:

```bash
# Navigate to your project root
cd /path/to/your/laravel-project

# Update submodule to latest version on master branch (stable)
git submodule update --remote docs

# If you need to switch to a specific branch
cd docs
git checkout master  # Use 'master' for stable, 'develop' for development (may be unstable)
cd ..

# Commit the submodule update
git add docs .gitmodules
git commit -m "docs: update documentation submodule to latest version"
```

### Submodule Branch Strategy

**Branch Selection:**
- ✅ **MUST:** Always use `master` branch for stable, production-ready documentation
- ⚠️ **ALLOWED:** Use `develop` branch for testing, but it may be unstable
- ❌ **FORBIDDEN:** Using feature branches or untagged commits

**Why:**
- `master` = Stable, tested, production-ready documentation
- `develop` = Under active development, may contain breaking changes or incomplete updates

**Specify Branch When Adding:**
```bash
# Stable version (recommended)
git submodule add -b master <repo-url> docs

# Development version (use with caution)
git submodule add -b develop <repo-url> docs
```

### Understanding .gitmodules

The `.gitmodules` file is automatically created when you add a submodule. It tracks:
- Submodule path (where it's mounted in your project)
- Submodule URL (repository location)
- Branch/tag (if specified)

**Example `.gitmodules` content:**
```ini
[submodule "docs"]
    path = docs
    url = https://github.com/your-org/laravel-ai-workflow-template.git
    branch = master
```

**Important:**
- Don't manually edit `.gitmodules` unless you know what you're doing
- The file is tracked in your main repository
- When cloning a project with submodules, use: `git clone --recursive <repo-url>`

### .gitignore Configuration

**Submodule Files:**
- ❌ **DO NOT** add `docs/` to `.gitignore` - The submodule directory must be tracked
- ✅ **DO** track `.gitmodules` file (it's automatically tracked)
- ✅ **DO** track the submodule reference in your main repository

**What Git Tracks:**
- `.gitmodules` file (submodule configuration)
- Submodule commit reference (which commit of the submodule you're using)
- Submodule directory itself (as a special gitlink)

**What Git Does NOT Track:**
- Individual files inside the submodule (those are tracked in the submodule's own repository)

**Correct `.gitignore` (if you have one):**
```gitignore
# Don't ignore submodule directory
# docs/  ← DO NOT add this

# Your local documentation can be ignored if you don't want to track it
# doc/  ← Optional: add this if you don't want to track local docs
```

**Recommendation:**
- Track both `docs/` (submodule) and `doc/` (local) in your main repository
- Only ignore `doc/` if you have a specific reason (e.g., generated docs)

## ⚠️ Submodule Modification is FORBIDDEN

**ABSOLUTE RULE - NO EXCEPTIONS:**
- ❌ **NEVER modify, edit, delete, or add files inside the `docs/` submodule directory**
- ❌ **The submodule is READ-ONLY - NO EXCEPTIONS**
- ❌ **Any modifications will be lost when submodule is updated**
- ❌ **Modifications would affect all projects using this submodule**

**Why This Rule Exists:**
- Submodule is shared across multiple projects
- Updates to submodule would overwrite any local changes
- Local modifications would be lost on every `git submodule update`
- Breaking this rule causes maintenance nightmares

**Correct Way to Override:**
- ✅ Create local documentation in `./doc/` directory
- ✅ Use ADRs in `doc/decisions/` to document overrides
- ✅ Create project-specific plans in `doc/plans/`
- ✅ Document all overrides clearly

## Local Documentation Override Example

Here's how to override a global standard with a local one (the ONLY correct way):

**Global Standard** (`docs/architecture/principles.md`):
```markdown
- ✅ **MUST:** PHP version **8.4+** (minimum)
```

**Local Override** (`doc/decisions/php-version.md`):
```markdown
# ADR: PHP Version Requirement

**Status:** Accepted  
**Date:** 2025-01-20

## Context
Our hosting environment currently supports PHP 8.3 maximum.

## Decision
We will use PHP 8.3 for this project, overriding the global 8.4+ requirement.

## Consequences
- Must ensure all code is compatible with PHP 8.3
- Cannot use PHP 8.4-specific features
- Will upgrade to 8.4+ when hosting supports it
```

**Result:** For this project, PHP 8.3 is acceptable (local override), but all other global standards still apply.

## Integration Checklist

In your main project, verify:

- [ ] Submodule is mounted under `docs/` path
- [ ] `.gitmodules` file exists and is tracked
- [ ] You know which guides are **optional** (WordPress, LM Studio, Inertia.js, etc.)
- [ ] Local `doc/` folder created (if needed)
- [ ] Project-specific plans are added under `doc/plans/` (not `docs/plans/`)
- [ ] Project-specific decisions are added under `doc/decisions/`
- [ ] Project-specific retrospectives are added under `doc/retrospectives/`

## Troubleshooting

### Issue: Submodule not updating
**Symptom:** `git submodule update --remote` doesn't pull latest changes

**Solution:**
```bash
# Navigate into submodule directory
cd docs
git fetch origin
git checkout master  # Always use 'master' for stable version
cd ..
git add docs
git commit -m "docs: update submodule"
```

### Issue: Local documentation conflicts with global
**Symptom:** Unclear which standard to follow

**Solution:**
1. **Verify with human** before taking any action
2. Check if local override is documented in `doc/decisions/`
3. If documented, follow local override
4. If not documented, follow global standard and ask human to create ADR

### Issue: Missing local documentation
**Symptom:** `doc/` folder doesn't exist

**Solution:**
- Follow global documentation only
- Create `doc/` folder when you need project-specific overrides
- Document any overrides in `doc/decisions/` as ADRs

### Issue: Submodule shows as modified after clone
**Symptom:** `git status` shows `docs/` as modified after cloning

**Solution:**
```bash
# Initialize and update submodules
git submodule update --init --recursive
```

### Issue: Documentation conflicts or unclear
**Symptom:** Conflicting information between global and local docs

**Solution:**
1. **MUST verify with human** before taking any action
2. Document the conflict in an issue or ADR
3. Wait for human resolution
4. Never assume or guess - always ask

### Issue: Accidentally modified submodule files
**Symptom:** You or someone else modified files inside `docs/` submodule directory

**Solution:**
1. **IMMEDIATELY discard all changes** to submodule:
   ```bash
   cd docs
   git checkout .
   git clean -fd
   cd ..
   ```
2. **Verify submodule is clean:**
   ```bash
   git status
   # Should NOT show docs/ as modified
   ```
3. **Create proper override** in local documentation (`./doc/`) instead
4. **Document the override** in `doc/decisions/` as an ADR
5. **Never do this again** - submodule is read-only (NO EXCEPTIONS)

## Support

For questions or issues with this template:
- **Author:** Viet Vu <jooservices@gmail.com>
- **Company:** JOOservices Ltd

---

**Copyright (c) 2025 Viet Vu <jooservices@gmail.com>**  
**Company: JOOservices Ltd**  
Licensed under the MIT License.

