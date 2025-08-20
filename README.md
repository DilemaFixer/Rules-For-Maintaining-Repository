# Git Workflow and Versioning

Complete guide to Git workflow organization, branching strategies, and project versioning.

## 📋 Table of Contents

- [Branch Structure](#branch-structure)
- [Versioning](#versioning)
- [Pull Request Workflow](#pull-request-workflow)
- [Command Examples](#command-examples)
- [Best Practices](#best-practices)

## 🌳 Branch Structure

### Main Branches

#### `main` / `master`
- **Purpose**: Stable version ready for production release
- **Characteristics**: 
  - Always in working state
  - Only verified code
  - Protected from direct commits

#### `develop`
- **Purpose**: Main development branch
- **Characteristics**:
  - Integration of all new features
  - May be unstable
  - Optional for small projects

### Working Branches

#### `feature/feature-name`
- **Created from**: `develop` or `main`
- **Purpose**: Development of new features
- **Lifecycle**: Create → Develop → Pull Request → Merge → Delete

```bash
# Creation
git checkout develop
git pull origin develop
git checkout -b feature/user-authentication

# After completion
git checkout develop
git merge feature/user-authentication
git branch -d feature/user-authentication
```

#### `bugfix/bug-description`
- **Created from**: `develop` or `main`
- **Purpose**: Regular bug fixes
- **Naming examples**: 
  - `bugfix/login-validation`
  - `bugfix/memory-leak-fix`

#### `hotfix/critical-bug`
- **Created from**: Directly from `main`
- **Purpose**: Critical production fixes
- **Characteristics**: Merged directly into `main` and `develop`

```bash
# Creating hotfix
git checkout main
git pull origin main
git checkout -b hotfix/security-vulnerability

# After fix
git checkout main
git merge hotfix/security-vulnerability
git checkout develop
git merge hotfix/security-vulnerability
```

#### `release/x.y.z`
- **Created from**: `develop`
- **Purpose**: Release preparation
- **Content**: Only bug fixes and minor adjustments
- **Completion**: Merge to `main` + create tag

## 🏷️ Versioning

### Semantic Versioning (SemVer)

Format: `MAJOR.MINOR.PATCH`

#### MAJOR (X.0.0)
- **When to increment**: Breaking API changes
- **Examples**:
  - Removing public methods
  - Changing API response structure
  - Modifying existing function behavior

#### MINOR (0.X.0)
- **When to increment**: New features without breaking compatibility
- **Examples**:
  - Adding new API methods
  - New optional parameters
  - Performance improvements

#### PATCH (0.0.X)
- **When to increment**: Bug fixes and minor changes
- **Examples**:
  - Error corrections
  - Documentation updates
  - Refactoring without API changes

### Versioning Examples

```
1.0.0   → First stable release
1.0.1   → Fixed authentication bug
1.1.0   → Added notification system
1.1.1   → Fixed notification error
1.2.0   → Added PDF export
2.0.0   → New API, incompatible with v1
2.0.1   → Fixed migration scripts
```

### Pre-release Versions

```
1.0.0-alpha.1    → Alpha version
1.0.0-beta.1     → Beta version  
1.0.0-rc.1       → Release Candidate
```

## 🔄 Pull Request Workflow

### Creating PR

1. **Title**: Clearly reflects the essence of changes
   ```
   ✅ Good: "Add OAuth2 authentication"
   ❌ Bad: "Fixes", "Updates"
   ```

2. **Description**: Includes context and details
   ```markdown
   ## Description
   Implemented OAuth2 authentication system
   
   ## Changes
   - Added OAuth2 provider
   - Updated login page
   - Added tests
   
   ## Testing
   - [ ] Verified Google authentication
   - [ ] Verified GitHub authentication
   ```

3. **One PR = One Task**: Don't mix different changes

### Code Review Checklist

#### For PR Author:
- [ ] Code is covered by tests
- [ ] Documentation updated
- [ ] Functionality verified
- [ ] Debug comments removed
- [ ] Coding style followed

#### For Reviewer:
- [ ] Logic is clear and correct
- [ ] No code duplication
- [ ] Security principles followed
- [ ] Performance not degraded
- [ ] Tests cover main scenarios

## 💻 Command Examples

### Git Flow Setup

```bash
# Initialize Git Flow
git flow init

# Or manual setup
git checkout -b develop main
git push -u origin develop
```

### Working with Features

```bash
# Start new feature
git flow feature start user-profile

# Or manually
git checkout develop
git pull origin develop
git checkout -b feature/user-profile

# Finish feature
git flow feature finish user-profile

# Or manually
git checkout develop
git merge feature/user-profile
git branch -d feature/user-profile
git push origin develop
```

### Creating Releases

```bash
# Start release
git flow release start 1.2.0

# Manually
git checkout develop
git pull origin develop
git checkout -b release/1.2.0

# Finish release
git flow release finish 1.2.0

# Manually
git checkout main
git merge release/1.2.0
git tag -a v1.2.0 -m "Release version 1.2.0"
git checkout develop
git merge release/1.2.0
git push origin main develop --tags
```

### Working with Tags

```bash
# Create annotated tag
git tag -a v1.2.0 -m "Release version 1.2.0"

# Push tags to server
git push origin --tags

# View all tags
git tag -l

# Create signed tag
git tag -s v1.2.0 -m "Signed release 1.2.0"
```

## ✨ Best Practices

### Branch Naming

```bash
# Use descriptive names
feature/add-payment-gateway
bugfix/fix-memory-leak
hotfix/security-patch-cve-2023-1234
release/2.1.0

# Avoid
feature/stuff
bugfix/fix
hotfix/urgent
```

### Commit Messages

Use [Conventional Commits](https://www.conventionalcommits.org/):

```bash
feat: add OAuth2 authentication
fix: resolve memory leak in cache
docs: update README with API examples
style: fix code formatting
refactor: rewrite authentication module
test: add tests for payment gateway
chore: update dependencies
```

### Branch Protection

Configure in repository settings:
- Protect `main` branch
- Require code review
- Require CI/CD test passing
- Require branch to be up to date

### Automation

```yaml
# .github/workflows/release.yml
name: Create Release
on:
  push:
    tags:
      - 'v*'
jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Create Release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ github.ref }}
```

### Useful Git Aliases

```bash
# Add to ~/.gitconfig
[alias]
    co = checkout
    br = branch
    ci = commit
    st = status
    unstage = reset HEAD --
    last = log -1 HEAD
    visual = !gitk
    tree = log --oneline --decorate --graph --all
    amend = commit --amend --no-edit
```

### Git Hooks Examples

```bash
# pre-commit hook example
#!/bin/sh
# Run tests before commit
npm test
if [ $? -ne 0 ]; then
  echo "Tests must pass before commit!"
  exit 1
fi

# Check code style
npm run lint
if [ $? -ne 0 ]; then
  echo "Please fix linting errors before commit!"
  exit 1
fi
```

### Release Process

1. **Feature Freeze**: Stop adding new features
2. **Testing Phase**: Thorough testing on `release/x.y.z`
3. **Bug Fixes**: Only critical fixes in release branch
4. **Final Review**: Code review and approval
5. **Merge to Main**: Create tag and deploy
6. **Hotfixes**: If issues found, use `hotfix/` branches

### Merge Strategies

#### Merge Commit
```bash
git checkout main
git merge feature/new-feature
# Creates merge commit preserving branch history
```

#### Squash and Merge
```bash
git checkout main
git merge --squash feature/new-feature
git commit -m "Add new feature"
# Combines all feature commits into single commit
```

#### Rebase and Merge
```bash
git checkout feature/new-feature
git rebase main
git checkout main
git merge feature/new-feature
# Linear history without merge commits
```

### Conflict Resolution

```bash
# When conflicts occur during merge
git status  # See conflicted files
# Edit files to resolve conflicts
git add resolved-file.js
git commit -m "Resolve merge conflicts"

# Abort merge if needed
git merge --abort

# For rebase conflicts
git rebase --continue  # After resolving
git rebase --abort     # To cancel rebase
```

### Working with Remote Branches

```bash
# Fetch all remote branches
git fetch --all

# Track remote branch
git checkout -b feature/remote-feature origin/feature/remote-feature

# Push new branch to remote
git push -u origin feature/new-feature

# Delete remote branch
git push origin --delete feature/old-feature

# Prune deleted remote branches
git remote prune origin
```

## 📊 Workflow Comparison

| Feature | Git Flow | GitHub Flow | GitLab Flow |
|---------|----------|-------------|-------------|
| Complexity | High | Low | Medium |
| Release Branches | Yes | No | Optional |
| Hotfixes | Dedicated branch | From main | From main |
| Best for | Large teams, scheduled releases | Continuous deployment | Mixed environments |

## 🔍 Troubleshooting

### Common Issues

**Large files in history:**
```bash
# Use Git LFS for large files
git lfs track "*.pdf"
git add .gitattributes
git add large-file.pdf
git commit -m "Add large file with LFS"
```

**Accidentally committed to wrong branch:**
```bash
# Move commits to correct branch
git log --oneline -n 5  # Find commit hash
git reset --hard HEAD~1  # Remove from current branch
git checkout correct-branch
git cherry-pick commit-hash
```

**Revert a merge:**
```bash
# Revert merge commit
git revert -m 1 merge-commit-hash
```

## 📚 Additional Resources

- [Git Flow Cheatsheet](https://danielkummer.github.io/git-flow-cheatsheet/)
- [Semantic Versioning](https://semver.org/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [Atlassian Git Tutorials](https://www.atlassian.com/git/tutorials)
- [Pro Git Book](https://git-scm.com/book)

## 🎯 Quick Reference

### Daily Commands
```bash
git status                    # Check current state
git pull origin develop       # Update local branch
git checkout -b feature/xyz   # Create new feature
git add .                     # Stage changes
git commit -m "message"       # Commit changes
git push origin feature/xyz   # Push to remote
```

### Emergency Commands
```bash
git stash                     # Save work temporarily
git stash pop                 # Restore stashed work
git reset --hard HEAD~1       # Undo last commit (dangerous)
git reflog                    # Recovery command history
git clean -fd                 # Remove untracked files
```

---

**Remember**: The key to successful Git workflow is consistency and clarity for the entire team. Choose the appropriate model and stick to it!
