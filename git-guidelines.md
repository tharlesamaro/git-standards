# Git Guidelines for Projects

This document serves as a reference guide for Git standards and practices in versionable projects. Following these guidelines ensures consistency, traceability, and facilitates collaboration among team members.

## Branch Structure

### Main Branches

- **`main`**: Stable production code
- **`develop`**: Main development branch
- **`staging`**: Staging branch for testing before production

### Temporary Branches

- **`feature/{task-number}-{description}`**: For new feature development
- **`bugfix/{task-number}-{description}`**: For bug fixes in the development environment
- **`hotfix/{task-number}-{description}`**: For urgent fixes directly to production
- **`refactor/{task-number}-{description}`**: For code refactoring
- **`docs/{description}`**: For documentation updates

## Commit Standards

We follow the [Conventional Commits](https://www.conventionalcommits.org/) standard for commit messages, which includes:

```
<type>: <description>

[optional body]

[optional footer]
```

### Commit Types

- **`feat`**: New feature
- **`fix`**: Bug fix
- **`docs`**: Documentation changes
- **`style`**: Code formatting, semicolons, etc; no logic changes
- **`refactor`**: Code refactoring
- **`test`**: Adding or fixing tests
- **`chore`**: Build updates, dependencies, etc.
- **`WIP`**: Work in progress (should be temporary)

### Commit Examples

```
feat: implement biometric authentication
fix: correct average calculation in performance report
docs: update README with installation instructions
style: standardize indentation in Controllers
refactor: optimize queries in CartRepository
test: add tests for payment module
chore: update composer dependencies
WIP: initial implementation of student dashboard
```

## Workflow

### Starting a New Feature

```bash
# Update develop branch
git checkout develop
git pull origin develop

# Create new feature branch
git checkout -b feature/123-feature-description

# Work on the feature...

# Frequent and meaningful commits
git add modified-file.php
git commit -m "feat: clear description of the change"
```

### Finishing a Feature

```bash
# Ensure your branch is updated with develop
git checkout develop
git pull origin develop
git checkout feature/123-feature-description
git rebase develop

# Resolve conflicts if necessary
# Clean up WIP commits if they exist
git rebase -i HEAD~n  # n = number of commits in the branch

# Push to remote repository
git push origin feature/123-feature-description

# Create Merge/Pull Request via GitLab/GitHub interface
```

### Bug Fixes

```bash
git checkout develop
git pull origin develop
git checkout -b bugfix/456-bug-description

# Implement fix
git commit -m "fix: description of the fix"

# Push and follow a similar flow to features
```

### Hotfixes (production fixes)

```bash
git checkout main
git pull origin main
git checkout -b hotfix/789-problem-description

# Implement fix
git commit -m "fix: description of the urgent fix"

# After approval and merge to main
git checkout main
git pull origin main
git tag -a v1.0.1 -m "Hotfix: short description"
git push origin v1.0.1

# Propagate fix to develop
git checkout develop
git pull origin develop
git merge main
git push origin develop
```

## Using WIP (Work in Progress)

The term "WIP" should be used in specific situations:

- **When to use:**
  - End of day with incomplete work that needs to be saved
  - Temporary context/task switching
  - Code synchronization between machines
  - During pair programming

- **Best practices:**
  - Avoid keeping WIP commits for too long
  - Never merge branches with WIP commits
  - Squash WIP commits before merge request
  - Use a clear prefix: `WIP: description of work in progress`

- **Alternatives to WIP:**
  - `git stash` for temporary changes
  - Temporary branches for experiments

## Tags and Releases

We use Semantic Versioning for tags:

```
v{MAJOR}.{MINOR}.{PATCH}
```

- **MAJOR**: Incompatible changes with previous versions
- **MINOR**: Compatible feature additions
- **PATCH**: Compatible bug fixes

Examples: `v1.0.0`, `v1.1.0`, `v1.1.1`

```bash
# Create tag
git tag -a v1.1.0 -m "Release: description of main changes"

# Publish tag
git push origin v1.1.0
```

## Code Reviews

- All changes must pass code review before being integrated
- Minimum requirements:
  - Code follows style standards (PSR-12)
  - Automated tests have been added/updated
  - Documentation has been updated when necessary
  - No WIP commits

## General Best Practices

1. **Frequent and atomic commits:**
   - Each commit should represent a single logical change
   - Facilitates review and possible rollbacks

2. **Clear commit messages:**
   - Use imperative: "Add feature" instead of "Added feature"
   - First line with maximum 72 characters

3. **Keep branches updated:**
   - Frequent rebase with the main development branch

4. **Protect the main branch:**
   - No direct pushes, only via approved merge/pull requests

5. **Recommended tools:**
   - Git hooks for style checking and tests
   - Laravel Pint for code standardization
   - PHPStan for static analysis

## Practical Examples

### Example 1: Developing a New Feature

```bash
# Start development
git checkout develop
git pull origin develop
git checkout -b feature/127-implement-student-module

# Incremental development
git add app/Models/Student.php
git commit -m "feat: create Student model with basic relations"

git add app/Http/Requests/StudentRequest.php
git commit -m "feat: add validations for student registration"

# End of day, incomplete work
git add app/Http/Controllers/StudentController.php
git commit -m "WIP: initial implementation of StudentController"

# Continuing the next day
git add app/Http/Controllers/StudentController.php app/Services/StudentService.php
git commit -m "feat: finalize StudentController implementation with Service"

# Sending for review
git rebase -i HEAD~4  # To clean up the WIP commit
git push origin feature/127-implement-student-module
# Create Merge Request via interface
```

### Example 2: Urgent Hotfix

```bash
# Create hotfix branch
git checkout main
git pull origin main
git checkout -b hotfix/165-fix-login-error-production

# Fix the problem
git add app/Http/Controllers/Auth/LoginController.php
git commit -m "fix: correct credential validation in login"

# Test
git add tests/Feature/Auth/LoginTest.php
git commit -m "test: ensure correct login functionality with invalid credentials"

# Send for review and merge
git push origin hotfix/165-fix-login-error-production

# After approval, create release
git checkout main
git pull origin main
git tag -a v1.0.1 -m "Hotfix: Login error correction"
git push origin v1.0.1

# Propagate to develop
git checkout develop
git pull origin develop
git merge main
git push origin develop
```

## Frequently Asked Questions

**Q: Can I commit directly to the main/develop branch?**  
A: No. All changes must be made in specific branches and integrated via Merge/Pull Requests after review.

**Q: When should I create a tag?**  
A: Tags should be created for each release that goes to production, including hotfixes.

**Q: How to handle conflicts during rebase?**  
A: Resolve conflicts maintaining a clean history. In case of doubt, consult the author of the conflicting code.

**Q: Is squashing commits necessary before merging?**  
A: We recommend squashing related commits to maintain a clean and meaningful history, especially for WIP commits.

---

This document is subject to updates as team practices evolve. Improvement suggestions are welcome via branch `docs/improve-git-guidelines`.

Last update: March 2025
