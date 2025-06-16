# Git Workflow & Branching Conventions for Teams

## Overview

This guide establishes consistent Git practices for our team. Following these conventions ensures clean code history, easier collaboration, and smoother deployments. Whether you're an intern or experienced developer, these practices will help maintain code quality and team productivity.

## General Git Usage Principles

### Core Rules

**Commit Early and Often**
- Make small, focused commits that represent a single logical change
- Don't wait until the end of the day to commit your work
- Better to have many small commits than one large commit

**Never Commit Directly to Protected Branches**
- Never push commits directly to `main`, `staging`, or `production`
- Always work on feature branches and use pull requests
- This ensures code review and maintains branch stability

**Always Use Pull Requests (PRs)**
- All code changes must go through pull request review
- PRs provide opportunity for code review, discussion, and automated testing
- Merge only after approval from at least one team member

## Branch Naming Conventions

### With Jira Integration

When using Jira for project management, include the ticket ID in your branch name:

```
feature/JIRA-123-implement-user-authentication
bugfix/JIRA-456-fix-password-validation
hotfix/JIRA-789-fix-production-login-error
```

### Without Jira

For teams not using Jira, use descriptive names that clearly indicate the purpose:

```
feature/user-authentication
feature/payment-integration
bugfix/fix-signup-validation
bugfix/resolve-memory-leak
hotfix/fix-production-crash
```

### Branch Type Prefixes

- `feature/` - New features or enhancements
- `bugfix/` - Bug fixes for non-production issues
- `hotfix/` - Critical fixes for production issues
- `chore/` - Maintenance tasks, dependency updates
- `docs/` - Documentation-only changes

### Long-lived Branches

Our workflow uses three main long-lived branches:

- `production` - Production-ready code, always stable
- `staging` - Pre-production testing environment
- `dev` - Development integration branch

## Commit Message Conventions

### With Jira Integration

Start commit messages with the Jira ticket ID:

```
JIRA-123: Implement user authentication with OAuth
JIRA-456: Fix password validation regex
JIRA-789: Add error handling for API timeouts
```

### Without Jira (Conventional Commits)

Use the format: `type(scope): description`

```
feat(auth): add OAuth integration for user login
fix(validation): correct password regex pattern
chore(deps): update React to version 18.2
refactor(api): simplify user service methods
docs(readme): add installation instructions
test(auth): add unit tests for login flow
build(docker): optimize container image size
```

### Commit Types

- `feat` - New feature
- `fix` - Bug fix
- `chore` - Maintenance tasks
- `refactor` - Code refactoring without feature changes
- `docs` - Documentation changes
- `test` - Adding or updating tests
- `build` - Build system or dependency changes
- `perf` - Performance improvements
- `style` - Code style changes (formatting, etc.)

## Branch Merging Rules

### Merge Flow Hierarchy

Our branching strategy follows a strict hierarchy to ensure code stability:

```
feature/bugfix/hotfix branches → dev → staging → production
```

### Specific Rules

1. **Production Branch**
   - Only accepts PRs from `staging`
   - Represents code currently running in production
   - Must always be stable and deployable

2. **Staging Branch**
   - Only accepts PRs from `dev`
   - Used for pre-production testing and QA
   - Should mirror production environment

3. **Dev Branch**
   - Accepts PRs from `feature/*`, `bugfix/*`, `hotfix/*`
   - Integration branch for ongoing development
   - May be less stable than staging/production

4. **Feature/Bugfix/Hotfix Branches**
   - Created from `dev` (or `production` for critical hotfixes)
   - Merged back to `dev` via pull request
   - Deleted after successful merge

### Critical Rule
**Never merge feature branches directly into `staging` or `production`**. All changes must flow through the proper hierarchy.

## Examples

### Good vs Bad Branch Names

**✅ Good Branch Names:**
```
feature/JIRA-234-user-profile-page
bugfix/JIRA-567-fix-cart-calculation
hotfix/JIRA-890-fix-payment-gateway
feature/shopping-cart-ui
bugfix/fix-mobile-layout
```

**❌ Bad Branch Names:**
```
my-feature
john-branch
test
fix
branch1
temp-branch
```

### Good vs Bad Commit Messages

**✅ Good Commit Messages:**
```
JIRA-123: Add user authentication with JWT tokens
feat(cart): implement add to cart functionality
fix(validation): resolve email format validation issue
chore(deps): update lodash to version 4.17.21
docs(api): add endpoint documentation for user service
```

**❌ Bad Commit Messages:**
```
fixed stuff
update
changes
wip
asdf
minor tweaks
```

### Proper vs Improper Merge Scenarios

**✅ Proper Merge Flow:**
```
1. Create feature/JIRA-123-login-page from dev
2. Work on feature, make commits
3. Create PR: feature/JIRA-123-login-page → dev
4. After approval and merge, create PR: dev → staging
5. After staging testing, create PR: staging → production
```

**❌ Improper Merge Flow:**
```
1. Create feature/login-page from dev
2. Work on feature, make commits
3. Create PR: feature/login-page → production (WRONG!)
4. Or: Create PR: feature/login-page → staging (ALSO WRONG!)
```

**✅ Hotfix Exception:**
```
1. Create hotfix/JIRA-999-critical-fix from production
2. Fix critical issue, commit changes
3. Create PR: hotfix/JIRA-999-critical-fix → production
4. Also merge hotfix branch back to dev and staging to keep them in sync
```

## Daily Workflow Summary

1. **Starting New Work:**
   - Pull latest changes from `dev`
   - Create feature branch: `git checkout -b feature/JIRA-123-feature-name`

2. **During Development:**
   - Make small, focused commits with clear messages
   - Push branch regularly: `git push origin feature/JIRA-123-feature-name`

3. **Ready for Review:**
   - Create pull request from your feature branch to `dev`
   - Request review from team members
   - Address feedback and make necessary changes

4. **After Merge:**
   - Delete feature branch locally and remotely
   - Pull latest `dev` changes before starting next feature

## Best Practices

- **Rebase before creating PR** to keep commit history clean
- **Test your changes** before creating pull request
- **Keep PRs small** and focused on single feature/fix
- **Write descriptive PR descriptions** explaining what and why
- **Respond promptly** to code review feedback
- **Don't force push** to shared branches
- **Use draft PRs** for work-in-progress that needs early feedback

Following these conventions will help maintain a clean, organized codebase and smooth collaboration across the team. When in doubt, ask a senior team member for guidance!
