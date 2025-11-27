# Pull Request Review & Merge Policy

Complete guide to PR review process, approval requirements, merge standards, and quality gates.

> **Related Documentation:**
> - [PR Workflow](../development/guidelines.md#pull-request-pr-workflow) - PR creation process
> - [Commit Standards](../reference/standards.md#git-standards) - Commit message requirements
> - [Quality Pipeline](../development/code-quality.md) - Quality gates and tooling
> - [AI Workflow](../ai-workflow.md) - AI agent responsibilities

---

## Overview

Pull Requests (PRs) are the **primary quality gate** before code reaches the main codebase. This policy ensures all PRs meet quality standards, follow architectural patterns, and maintain codebase consistency.

**Key Principles:**
- **One feature = one PR** (based on plan file)
- **All quality gates must pass** before merge
- **Minimum 1 approval** required for merge
- **PR size limits** to ensure reviewability
- **Clear rejection criteria** for non-compliant PRs

---

## PR Review Requirements

### Reviewer Assignment

**Required Reviewers:**
- ✅ **MUST:** At least **1 approved review** before merge
- ✅ **SHOULD:** Technical lead reviews architecture changes
- ✅ **SHOULD:** Domain expert reviews business logic changes
- ✅ **MAY:** Multiple reviewers for complex or high-risk changes

**Reviewer Responsibilities:**
- ✅ Verify compliance with all documented standards
- ✅ Check architectural patterns and layer boundaries
- ✅ Validate test coverage and quality
- ✅ Ensure plan file synchronization
- ✅ Approve or request changes with clear feedback

### Approval Rules

**What Counts as Approval:**
- ✅ GitHub/GitLab "Approve" status
- ✅ "LGTM" (Looks Good To Me) comment with explicit approval
- ✅ Explicit approval from designated reviewer

**What Does NOT Count as Approval:**
- ❌ Comments without explicit approval
- ❌ "Looks good" without approval status
- ❌ Self-approvals (author cannot approve own PR)

**Required Approvals by Change Type:**

| Change Type | Minimum Approvals | Required Reviewer Type |
|------------|------------------|----------------------|
| **Feature (new functionality)** | 1 | Technical Lead or Senior Developer |
| **Bug Fix** | 1 | Any team member |
| **Architecture Change** | 2 | Technical Lead + Domain Expert |
| **Security Fix** | 2 | Security Lead + Technical Lead |
| **Breaking Change** | 2 | Technical Lead + Product Owner |
| **Documentation Only** | 0 (auto-merge) | N/A |

---

## PR Merge Requirements

### Pre-Merge Quality Gates

**ALL of the following MUST pass before merge:**

1. ✅ **Quality Pipeline Passes**
   ```bash
   composer lint              # All tools pass (Pint, PHPCS, PHPMD, PHPStan)
   composer test:coverage-check # Coverage ≥ 80%
   npm run typecheck          # TypeScript validation
   npm run build              # Production build succeeds
   ```

2. ✅ **At Least 1 Approval**
   - PR must have approved review status
   - Cannot merge without explicit approval

3. ✅ **No Conflicts**
   - PR must be up-to-date with base branch (`develop`)
   - No merge conflicts with base branch

4. ✅ **CI/CD Checks Pass**
   - All automated checks must be green
   - No failing tests or lint errors

5. ✅ **Plan File Synchronized**
   - All completed tasks marked in plan file
   - Plan status reflects actual implementation state

### Merge Restrictions

**❌ FORBIDDEN to merge if:**
- ❌ Any quality tool fails (lint, tests, coverage, typecheck)
- ❌ No approvals from reviewers
- ❌ Merge conflicts with base branch
- ❌ CI/CD pipeline is failing
- ❌ Plan file not updated or out of sync
- ❌ Breaking changes without proper documentation
- ❌ Security vulnerabilities detected
- ❌ Code decreases overall test coverage below 80%

---

## PR Size Limits

### File Count Guidelines

| File Count | Status | Action Required |
|-----------|--------|----------------|
| **1-15 files** | ✅ Acceptable | Standard review process |
| **16-30 files** | ⚠️ Large | Require justification in PR description |
| **31-50 files** | ⚠️ Very Large | Consider splitting into smaller PRs |
| **>50 files** | ❌ Too Large | **REQUIRED to split** into multiple PRs |

### Lines of Code Guidelines

| LOC Changed | Status | Action Required |
|------------|--------|----------------|
| **<500 lines** | ✅ Ideal | Standard review process |
| **500-1000 lines** | ⚠️ Large | Require detailed PR description |
| **1000-2000 lines** | ⚠️ Very Large | Require architectural review |
| **>2000 lines** | ❌ Too Large | **REQUIRED to split** into smaller PRs |

### PR Size Decision Rule

**"Can a reviewer understand all changes in one review session?"**
- ✅ **Yes** → Acceptable size
- ❌ **No** → Split into smaller PRs

**When to Split PRs:**
- ✅ Feature spans multiple unrelated modules
- ✅ PR contains both implementation and refactoring
- ✅ PR mixes new features with bug fixes
- ✅ PR contains unrelated documentation changes

---

## PR Review Checklist

### Architecture & Patterns

- [ ] **Layer Boundaries:** Code respects Controller → Service → Repository/SDK flow
- [ ] **No Business Logic in Wrong Layer:** Controllers thin, Repositories data-only
- [ ] **SOLID Principles:** Classes follow Single Responsibility, Dependency Inversion
- [ ] **Module Structure:** Changes follow modular architecture (Core vs Domain modules)

### Code Quality

- [ ] **Type Safety:** All files have `declare(strict_types=1);`
- [ ] **Final Classes:** Classes use `final` keyword (unless justified)
- [ ] **Readonly Dependencies:** Constructor-injected dependencies are `readonly`
- [ ] **No `mixed` Types:** All types explicitly declared (no `mixed` except third-party)
- [ ] **PHPDoc Complete:** All public methods have proper PHPDoc annotations

### Testing & Coverage

- [ ] **Tests Present:** All new code has corresponding tests
- [ ] **Coverage Maintained:** Overall coverage ≥ 80%, layer-specific targets met
- [ ] **FormRequests 100%:** All FormRequests have 100% coverage
- [ ] **Tests Pass:** All tests pass locally and in CI/CD
- [ ] **Mocking Correct:** External dependencies properly mocked

### Standards Compliance

- [ ] **Naming Conventions:** All components follow naming patterns
- [ ] **API Standards:** RESTful API endpoints follow all documented standards
- [ ] **Error Handling:** Proper exception handling and error responses
- [ ] **Logging:** Appropriate audit logging for domain mutations
- [ ] **Security:** No hardcoded secrets, proper input validation

### Documentation

- [ ] **Plan File Updated:** All completed tasks marked in plan file
- [ ] **PR Description Complete:** PR description references plan file
- [ ] **Code Comments:** Complex logic explained with comments
- [ ] **API Documentation:** API changes documented (if applicable)

---

## PR Rejection Criteria

### Automatic Rejection

**PRs are automatically rejected if ANY of the following:**

1. ❌ **Quality Pipeline Fails**
   - Linting errors (Pint, PHPCS, PHPMD, PHPStan)
   - Test failures
   - Coverage below 80%
   - TypeScript errors
   - Build failures

2. ❌ **Standards Violations**
   - Missing `declare(strict_types=1);` in PHP files
   - Using `mixed` type without justification
   - Missing required metadata in commit messages (AI-generated)
   - Violating architectural patterns (business logic in wrong layer)

3. ❌ **Security Issues**
   - Hardcoded secrets or credentials
   - SQL injection vulnerabilities
   - Missing input validation
   - Direct external API calls from frontend

4. ❌ **Test Coverage Decrease**
   - PR decreases overall coverage below 80%
   - New code without tests
   - FormRequests without 100% coverage

5. ❌ **Merge Conflicts**
   - Cannot merge due to conflicts (must rebase/resolve first)

### Rejection Process

**When rejecting a PR:**

1. **Provide Clear Feedback**
   - List all violations with specific file locations
   - Reference relevant documentation sections
   - Explain why changes are needed

2. **Request Changes**
   - Use GitHub/GitLab "Request Changes" status
   - Provide actionable feedback
   - Link to relevant standards/guides

3. **Allow Time for Fixes**
   - Author can address feedback and update PR
   - Re-review after changes are pushed

**Example Rejection Feedback:**
```markdown
## ❌ Changes Requested

### Quality Pipeline Failures
- PHPStan errors in `app/Services/ProductService.php:42`
- Test coverage decreased from 82% to 79% (below 80% threshold)

### Standards Violations
- Missing `declare(strict_types=1);` in `app/Models/Category.php`
- Business logic in Controller (`app/Http/Controllers/ProductController.php:56`)

### Required Actions
1. Fix PHPStan errors (see CI output)
2. Add tests to restore coverage to ≥80%
3. Add `declare(strict_types=1);` to Category.php
4. Move business logic from Controller to Service layer

See: [Architecture Flow Guide](../architecture/flow.md#4-service---business-logic)
```

---

## Conflict Resolution

### Handling Merge Conflicts

**When conflicts occur:**

1. **Rebase on Latest Base Branch**
   ```bash
   git checkout feature/your-branch
   git fetch origin
   git rebase origin/develop
   ```

2. **Resolve Conflicts**
   - Review conflicted files
   - Resolve conflicts following project standards
   - Ensure no business logic is lost

3. **Verify After Resolution**
   ```bash
   composer lint              # Ensure no new errors
   composer test             # Ensure tests still pass
   ```

4. **Force Push (if needed)**
   ```bash
   git push --force-with-lease origin feature/your-branch
   ```

**Conflict Resolution Rules:**
- ✅ **MUST:** Keep your feature changes intact
- ✅ **MUST:** Respect others' changes from base branch
- ✅ **MUST:** Run quality checks after resolving conflicts
- ❌ **FORBIDDEN:** Force pushing without rebasing first
- ❌ **FORBIDDEN:** Resolving conflicts without understanding changes

---

## PR Description Template

### Required PR Description Format

```markdown
## Summary
[Brief description of feature - 2-3 sentences]

## Plan Reference
- **Plan File:** `docs/plans/features/[plan-name].md`
- **Plan Status:** [Active/Review/Completed]
- **Tasks Completed:** [X/Y tasks from plan]

## Changes
- [ ] Task 1: [Description]
- [ ] Task 2: [Description]
- [ ] Task 3: [Description]

## Testing
- [ ] All tests pass (`composer test`)
- [ ] Coverage maintained (≥80%)
- [ ] Quality gates passed (`composer lint`)
- [ ] TypeScript validation passed (`npm run typecheck`)
- [ ] Production build succeeds (`npm run build`)

## Architecture Changes
[If applicable - describe architectural changes, module additions, etc.]

## Breaking Changes
[If applicable - list any breaking changes and migration steps]

## Checklist
- [ ] Plan file updated with completion status
- [ ] All tasks from plan completed
- [ ] Documentation updated (if needed)
- [ ] API documentation updated (if applicable)
- [ ] No merge conflicts with base branch

## Related Issues
[Link to related issues, if any]
```

---

## Special Cases

### Hotfix PRs

**Hotfix Definition:** Critical bug fix that must be deployed immediately

**Hotfix PR Process:**
1. Create branch from `main` (not `develop`)
2. Fix critical issue
3. Merge to both `main` and `develop`
4. Fast-track review (may skip some checks with approval)

**Hotfix Requirements:**
- ✅ Still requires 1 approval
- ✅ Must pass quality gates
- ✅ Must update plan file if related to planned work

### Documentation-Only PRs

**Definition:** PRs that only change documentation files (`.md`, comments)

**Documentation PR Process:**
- ✅ Can be auto-merged without approval (optional)
- ✅ Still must pass lint checks (if applicable)
- ✅ Must follow documentation standards

### Refactoring PRs

**Definition:** Code changes without functional changes

**Refactoring PR Requirements:**
- ✅ Must maintain all tests passing
- ✅ Must not decrease test coverage
- ✅ Must improve code quality (justify refactoring)
- ✅ Requires approval (standard review process)

---

## Merge Strategies

### Standard Merge (Recommended)

**Use merge commit for feature branches:**
- ✅ Preserves branch history
- ✅ Clear merge point in history
- ✅ Easier to revert if needed

**GitHub/GitLab Settings:**
- Merge method: "Create a merge commit"
- Delete branch after merge: ✅ Yes

### Squash Merge (Optional)

**Use squash merge for cleanup:**
- ✅ Cleaner main branch history
- ✅ All commits squashed into one
- ⚠️ Loses individual commit history

**When to Use:**
- Small PRs with multiple fix commits
- PRs with commit message cleanup needed

---

## Post-Merge Requirements

### After PR is Merged

1. **Verify Merge**
   - Check that PR merged successfully
   - Verify all changes in base branch

2. **Update Local Repository**
   ```bash
   git checkout develop
   git pull origin develop
   ```

3. **Clean Up**
   - Delete merged branch locally (if auto-delete not enabled)
   - Update plan file status if needed

4. **Monitor CI/CD**
   - Verify post-merge checks pass
   - Check deployment status (if automated)

---

## Enforcement

### Automated Enforcement

**CI/CD Pipeline:**
- ✅ Blocks merge if quality gates fail
- ✅ Blocks merge if no approvals
- ✅ Blocks merge if conflicts exist
- ✅ Validates commit message format
- ✅ Checks test coverage thresholds

### Manual Enforcement

**Reviewers:**
- ✅ Must check all checklist items
- ✅ Must reject PRs that violate standards
- ✅ Must provide clear feedback on rejections

**Team Leads:**
- ✅ Monitor PR quality trends
- ✅ Address repeated violations
- ✅ Update policies based on patterns

---

## Summary Checklist

### For PR Authors

- [ ] PR based on latest `develop` branch
- [ ] One feature = one PR (based on plan)
- [ ] All quality gates pass locally
- [ ] Plan file updated with completion status
- [ ] PR description follows template
- [ ] No merge conflicts
- [ ] Commit messages follow standards

### For Reviewers

- [ ] Review all checklist items
- [ ] Verify architecture compliance
- [ ] Check code quality and standards
- [ ] Validate test coverage
- [ ] Approve or request changes with feedback
- [ ] Ensure plan file is synchronized

### For Mergers

- [ ] All quality gates passed
- [ ] At least 1 approval received
- [ ] No merge conflicts
- [ ] CI/CD checks green
- [ ] PR description complete
- [ ] Plan file updated

---

> **Related Documentation:**
> - [PR Workflow](../development/guidelines.md#pull-request-pr-workflow) - PR creation process
> - [Commit Standards](../reference/standards.md#git-standards) - Commit message requirements
> - [Quality Pipeline](../development/code-quality.md) - Quality gates and tooling
> - [AI Workflow](../ai-workflow.md) - AI agent responsibilities

