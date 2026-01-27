# Complete Git Course Summary for DevOps Engineers

*From Basic Concepts to Advanced Topics - Interview Preparation Guide*

---

## Table of Contents

1. [Git Basics](#1-git-basics)
2. [Branching and Merging](#2-branching-and-merging)
3. [Remote Repositories](#3-remote-repositories)
4. [Git Workflows](#4-git-workflows)
5. [Advanced Git Concepts](#5-advanced-git-concepts)
6. [Git for DevOps](#6-git-for-devops)
7. [Common Mistakes & Interview Tips](#7-common-mistakes--interview-tips)
8. [Git Command Cheat Sheet](#8-git-command-cheat-sheet)

---

## 1. Git Basics

### What is Git and Why Use It?

**Git** is a distributed version control system (DVCS) that tracks changes in source code during software development.

**Why use Git?**
- Track code history and changes
- Collaborate with team members
- Revert to previous versions
- Work on features independently (branching)
- Maintain code integrity and backup
- Enable code reviews and quality control

### Git vs GitHub/GitLab

| Aspect | Git | GitHub/GitLab |
|--------|-----|---------------|
| Type | Version control software | Web-based hosting service |
| Location | Local computer | Cloud/Remote servers |
| Purpose | Track code changes | Store repositories + collaboration tools |
| Features | Commit, branch, merge | Pull requests, issues, CI/CD, wikis |
| Access | Command line/GUI | Web interface + CLI |

**Analogy:** Git is like Microsoft Word (software), GitHub/GitLab is like Google Docs (service).

### Installing Git

**Linux (Ubuntu/Debian):**
```bash
sudo apt update
sudo apt install git
```

**macOS:**
```bash
brew install git
```

**Windows:**
Download from [git-scm.com](https://git-scm.com)

**Verify installation:**
```bash
git --version
# Output: git version 2.x.x
```

### Initial Configuration

**Set your identity (required):**
```bash
# Set username globally
git config --global user.name "Your Name"

# Set email globally
git config --global user.email "your.email@example.com"
```

**Useful configurations:**
```bash
# Set default editor
git config --global core.editor "vim"

# Set default branch name to 'main'
git config --global init.defaultBranch main

# Enable color output
git config --global color.ui auto

# View all configurations
git config --list

# View specific config
git config user.name

# View config location
git config --list --show-origin
```

**Config levels:**
- `--system`: All users on the system (`/etc/gitconfig`)
- `--global`: Current user (`~/.gitconfig`)
- `--local`: Current repository (`.git/config`) - default

### Repository Initialization

#### Creating a New Repository

```bash
# Navigate to your project directory
cd my-project

# Initialize Git repository
git init

# Output: Initialized empty Git repository in /path/to/my-project/.git/
```

This creates a hidden `.git` directory containing all repository metadata.

#### Cloning an Existing Repository

```bash
# Clone a repository
git clone https://github.com/username/repository.git

# Clone to a specific directory
git clone https://github.com/username/repository.git my-folder

# Clone a specific branch
git clone -b develop https://github.com/username/repository.git
```

### Git File Lifecycle

Git has **three main areas**:

1. **Working Directory**: Your local files
2. **Staging Area (Index)**: Files ready to be committed
3. **Repository (.git directory)**: Committed snapshots

**File states:**
- **Untracked**: New files Git doesn't know about
- **Unmodified**: Tracked files with no changes
- **Modified**: Tracked files with changes
- **Staged**: Modified files marked for commit

```
┌──────────────┐     git add     ┌──────────────┐    git commit    ┌──────────────┐
│   Working    │ ──────────────> │   Staging    │ ──────────────>  │  Repository  │
│   Directory  │                 │     Area     │                  │   (.git)     │
└──────────────┘                 └──────────────┘                  └──────────────┘
      ▲                                                                    │
      │                            git checkout                            │
      └────────────────────────────────────────────────────────────────────┘
```

### Core Commands with Examples

#### git status

Shows the current state of the working directory and staging area.

```bash
git status

# Output example:
# On branch main
# Changes not staged for commit:
#   modified:   README.md
# Untracked files:
#   newfile.txt

# Short format
git status -s
# Output:
#  M README.md
# ?? newfile.txt
```

**Status codes:**
- `??` - Untracked
- `A` - Added to staging
- `M` - Modified
- `D` - Deleted

#### git add

Adds files to the staging area.

```bash
# Add a specific file
git add file.txt

# Add multiple files
git add file1.txt file2.txt

# Add all files in current directory
git add .

# Add all changes (including deletions)
git add -A

# Add all files with specific extension
git add *.js

# Interactive staging
git add -p
```

**Example workflow:**
```bash
# Create a new file
echo "Hello Git" > hello.txt

# Check status
git status
# Untracked files: hello.txt

# Stage the file
git add hello.txt

# Check status again
git status
# Changes to be committed: new file: hello.txt
```

#### git commit

Records staged changes to the repository.

```bash
# Commit with inline message
git commit -m "Add hello.txt file"

# Commit with detailed message (opens editor)
git commit

# Stage all tracked modified files and commit
git commit -am "Update existing files"

# Amend the last commit (change message or add files)
git commit --amend -m "Corrected commit message"

# Amend without changing message
git commit --amend --no-edit
```

**Good commit message format:**
```
Short summary (50 chars or less)

More detailed explanation if needed. Wrap at 72 characters.
Explain what and why, not how.

- Bullet points are okay
- Use imperative mood: "Add feature" not "Added feature"
```

**Example:**
```bash
# Make changes
echo "# My Project" > README.md

# Stage and commit
git add README.md
git commit -m "Add project README file"

# Output:
# [main 1a2b3c4] Add project README file
#  1 file changed, 1 insertion(+)
#  create mode 100644 README.md
```

#### git log

Shows commit history.

```bash
# Basic log
git log

# One line per commit
git log --oneline

# Show last n commits
git log -n 5

# Show commits with stats
git log --stat

# Show commits with diff
git log -p

# Show graph of branches
git log --graph --oneline --all

# Show commits by author
git log --author="John Doe"

# Show commits in date range
git log --since="2024-01-01" --until="2024-12-31"

# Show commits affecting a specific file
git log -- filename.txt

# Pretty format
git log --pretty=format:"%h - %an, %ar : %s"
```

**Example output:**
```bash
git log --oneline --graph
# * 3c4d5e6 (HEAD -> main) Update README
# * 2b3c4d5 Add login feature
# * 1a2b3c4 Initial commit
```

#### git diff

Shows differences between commits, branches, files, etc.

```bash
# Show unstaged changes
git diff

# Show staged changes
git diff --staged
# or
git diff --cached

# Show changes between commits
git diff commit1 commit2

# Show changes in a specific file
git diff filename.txt

# Show changes between branches
git diff main feature-branch

# Show summary of changes
git diff --stat

# Show word-level diff
git diff --word-diff
```

**Example:**
```bash
# Modify a file
echo "New line" >> README.md

# View changes
git diff README.md

# Output:
# diff --git a/README.md b/README.md
# index 1a2b3c4..5e6f7g8 100644
# --- a/README.md
# +++ b/README.md
# @@ -1 +1,2 @@
#  # My Project
# +New line
```

---

## 2. Branching and Merging

### Understanding Branches

Branches are lightweight pointers to commits. They allow parallel development without affecting the main codebase.

**HEAD**: A pointer to your current branch/commit.

### Creating and Switching Branches

#### git branch

```bash
# List all local branches
git branch

# List all branches (local + remote)
git branch -a

# Create a new branch
git branch feature-login

# Delete a branch (safe - prevents deletion if not merged)
git branch -d feature-login

# Force delete a branch
git branch -D feature-login

# Rename current branch
git branch -m new-branch-name

# Rename a specific branch
git branch -m old-name new-name

# Show last commit on each branch
git branch -v
```

#### git checkout (older method)

```bash
# Switch to an existing branch
git checkout main

# Create and switch to a new branch
git checkout -b feature-login

# Switch to a previous branch
git checkout -
```

#### git switch (newer, recommended)

```bash
# Switch to an existing branch
git switch main

# Create and switch to a new branch
git switch -c feature-login

# Switch to previous branch
git switch -
```

**Example workflow:**
```bash
# Create and switch to new branch
git switch -c feature-authentication

# Make changes
echo "Authentication module" > auth.js
git add auth.js
git commit -m "Add authentication module"

# Switch back to main
git switch main

# auth.js doesn't exist here - it's only in feature-authentication branch
```

### Merging Branches

#### git merge

Combines changes from different branches.

```bash
# Switch to the branch you want to merge INTO
git switch main

# Merge another branch into current branch
git merge feature-login
```

**Types of merges:**

**1. Fast-forward merge** (linear history):
```bash
# main: A---B
# feature:    C---D

git switch main
git merge feature

# Result: A---B---C---D (main, feature)
```

**2. Three-way merge** (creates merge commit):
```bash
# main: A---B---E
# feature:    C---D

git switch main
git merge feature

# Result: 
# A---B---E---M (main)
#      \     /
#       C---D (feature)
# M is a merge commit
```

**Merge options:**
```bash
# No fast-forward (always create merge commit)
git merge --no-ff feature-branch

# Fast-forward only (fail if not possible)
git merge --ff-only feature-branch

# Squash all commits into one
git merge --squash feature-branch
```

### Rebase vs Merge

#### git merge
- Creates a merge commit
- Preserves complete history
- Non-linear history (can be messy)

#### git rebase
- Rewrites commit history
- Linear, cleaner history
- Changes commit hashes (don't rebase public branches!)

**Merge example:**
```bash
# Current state:
# main:    A---B---E
# feature:      C---D

git switch main
git merge feature

# Result:
# A---B---E---M (main)
#      \     /
#       C---D (feature)
```

**Rebase example:**
```bash
# Current state:
# main:    A---B---E
# feature:      C---D

git switch feature
git rebase main

# Result:
# A---B---E---C'---D' (feature)
# 
# main:    A---B---E

# C and D are now C' and D' (new commits with same changes)
```

**When to use what:**
- **Merge**: For integrating feature branches, public branches, team collaboration
- **Rebase**: For cleaning up local commits before pushing, updating feature branch with main

**Golden rule:** Never rebase public/shared branches!

### Resolving Merge Conflicts

Conflicts occur when Git can't automatically merge changes.

**Step-by-step conflict resolution:**

**1. Attempt merge:**
```bash
git switch main
git merge feature-branch

# Output:
# Auto-merging file.txt
# CONFLICT (content): Merge conflict in file.txt
# Automatic merge failed; fix conflicts and then commit the result.
```

**2. Check conflict status:**
```bash
git status

# Output:
# You have unmerged paths.
# Unmerged paths:
#   both modified:   file.txt
```

**3. Open conflicted file:**
```bash
cat file.txt

# Content:
# <<<<<<< HEAD
# Changes from current branch (main)
# =======
# Changes from feature-branch
# >>>>>>> feature-branch
```

**4. Resolve conflict manually:**
```bash
# Edit file.txt - remove conflict markers and keep desired changes
# Option 1: Keep both changes
# Option 2: Keep only one
# Option 3: Write something new

# After resolution:
echo "Combined content from both branches" > file.txt
```

**5. Mark as resolved and commit:**
```bash
# Stage the resolved file
git add file.txt

# Check status
git status
# All conflicts fixed: run "git commit" to conclude merge

# Complete the merge
git commit -m "Merge feature-branch, resolve conflicts in file.txt"
```

**6. Abort merge if needed:**
```bash
# Discard merge and return to pre-merge state
git merge --abort
```

**Tools for conflict resolution:**
```bash
# Use visual merge tool
git mergetool

# View conflict in different ways
git diff --ours      # Your changes
git diff --theirs    # Their changes
git diff --base      # Common ancestor
```

---

## 3. Remote Repositories

### Understanding Remote Repositories

Remote repositories are versions of your project hosted on the internet or network. They enable collaboration.

### git remote

Manage remote repository connections.

```bash
# List remote repositories
git remote

# List with URLs
git remote -v

# Output:
# origin  https://github.com/user/repo.git (fetch)
# origin  https://github.com/user/repo.git (push)

# Add a remote
git remote add origin https://github.com/user/repo.git

# Add another remote (e.g., upstream)
git remote add upstream https://github.com/original/repo.git

# Remove a remote
git remote remove origin

# Rename a remote
git remote rename old-name new-name

# Change remote URL
git remote set-url origin https://github.com/user/new-repo.git

# Show detailed info about a remote
git remote show origin
```

### git fetch vs git pull

#### git fetch

Downloads commits, files, and refs from a remote repository without merging.

```bash
# Fetch from default remote (origin)
git fetch

# Fetch from specific remote
git fetch upstream

# Fetch a specific branch
git fetch origin main

# Fetch all remotes
git fetch --all

# Prune deleted remote branches
git fetch --prune
```

**What happens:**
- Downloads new data from remote
- Updates remote-tracking branches (e.g., `origin/main`)
- **Does NOT** modify your local working files
- Safe operation - you can review before merging

#### git pull

Fetch + merge in one command.

```bash
# Pull from current branch's tracking branch
git pull

# Equivalent to:
# git fetch + git merge origin/current-branch

# Pull with rebase instead of merge
git pull --rebase

# Pull from specific remote and branch
git pull origin main

# Pull all branches
git pull --all
```

**Difference:**
```bash
# fetch (safe, review first)
git fetch origin
git diff main origin/main  # Review changes
git merge origin/main      # Merge when ready

# pull (fetch + merge automatically)
git pull origin main
```

### git push

Upload local commits to a remote repository.

```bash
# Push current branch to remote
git push

# Push to specific remote and branch
git push origin main

# Push and set upstream (tracking)
git push -u origin feature-branch
# Now you can just use 'git push' from this branch

# Push all branches
git push --all

# Push tags
git push --tags

# Force push (dangerous! overwrites remote)
git push --force
# Safer alternative:
git push --force-with-lease

# Delete remote branch
git push origin --delete feature-branch
# or
git push origin :feature-branch
```

**Example workflow:**
```bash
# 1. Make local changes
git add .
git commit -m "Add new feature"

# 2. Update local repository with remote changes
git pull origin main

# 3. Push your changes
git push origin main
```

### Working with GitHub/GitLab

**Initial setup:**
```bash
# Create repository on GitHub/GitLab first, then:

# Option 1: Start with existing local repository
git remote add origin https://github.com/username/repo.git
git branch -M main
git push -u origin main

# Option 2: Clone existing remote repository
git clone https://github.com/username/repo.git
cd repo
# Start working...
```

**SSH vs HTTPS:**

**HTTPS:**
```bash
git clone https://github.com/username/repo.git
# Requires username/password or token
```

**SSH (recommended):**
```bash
git clone git@github.com:username/repo.git
# Requires SSH key setup (no password needed)
```

**Setup SSH key:**
```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

# Add to SSH agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Copy public key and add to GitHub/GitLab
cat ~/.ssh/id_ed25519.pub
```

### Pull Requests and Code Reviews

**Pull Request (PR) / Merge Request (MR)** is a request to merge changes from one branch to another.

**Typical workflow:**

**1. Create feature branch:**
```bash
git switch -c feature/user-authentication
```

**2. Make changes and commit:**
```bash
# Work on feature
git add .
git commit -m "Implement user authentication"
```

**3. Push to remote:**
```bash
git push -u origin feature/user-authentication
```

**4. Create Pull Request (on GitHub/GitLab):**
- Navigate to repository on GitHub/GitLab
- Click "New Pull Request" / "Create Merge Request"
- Select source branch (feature/user-authentication) → target branch (main)
- Add title and description
- Assign reviewers
- Submit

**5. Code review process:**
- Reviewers comment on code
- Request changes if needed
- You push additional commits to address feedback:
  ```bash
  git add .
  git commit -m "Address review comments"
  git push
  # PR updates automatically
  ```

**6. Approval and merge:**
- Reviewers approve
- Merge via GitHub/GitLab interface
- Options: Merge commit / Squash and merge / Rebase and merge

**7. Clean up:**
```bash
# Delete local branch
git branch -d feature/user-authentication

# Delete remote branch (if not auto-deleted)
git push origin --delete feature/user-authentication

# Update main branch
git switch main
git pull
```

**Example PR description template:**
```markdown
## Description
Implements user authentication using JWT tokens

## Changes
- Add authentication middleware
- Create login/logout endpoints
- Add user session management

## Testing
- Unit tests added for auth module
- Tested login flow manually

## Related Issues
Closes #123
```

---

## 4. Git Workflows

### Feature Branching Workflow

Simple workflow where each feature is developed in a dedicated branch.

**Process:**

```bash
# 1. Start from main
git switch main
git pull origin main

# 2. Create feature branch
git switch -c feature/shopping-cart

# 3. Work and commit
git add .
git commit -m "Add shopping cart functionality"

# 4. Push feature branch
git push -u origin feature/shopping-cart

# 5. Create Pull Request on GitHub/GitLab

# 6. After approval, merge to main (via PR)

# 7. Clean up
git switch main
git pull
git branch -d feature/shopping-cart
```

**Branch naming conventions:**
- `feature/description` - New features
- `bugfix/description` - Bug fixes
- `hotfix/description` - Urgent production fixes
- `release/version` - Release preparation

### Gitflow Workflow

A strict branching model for larger projects with scheduled releases.

**Branch types:**

1. **main** (or master) - Production-ready code
2. **develop** - Integration branch for features
3. **feature/** - New features (branch from develop)
4. **release/** - Release preparation (branch from develop)
5. **hotfix/** - Urgent fixes (branch from main)

**Workflow:**

**Starting a feature:**
```bash
# Branch from develop
git switch develop
git pull origin develop
git switch -c feature/user-profile

# Work on feature
git add .
git commit -m "Add user profile page"

# Finish feature - merge back to develop
git switch develop
git merge --no-ff feature/user-profile
git push origin develop
git branch -d feature/user-profile
```

**Creating a release:**
```bash
# Branch from develop
git switch develop
git switch -c release/1.2.0

# Make release preparations (update version, documentation)
git commit -m "Bump version to 1.2.0"

# Merge to main (production)
git switch main
git merge --no-ff release/1.2.0
git tag -a v1.2.0 -m "Release version 1.2.0"
git push origin main --tags

# Merge back to develop
git switch develop
git merge --no-ff release/1.2.0
git push origin develop

# Delete release branch
git branch -d release/1.2.0
```

**Hotfix:**
```bash
# Branch from main
git switch main
git switch -c hotfix/critical-bug

# Fix the bug
git commit -m "Fix critical security vulnerability"

# Merge to main
git switch main
git merge --no-ff hotfix/critical-bug
git tag -a v1.2.1 -m "Hotfix version 1.2.1"
git push origin main --tags

# Merge to develop
git switch develop
git merge --no-ff hotfix/critical-bug
git push origin develop

# Delete hotfix branch
git branch -d hotfix/critical-bug
```

### Trunk-Based Development

Developers work on short-lived branches (or directly on trunk/main) with frequent integration.

**Characteristics:**
- Very short-lived feature branches (1-2 days max)
- Frequent commits to main
- Feature flags for incomplete features
- Strong CI/CD pipeline required

**Workflow:**
```bash
# 1. Update main
git switch main
git pull origin main

# 2. Create short-lived branch
git switch -c add-logging

# 3. Make small, focused changes
git add .
git commit -m "Add structured logging"

# 4. Push and create PR immediately
git push -u origin add-logging

# 5. Quick review and merge (same day)
# Merge via PR

# 6. Delete branch
git branch -d add-logging
```

**Best for:**
- Teams with strong CI/CD
- Continuous delivery
- Fast-moving projects

### Versioning and Release Strategies

#### Semantic Versioning (SemVer)

Format: `MAJOR.MINOR.PATCH` (e.g., 2.4.1)

- **MAJOR**: Breaking changes (incompatible API changes)
- **MINOR**: New features (backward-compatible)
- **PATCH**: Bug fixes (backward-compatible)

Examples:
- `1.0.0` → `1.0.1` - Bug fix
- `1.0.1` → `1.1.0` - New feature
- `1.1.0` → `2.0.0` - Breaking change

**Pre-release versions:**
- `1.0.0-alpha`
- `1.0.0-beta.1`
- `1.0.0-rc.2` (release candidate)

#### Git Tags for Versioning

```bash
# Lightweight tag (just a pointer)
git tag v1.0.0

# Annotated tag (recommended - includes message, tagger, date)
git tag -a v1.0.0 -m "Release version 1.0.0"

# Tag specific commit
git tag -a v1.0.0 abc1234 -m "Release version 1.0.0"

# List tags
git tag

# List tags with pattern
git tag -l "v1.*"

# Show tag details
git show v1.0.0

# Push single tag
git push origin v1.0.0

# Push all tags
git push origin --tags

# Delete local tag
git tag -d v1.0.0

# Delete remote tag
git push origin --delete v1.0.0

# Checkout a tag
git checkout v1.0.0
```

**Release workflow:**
```bash
# 1. Prepare release
git switch main
git pull origin main

# 2. Update version in code (package.json, etc.)

# 3. Commit version bump
git commit -am "Bump version to 1.2.0"

# 4. Create tag
git tag -a v1.2.0 -m "Release version 1.2.0

Features:
- Add user authentication
- Improve performance

Bug fixes:
- Fix memory leak in data processing"

# 5. Push commits and tags
git push origin main
git push origin v1.2.0

# 6. Create release on GitHub/GitLab (with changelog)
```

---

## 5. Advanced Git Concepts

### Interactive Rebase

Rewrite, reorder, squash, or edit commits.

```bash
# Rebase last 3 commits
git rebase -i HEAD~3

# Rebase from specific commit
git rebase -i abc1234
```

**Interactive rebase screen:**
```
pick abc1234 Add user authentication
pick def5678 Fix typo in auth
pick ghi9012 Update documentation

# Commands:
# p, pick = use commit
# r, reword = use commit, but edit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like squash, but discard commit message
# d, drop = remove commit
```

**Common use cases:**

**1. Squash commits:**
```bash
# Before:
# * ghi9012 Update documentation
# * def5678 Fix typo in auth
# * abc1234 Add user authentication

git rebase -i HEAD~3

# Change to:
pick abc1234 Add user authentication
squash def5678 Fix typo in auth
squash ghi9012 Update documentation

# Result: One commit with combined changes
```

**2. Reword commit message:**
```bash
git rebase -i HEAD~1

# Change 'pick' to 'reword'
reword abc1234 Add user authentication

# Editor opens to change message
```

**3. Reorder commits:**
```bash
# Simply reorder lines in interactive rebase
pick ghi9012 Update documentation
pick abc1234 Add user authentication
pick def5678 Fix typo in auth
```

**4. Edit a commit:**
```bash
git rebase -i HEAD~3

# Change 'pick' to 'edit'
edit abc1234 Add user authentication

# Make changes
git add .
git commit --amend

# Continue rebase
git rebase --continue
```

**Abort rebase:**
```bash
git rebase --abort
```

### Cherry-pick

Apply specific commits from one branch to another.

```bash
# Apply single commit to current branch
git cherry-pick abc1234

# Apply multiple commits
git cherry-pick abc1234 def5678

# Apply commit range
git cherry-pick abc1234..ghi9012

# Cherry-pick without committing (stage only)
git cherry-pick -n abc1234

# Continue after resolving conflicts
git cherry-pick --continue

# Abort cherry-pick
git cherry-pick --abort
```

**Example use case:**
```bash
# You're on main and need a specific bug fix from develop
git switch main
git cherry-pick def5678  # Commit from develop branch
# Now that bug fix is also on main
```

### Reset (soft, mixed, hard)

Move HEAD and optionally modify staging area and working directory.

**Three modes:**

**1. Soft reset** - Move HEAD only (keep staging and working directory)
```bash
git reset --soft HEAD~1

# Before:
# Working directory: Changed files
# Staging area: Staged files
# HEAD: At commit C

# After:
# Working directory: Changed files (unchanged)
# Staging area: Staged files (unchanged) + changes from C
# HEAD: At commit B (moved back)

# Use case: Undo commit but keep changes staged
```

**2. Mixed reset** - Default (move HEAD, reset staging, keep working directory)
```bash
git reset HEAD~1
# or
git reset --mixed HEAD~1

# Before:
# Working directory: Changed files
# Staging area: Staged files
# HEAD: At commit C

# After:
# Working directory: Changed files + changes from C (unchanged)
# Staging area: Empty (reset)
# HEAD: At commit B (moved back)

# Use case: Undo commit and unstage, but keep file changes
```

**3. Hard reset** - Move HEAD, reset staging and working directory (DESTRUCTIVE!)
```bash
git reset --hard HEAD~1

# Before:
# Working directory: Changed files
# Staging area: Staged files
# HEAD: At commit C

# After:
# Working directory: Clean (changes lost!)
# Staging area: Empty
# HEAD: At commit B (moved back)

# Use case: Completely discard commits and all changes
```

**Common scenarios:**

```bash
# Undo last commit, keep changes staged
git reset --soft HEAD~1

# Undo last commit, unstage changes
git reset HEAD~1

# Discard last commit and all changes (DANGEROUS!)
git reset --hard HEAD~1

# Reset to specific commit
git reset --hard abc1234

# Unstage a specific file
git reset HEAD filename.txt

# Discard all local changes
git reset --hard HEAD
```

### Revert vs Reset

**git reset** - Rewrites history (dangerous for shared branches)
**git revert** - Creates new commit that undoes changes (safe for shared branches)

```bash
# Reset (rewrite history)
# main: A---B---C---D
git reset --hard B
# main: A---B (C and D are gone!)

# Revert (create new commit)
# main: A---B---C---D
git revert D
# main: A---B---C---D---D' (D' undoes changes from D)
```

**Using revert:**
```bash
# Revert last commit
git revert HEAD

# Revert specific commit
git revert abc1234

# Revert multiple commits (creates multiple revert commits)
git revert HEAD~3..HEAD

# Revert without committing (stage changes only)
git revert -n abc1234

# Continue after resolving conflicts
git revert --continue

# Abort revert
git revert --abort
```

**When to use:**
- **Reset**: Local branches, cleaning up local history
- **Revert**: Shared/public branches, keeping history intact

### Stash

Temporarily save changes without committing.

```bash
# Stash current changes
git stash

# Stash with message
git stash save "Work in progress on feature X"

# Stash including untracked files
git stash -u

# Stash including untracked and ignored files
git stash -a

# List stashes
git stash list
# Output:
# stash@{0}: WIP on main: abc1234 Commit message
# stash@{1}: On feature: def5678 Another message

# Show stash contents
git stash show
git stash show -p  # Show full diff

# Apply most recent stash (keep it in stash list)
git stash apply

# Apply specific stash
git stash apply stash@{1}

# Apply and remove from stash list
git stash pop

# Apply specific stash and remove
git stash pop stash@{1}

# Create branch from stash
git stash branch new-branch-name

# Drop specific stash
git stash drop stash@{0}

# Clear all stashes
git stash clear
```

**Example workflow:**
```bash
# Working on feature branch
git status
# Modified files: feature.js

# Urgent bug fix needed on main
git stash save "Half-done feature work"

# Switch to main and fix bug
git switch main
git switch -c hotfix/urgent-bug
# ... fix bug, commit, merge ...

# Return to feature work
git switch feature-branch
git stash pop
# Continue working
```

### Tags

Mark specific points in history (usually releases).

**Lightweight tag:**
```bash
git tag v1.0
```

**Annotated tag (recommended):**
```bash
git tag -a v1.0 -m "Version 1.0 release"
```

**Tag operations:**
```bash
# List all tags
git tag

# Search tags
git tag -l "v1.*"

# Show tag data
git show v1.0

# Tag specific commit
git tag -a v1.0 abc1234 -m "Release 1.0"

# Push tag to remote
git push origin v1.0

# Push all tags
git push origin --tags

# Delete local tag
git tag -d v1.0

# Delete remote tag
git push origin --delete v1.0

# Checkout tag (creates detached HEAD)
git checkout v1.0

# Create branch from tag
git checkout -b version-1-fixes v1.0
```

### Git Hooks

Scripts that run automatically on certain Git events.

**Hook locations:** `.git/hooks/`

**Common hooks:**

**1. pre-commit** - Runs before commit is created
```bash
#!/bin/sh
# .git/hooks/pre-commit

# Run linter
npm run lint
if [ $? -ne 0 ]; then
    echo "Linting failed. Commit aborted."
    exit 1
fi

# Run tests
npm test
if [ $? -ne 0 ]; then
    echo "Tests failed. Commit aborted."
    exit 1
fi
```

**2. commit-msg** - Validate commit message
```bash
#!/bin/sh
# .git/hooks/commit-msg

commit_msg_file=$1
commit_msg=$(cat "$commit_msg_file")

# Check if commit message starts with ticket number
if ! echo "$commit_msg" | grep -qE "^[A-Z]+-[0-9]+"; then
    echo "Error: Commit message must start with ticket number (e.g., JIRA-123)"
    exit 1
fi
```

**3. pre-push** - Runs before push
```bash
#!/bin/sh
# .git/hooks/pre-push

# Run full test suite
npm run test:all
if [ $? -ne 0 ]; then
    echo "Tests failed. Push aborted."
    exit 1
fi
```

**4. post-merge** - Runs after merge
```bash
#!/bin/sh
# .git/hooks/post-merge

# Install dependencies if package.json changed
if git diff-tree -r --name-only --no-commit-id ORIG_HEAD HEAD | grep --quiet "package.json"; then
    echo "package.json changed. Running npm install..."
    npm install
fi
```

**Making hooks executable:**
```bash
chmod +x .git/hooks/pre-commit
```

**Sharing hooks with team:**
```bash
# Create hooks directory in repository
mkdir scripts/git-hooks

# Add hooks
cat > scripts/git-hooks/pre-commit << 'EOF'
#!/bin/sh
npm run lint
EOF

# Setup script
cat > scripts/setup-hooks.sh << 'EOF'
#!/bin/sh
cp scripts/git-hooks/* .git/hooks/
chmod +x .git/hooks/*
EOF

# Team members run:
./scripts/setup-hooks.sh
```

**Tools:**
- **Husky**: Popular tool for managing Git hooks in Node.js projects
- **pre-commit**: Framework for managing Git hooks (Python)

### Submodules

Include one Git repository within another.

**Adding a submodule:**
```bash
# Add submodule
git submodule add https://github.com/user/library.git libs/library

# This creates:
# - .gitmodules file (submodule configuration)
# - libs/library directory (the submodule)

# Commit submodule addition
git add .gitmodules libs/library
git commit -m "Add library submodule"
git push
```

**Cloning repository with submodules:**
```bash
# Option 1: Clone and initialize submodules separately
git clone https://github.com/user/project.git
cd project
git submodule init
git submodule update

# Option 2: Clone with submodules in one command
git clone --recurse-submodules https://github.com/user/project.git
```

**Updating submodules:**
```bash
# Update submodule to latest commit from its remote
cd libs/library
git pull origin main
cd ../..
git add libs/library
git commit -m "Update library submodule"

# Or from parent repository:
git submodule update --remote libs/library
git add libs/library
git commit -m "Update library submodule"
```

**Working with submodules:**
```bash
# Initialize submodules after cloning
git submodule init

# Update all submodules
git submodule update

# Update and fetch latest changes
git submodule update --remote

# Execute command in each submodule
git submodule foreach 'git checkout main'

# Show submodule status
git submodule status
```

**Removing a submodule:**
```bash
# 1. Remove from .gitmodules
git config -f .gitmodules --remove-section submodule.libs/library

# 2. Remove from .git/config
git config -f .git/config --remove-section submodule.libs/library

# 3. Remove from working tree
git rm --cached libs/library
rm -rf libs/library

# 4. Remove from .git/modules
rm -rf .git/modules/libs/library

# 5. Commit
git commit -m "Remove library submodule"
```

**Alternatives to submodules:**
- **Git subtree**: Simpler but copies entire repository
- **Package managers**: npm, pip, Maven for dependencies
- **Monorepos**: Keep everything in one repository

---

## 6. Git for DevOps

### Git in CI/CD Pipelines

Git is the trigger and source for most CI/CD pipelines.

**Common CI/CD + Git patterns:**

**1. GitHub Actions example:**
```yaml
# .github/workflows/ci.yml
name: CI Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
      
    - name: Set up Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        
    - name: Install dependencies
      run: npm ci
      
    - name: Run linter
      run: npm run lint
      
    - name: Run tests
      run: npm test
      
    - name: Build application
      run: npm run build
      
    - name: Deploy to staging
      if: github.ref == 'refs/heads/develop'
      run: ./deploy-staging.sh
      
    - name: Deploy to production
      if: github.ref == 'refs/heads/main'
      run: ./deploy-production.sh
```

**2. GitLab CI example:**
```yaml
# .gitlab-ci.yml
stages:
  - test
  - build
  - deploy

test:
  stage: test
  script:
    - npm install
    - npm run lint
    - npm test
  only:
    - merge_requests
    - main
    - develop

build:
  stage: build
  script:
    - npm run build
  artifacts:
    paths:
      - dist/
  only:
    - main
    - develop

deploy_staging:
  stage: deploy
  script:
    - ./deploy-staging.sh
  only:
    - develop
  environment:
    name: staging

deploy_production:
  stage: deploy
  script:
    - ./deploy-production.sh
  only:
    - main
  environment:
    name: production
  when: manual
```

**3. Jenkins Pipeline:**
```groovy
// Jenkinsfile
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/user/repo.git'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm install'
                sh 'npm test'
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                sh './deploy.sh'
            }
        }
    }
}
```

**Git triggers in CI/CD:**
- Push to specific branches
- Pull request creation/update
- Tag creation (releases)
- Scheduled builds with latest code

### GitOps Fundamentals

GitOps uses Git as the single source of truth for infrastructure and application deployments.

**Core principles:**

1. **Declarative**: Desired state defined in Git
2. **Versioned**: All changes tracked via Git commits
3. **Immutable**: Changes don't modify in-place; new versions created
4. **Automated**: Changes in Git trigger automatic deployments
5. **Auditable**: Full change history in Git

**GitOps workflow:**

```
Developer → Commits to Git → CI builds & tests → Updates deployment repo
                                                         ↓
                              GitOps operator watches ← Deployment repo
                                                         ↓
                              Operator syncs state → Kubernetes cluster
```

**Example GitOps structure:**

```
infrastructure-repo/
├── apps/
│   ├── frontend/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   └── backend/
│       ├── deployment.yaml
│       └── service.yaml
├── infrastructure/
│   ├── ingress/
│   └── databases/
└── environments/
    ├── dev/
    ├── staging/
    └── production/
```

**Popular GitOps tools:**
- **ArgoCD**: Kubernetes continuous delivery
- **Flux**: GitOps toolkit for Kubernetes
- **Jenkins X**: Cloud-native CI/CD with GitOps

**Example ArgoCD application:**
```yaml
# application.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/user/k8s-manifests.git
    targetRevision: HEAD
    path: apps/my-app
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

**Benefits:**
- Infrastructure as code
- Improved security (no cluster credentials needed)
- Disaster recovery (rebuild from Git)
- Audit trail
- Rollback capability

### Branch Protection Rules

Protect important branches from force pushes, accidental deletion, and enforce quality standards.

**Common protection rules:**

**On GitHub:**
1. Navigate to Settings → Branches → Add rule
2. Configure:
   - Require pull request reviews
   - Require status checks (CI) to pass
   - Require signed commits
   - Include administrators
   - Restrict who can push
   - Require linear history
   - Prevent force push
   - Prevent deletion

**On GitLab:**
Protected Branches settings:
- Allowed to merge
- Allowed to push
- Require approval
- Code owner approval

**Example protection rules for `main` branch:**

```
✓ Require pull request before merging
  ✓ Require 2 approvals
  ✓ Dismiss stale reviews
  ✓ Require review from code owners
  
✓ Require status checks to pass
  ✓ Require branches to be up to date
  - CI/Build
  - Tests
  - Security scan
  
✓ Require signed commits
✓ Require linear history
✓ Include administrators

✗ Allow force pushes
✗ Allow deletions
```

**Benefits:**
- Prevent accidental damage
- Enforce code review
- Ensure quality (tests must pass)
- Maintain clean history
- Audit compliance

**Code owners file (CODEOWNERS):**
```
# .github/CODEOWNERS

# Default owners
* @team-leads

# Frontend code
/frontend/ @frontend-team

# Backend API
/api/ @backend-team @security-team

# Infrastructure
/terraform/ @devops-team
*.tf @devops-team

# Documentation
/docs/ @tech-writers
*.md @tech-writers
```

### Managing Secrets

**NEVER commit secrets to Git!**

**What are secrets?**
- API keys
- Database passwords
- Private keys
- OAuth tokens
- SSL certificates
- Environment-specific configuration

**Best practices:**

**1. Use .gitignore**
```bash
# .gitignore

# Environment files
.env
.env.local
.env.production

# Secrets
secrets/
*.key
*.pem
config/secrets.yml

# Cloud provider credentials
.aws/credentials
.gcloud/
```

**2. Environment variables**
```bash
# Don't commit:
DATABASE_URL=postgresql://user:password@localhost/db

# Instead, use:
# .env.example (commit this)
DATABASE_URL=postgresql://user:pass@localhost/dbname

# .env (don't commit)
DATABASE_URL=postgresql://real_user:real_pass@prod.db.com/prod_db
```

**3. Secret management tools**

**GitHub Secrets:**
```yaml
# .github/workflows/deploy.yml
- name: Deploy
  env:
    API_KEY: ${{ secrets.API_KEY }}
    DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
  run: ./deploy.sh
```

**Vault, AWS Secrets Manager, etc.:**
```bash
# Fetch secret at runtime
SECRET=$(aws secretsmanager get-secret-value \
  --secret-id prod/db/password \
  --query SecretString \
  --output text)
```

**4. Encrypt secrets in repository**

**Using git-crypt:**
```bash
# Install git-crypt
sudo apt install git-crypt

# Initialize in repo
git-crypt init

# Add .gitattributes
echo "secrets/* filter=git-crypt diff=git-crypt" >> .gitattributes
echo ".env filter=git-crypt diff=git-crypt" >> .gitattributes

# Add GPG users who can decrypt
git-crypt add-gpg-user user@example.com

# Files are encrypted in Git, decrypted locally
```

**Using SOPS (Secrets OPerationS):**
```bash
# Encrypt file
sops -e secrets.yaml > secrets.enc.yaml

# Commit encrypted file
git add secrets.enc.yaml

# Decrypt when needed
sops -d secrets.enc.yaml > secrets.yaml
```

**5. Remove committed secrets**

```bash
# If you accidentally committed a secret:

# Option 1: BFG Repo-Cleaner (easiest)
java -jar bfg.jar --delete-files secrets.txt
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# Option 2: git filter-branch
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch secrets.txt" \
  --prune-empty --tag-name-filter cat -- --all

# Option 3: git filter-repo (recommended)
git filter-repo --invert-paths --path secrets.txt

# Force push to rewrite remote history
git push origin --force --all

# IMPORTANT: Rotate the compromised secret immediately!
```

**Template for secrets:**
```bash
# config/secrets.example.yml (commit this)
database:
  host: localhost
  username: myuser
  password: CHANGE_ME

api:
  key: YOUR_API_KEY_HERE
  secret: YOUR_SECRET_HERE

# config/secrets.yml (don't commit - add to .gitignore)
database:
  host: prod.database.com
  username: prod_user
  password: actual_secure_password

api:
  key: real_api_key_123
  secret: real_secret_456
```

**Detection tools:**
- **git-secrets**: Prevent committing secrets
- **TruffleHog**: Find secrets in Git history
- **Gitleaks**: Scan for secrets

---

## 7. Common Mistakes & Interview Tips

### Frequent Git Mistakes and How to Fix Them

#### 1. Committed to wrong branch

**Problem:**
```bash
# You're on main, should be on feature branch
git add .
git commit -m "Add new feature"
# Oops! This should be on feature-branch
```

**Fix:**
```bash
# Create new branch with current changes
git branch feature-branch

# Reset main to previous commit (keep changes)
git reset --hard HEAD~1

# Switch to feature branch
git switch feature-branch

# Your commit is now on feature-branch!
```

#### 2. Need to undo last commit

**Scenarios:**

**Keep changes, undo commit:**
```bash
git reset --soft HEAD~1
# Changes are staged, commit is undone
```

**Keep changes, unstage:**
```bash
git reset HEAD~1
# Changes are in working directory, unstaged
```

**Discard everything:**
```bash
git reset --hard HEAD~1
# CAREFUL: Changes are lost!
```

#### 3. Committed wrong files

**Before pushing:**
```bash
# Remove file from last commit
git reset HEAD~ file.txt
git commit --amend --no-edit

# Or completely redo commit
git reset --soft HEAD~1
git restore --staged unwanted.txt
git commit
```

**After pushing (shared branch):**
```bash
# Create new commit to remove file
git rm --cached unwanted.txt
git commit -m "Remove unwanted file"
git push
```

#### 4. Wrong commit message

**Before pushing:**
```bash
git commit --amend -m "Corrected commit message"
```

**After pushing (local branch):**
```bash
git commit --amend -m "Corrected commit message"
git push --force-with-lease
```

**After pushing (shared branch):**
```bash
# Can't change - commits are immutable on shared branches
# Live with it or add clarifying comment in PR
```

#### 5. Merge conflicts

**See Section 2 for detailed resolution**

Quick fix:
```bash
# Accept all their changes
git checkout --theirs .
git add .

# Accept all our changes
git checkout --ours .
git add .

# Abort merge
git merge --abort
```

#### 6. Accidentally deleted branch

**If not pushed:**
```bash
# Find the commit SHA
git reflog

# Output:
# abc1234 HEAD@{0}: checkout: moving from feature-branch to main
# def5678 HEAD@{1}: commit: Last commit on feature-branch

# Restore branch
git branch feature-branch def5678
```

#### 7. Pushed sensitive data

**Immediate actions:**
```bash
# 1. Remove from repository
git filter-repo --invert-paths --path secrets.txt
git push --force

# 2. ROTATE THE SECRET IMMEDIATELY!
# The secret is compromised - change it now!

# 3. Notify security team if applicable
```

#### 8. Detached HEAD state

**Problem:**
```bash
git checkout abc1234
# You are in 'detached HEAD' state
```

**Fix:**
```bash
# Create branch to save your position
git checkout -b temp-branch

# Or just checkout a branch
git checkout main
```

#### 9. Lost commits

```bash
# View reflog to find lost commits
git reflog

# Output shows all HEAD movements
# Find the commit you want

# Restore it
git checkout abc1234
# Or create branch
git branch recovered-branch abc1234
```

#### 10. Large files committed

```bash
# Remove large file from history
git filter-repo --strip-blobs-bigger-than 10M

# Or specific file
git filter-repo --invert-paths --path large-file.zip

# Force push
git push --force
```

### Common Git Interview Questions

#### Basic Questions

**Q1: What is Git?**
A: Git is a distributed version control system for tracking changes in source code during software development. It allows multiple developers to work together, maintains complete history, and enables branching and merging.

**Q2: What is the difference between Git and GitHub?**
A: Git is the version control software you run locally. GitHub is a cloud-based hosting service for Git repositories that adds collaboration features like pull requests, issues, and project management.

**Q3: Explain the Git workflow.**
A: 
1. Modify files in working directory
2. Stage changes with `git add`
3. Commit to local repository with `git commit`
4. Push to remote repository with `git push`

**Q4: What is a Git repository?**
A: A directory containing your project files and a `.git` subdirectory that stores all version control information (commits, branches, history, configuration).

**Q5: What is the difference between `git pull` and `git fetch`?**
A: 
- `git fetch`: Downloads changes from remote but doesn't merge them
- `git pull`: Downloads changes AND merges them (`fetch` + `merge`)

#### Intermediate Questions

**Q6: What is a merge conflict and how do you resolve it?**
A: A merge conflict occurs when Git can't automatically merge changes. Resolution steps:
1. Open conflicted files
2. Look for conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`)
3. Choose which changes to keep
4. Remove conflict markers
5. `git add` the resolved files
6. `git commit` to complete merge

**Q7: Difference between `git merge` and `git rebase`?**
A:
- `git merge`: Creates a merge commit, preserves complete history, non-linear
- `git rebase`: Rewrites history linearly, cleaner history, dangerous on shared branches

**Q8: What is `git stash`?**
A: Temporarily saves uncommitted changes so you can switch branches or pull updates, then reapply the changes later with `git stash pop`.

**Q9: Explain `git reset` modes.**
A:
- `--soft`: Move HEAD, keep staging and working directory
- `--mixed` (default): Move HEAD, reset staging, keep working directory
- `--hard`: Move HEAD, reset staging AND working directory (destructive)

**Q10: What is `git cherry-pick`?**
A: Applies a specific commit from one branch to another without merging the entire branch.

#### Advanced Questions

**Q11: What is the difference between `git revert` and `git reset`?**
A:
- `git reset`: Moves branch pointer backward (rewrites history) - use on local branches
- `git revert`: Creates new commit that undoes changes (preserves history) - use on shared branches

**Q12: How do you undo a pushed commit?**
A:
- If branch is not shared: `git reset` + `git push --force`
- If branch is shared: `git revert` + `git push`

**Q13: What are Git hooks?**
A: Scripts that run automatically before or after Git events (commit, push, merge). Located in `.git/hooks/`. Examples: pre-commit (run tests), commit-msg (validate message).

**Q14: Explain Git branching strategies.**
A:
- **Feature branching**: Each feature on separate branch
- **Gitflow**: Structured with main, develop, feature, release, hotfix branches
- **Trunk-based**: Short-lived branches, frequent merges to main

**Q15: What is a detached HEAD state?**
A: When HEAD points to a specific commit instead of a branch. Happens when checking out a commit directly. Changes made won't belong to any branch unless you create one.

**Q16: How do you find a bug introduced in recent commits?**
A: Use `git bisect`:
```bash
git bisect start
git bisect bad          # Current version is bad
git bisect good abc123  # Known good commit
# Git checks out middle commit
# Test and mark as good/bad
git bisect good/bad
# Repeat until bug found
git bisect reset
```

**Q17: What are Git submodules?**
A: A way to include one Git repository inside another. Used for dependencies or shared code. Managed with `git submodule` commands.

**Q18: How do you handle large files in Git?**
A: Use Git LFS (Large File Storage):
```bash
git lfs install
git lfs track "*.psd"
git add .gitattributes
```

**Q19: What is reflog and when would you use it?**
A: Reflog records updates to HEAD. Use it to recover lost commits, find deleted branches, or undo mistakes:
```bash
git reflog
git checkout abc1234  # Restore lost commit
```

**Q20: How do you sign commits?**
A: Using GPG keys:
```bash
git config --global user.signingkey YOUR_GPG_KEY
git commit -S -m "Signed commit"
git config --global commit.gpgsign true  # Sign all commits
```

#### DevOps-Specific Questions

**Q21: How is Git used in CI/CD pipelines?**
A: 
- Git commits trigger builds
- Branches determine deployment environments (main→prod, develop→staging)
- Tags trigger releases
- Pull requests trigger automated tests

**Q22: What is GitOps?**
A: A DevOps practice where Git is the single source of truth for infrastructure and applications. Changes to Git automatically deploy to environments. Tools: ArgoCD, Flux.

**Q23: How do you protect sensitive data in Git?:
- Use `.gitignore` for secret files
- Environment variables for credentials
- Git-crypt or SOPS for encrypted secrets
- Secret management tools (Vault, AWS Secrets Manager)
- Never commit API keys, passwords, or tokens

**Q24: Explain branch protection rules.**
A: Rules that protect important branches:
- Require pull request reviews
- Require status checks (CI) to pass
- Prevent force push
- Prevent deletion
- Require signed commits

**Q25: How do you manage multiple environments (dev/staging/prod) with Git?**
A: Common approaches:
- **Branch-based**: Different branches for each environment
- **Tag-based**: Tags mark releases to environments
- **GitOps**: Separate repos for config, GitOps tools deploy

---

## 8. Git Command Cheat Sheet

### Setup and Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --list
git config --global core.editor "vim"
```

### Creating Repositories

```bash
git init                                    # Initialize repository
git clone <url>                             # Clone repository
git clone -b <branch> <url>                 # Clone specific branch
```

### Basic Snapshotting

```bash
git status                                  # Check status
git status -s                               # Short status
git add <file>                              # Stage file
git add .                                   # Stage all
git add -p                                  # Interactive staging
git commit -m "message"                     # Commit
git commit -am "message"                    # Stage + commit tracked files
git commit --amend                          # Amend last commit
git reset HEAD <file>                       # Unstage file
git reset --soft HEAD~1                     # Undo commit, keep changes staged
git reset HEAD~1                            # Undo commit, unstage changes
git reset --hard HEAD~1                     # Undo commit, discard changes
git rm <file>                               # Remove file
git mv <old> <new>                          # Rename file
```

### Branching

```bash
git branch                                  # List branches
git branch <branch-name>                    # Create branch
git branch -d <branch-name>                 # Delete branch (safe)
git branch -D <branch-name>                 # Force delete branch
git branch -m <new-name>                    # Rename current branch
git checkout <branch>                       # Switch branch (old)
git checkout -b <branch>                    # Create + switch (old)
git switch <branch>                         # Switch branch (new)
git switch -c <branch>                      # Create + switch (new)
```

### Merging

```bash
git merge <branch>                          # Merge branch
git merge --no-ff <branch>                  # No fast-forward
git merge --squash <branch>                 # Squash merge
git merge --abort                           # Abort merge
```

### Rebasing

```bash
git rebase <branch>                         # Rebase onto branch
git rebase -i HEAD~3                        # Interactive rebase last 3
git rebase --continue                       # Continue after conflict
git rebase --abort                          # Abort rebase
```

### Remote Repositories

```bash
git remote                                  # List remotes
git remote -v                               # List with URLs
git remote add <name> <url>                 # Add remote
git remote remove <name>                    # Remove remote
git remote rename <old> <new>               # Rename remote
git fetch                                   # Fetch from remote
git fetch --all                             # Fetch all remotes
git pull                                    # Fetch + merge
git pull --rebase                           # Fetch + rebase
git push                                    # Push to remote
git push -u origin <branch>                 # Push + set upstream
git push --force-with-lease                 # Safe force push
git push origin --delete <branch>           # Delete remote branch
```

### Inspection

```bash
git log                                     # Show commits
git log --oneline                           # One line per commit
git log --graph --oneline --all             # Graphical log
git log -p                                  # Show patches (diffs)
git log --stat                              # Show stats
git log --author="Name"                     # Filter by author
git log --since="2024-01-01"                # Filter by date
git log -- <file>                           # Log for file
git show <commit>                           # Show commit details
git diff                                    # Show unstaged changes
git diff --staged                           # Show staged changes
git diff <branch1> <branch2>                # Compare branches
git blame <file>                            # Show who changed what
```

### Stashing

```bash
git stash                                   # Stash changes
git stash save "message"                    # Stash with message
git stash -u                                # Include untracked
git stash list                              # List stashes
git stash show                              # Show latest stash
git stash apply                             # Apply latest stash
git stash apply stash@{n}                   # Apply specific stash
git stash pop                               # Apply + remove
git stash drop                              # Delete stash
git stash clear                             # Delete all stashes
```

### Tagging

```bash
git tag                                     # List tags
git tag <tagname>                           # Lightweight tag
git tag -a <tagname> -m "message"           # Annotated tag
git tag -a <tagname> <commit>               # Tag specific commit
git show <tagname>                          # Show tag
git push origin <tagname>                   # Push tag
git push origin --tags                      # Push all tags
git tag -d <tagname>                        # Delete local tag
git push origin --delete <tagname>          # Delete remote tag
```

### Advanced

```bash
git cherry-pick <commit>                    # Apply specific commit
git revert <commit>                         # Revert commit (new commit)
git clean -fd                               # Remove untracked files/dirs
git reflog                                  # Show reflog
git bisect start                            # Start binary search
git bisect good/bad                         # Mark commits
git bisect reset                            # End bisect
```

### Submodules

```bash
git submodule add <url> <path>              # Add submodule
git submodule init                          # Initialize submodules
git submodule update                        # Update submodules
git submodule update --remote               # Update to latest
git clone --recurse-submodules <url>        # Clone with submodules
```

### Useful Aliases

```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual 'log --graph --oneline --all'
```

---

## Quick Reference Tables

### File States

| State | Description | Command to Achieve |
|-------|-------------|-------------------|
| Untracked | New file, Git doesn't know about it | Create new file |
| Unmodified | Tracked, no changes | `git commit` |
| Modified | Tracked, has changes | Edit file |
| Staged | Ready to commit | `git add` |

### Reset Modes

| Mode | HEAD | Staging | Working Dir | Use Case |
|------|------|---------|-------------|----------|
| `--soft` | ✓ Move | Keep | Keep | Undo commit, keep staged |
| `--mixed` | ✓ Move | ✓ Reset | Keep | Undo commit, unstage |
| `--hard` | ✓ Move | ✓ Reset | ✓ Reset | Discard everything |

### Merge vs Rebase

| Aspect | Merge | Rebase |
|--------|-------|--------|
| History | Non-linear, preserves all commits | Linear, rewrites history |
|reates merge commit | Changes commit hashes |
| Use on | Any branch | Only local branches |
| Pros | Safe, preserves context | Clean history |
| Cons | Can be messy | Dangerous on shared branches |

### Common Operations

| Task | Command |
|------|---------|
| Undo last commit (keep changes) | `git reset --soft HEAD~1` |
| Undo last commit (discard changes) | `git reset --hard HEAD~1` |
| Unstage file | `git reset HEAD <file>` |
| Discard local changes | `git checkout -- <file>` |
| View file at specific commit | `git show <commit>:<file>` |
| List files in commit | `git show --name-only <commit>` |
| Compare file versions | `git diff <commit1> <commit2> <file>` |
| Find commit that introduced bug | `git bisect` |

---

## Best Practices Summary

1. **Commit often**, with meaningful messages
2. **Pull before push** to avoid conflicts
3. **Create branches** for features/fixes
4. **Never rebase** public/shared branches
5. **Review before committing** (use `git diff`)
6. **Keep commits atomic** (one logical change)
7. **Write good commit messages** (imperative mood, 50 char summary)
8. **Use `.gitignore`** to exclude generated files
9. **Never commit secrets** (use env variables)
10. **Protect main branch** (require PR reviews)
11. **Tag releases** for version tracking
12. **Document your workflow** (CONTRIBUTING.md)

---

## Recommended Learning Path

1. ✅ Install Git and configure
2. ✅ Practice basic commands (init, add, commit, status, log)
3. ✅ Learn branching (create, switch, merge)
4. ✅ Work with reositories (clone, push, pull)
5. ✅ Handle merge conflicts
6. ✅ Understand rebase
7. ✅ Use advanced features (stash, cherry-pick, reset)
8. ✅ Study workflows (feature branching, gitflow)
9. ✅ Implement Git in CI/CD
10. ✅ Learn GitOps concepts

---

## Additional Resources

**Official Documentation:**
- [Git Documentation](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book/en/v2) (Free)

**Interactive Learning:**
- [Learn Git Branching](https://learngitbranching.js.org/)
- [Git Imms://gitimmersion.com/)

**Cheat Sheets:**
- [GitHub Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)
- [Atlassian Git Cheat Sheet](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet)

**Tools:**
- GitKraken (GUI)
- Sourcetree (GUI)
- Git Extensions (GUI)
- Tig (Terminal UI)

---

**Good luck with your DevOps Engineer interview! 🚀**

*Remember: The best way to learn Git is by using it. Practice these commands, make mistakes, and learn from them!*
