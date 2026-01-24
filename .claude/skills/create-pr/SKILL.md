---
name: create-pr
description: Creates GitHub pull requests using the gh CLI. Automates committing changes, pushing branches, and creating PRs with titles and descriptions. Use when the user asks to create a PR, pull request, open a PR, or mentions "gh pr create".
compatibility: Requires gh CLI (GitHub CLI) and git
metadata:
  author: coding-agent-train
  version: "1.0"
allowed-tools: Bash(git:*) Bash(gh:*) Read
---

# Create Pull Request Skill

This skill automates the process of creating GitHub pull requests using the `gh` command-line tool.

## When to Use This Skill

Activate this skill when the user:
- Asks to "create a PR" or "create a pull request"
- Says "open a PR" or "make a PR"
- Mentions "gh pr create"
- Wants to submit their changes for review

## Prerequisites

Before using this skill, verify:
1. The `gh` CLI is installed and authenticated (`gh auth status`)
2. You are in a git repository with a remote
3. There are changes to commit or commits ready to push

## Instructions

Follow these steps to create a pull request:

### 1. Check Current Status

```bash
# Verify gh is authenticated
gh auth status

# Check git status
git status

# Check current branch
git branch --show-current
```

### 2. Ensure Changes Are Committed

If there are uncommitted changes:

```bash
# Stage relevant files
git add <files>

# Commit with descriptive message
git commit -m "Your commit message"
```

### 3. Push Branch to Remote

If the current branch is not on remote or has unpushed commits:

```bash
# Push and set upstream
git push -u origin <branch-name>
```

### 4. Create Pull Request

Use the `gh pr create` command with appropriate options:

```bash
# Interactive mode (prompts for title and body)
gh pr create

# With title and body
gh pr create --title "Your PR Title" --body "Description of changes"

# With title and body from file
gh pr create --title "Your PR Title" --body-file PR_DESCRIPTION.md

# Specify base branch
gh pr create --base main --title "Your PR Title" --body "Description"

# Create as draft
gh pr create --draft --title "WIP: Feature" --body "Work in progress"
```

### 5. Verify PR Creation

After creating the PR:

```bash
# View the PR in browser
gh pr view --web

# Check PR status
gh pr status
```

## Common Options

| Option | Description |
|--------|-------------|
| `--title`, `-t` | Pull request title |
| `--body`, `-b` | Pull request body/description |
| `--body-file`, `-F` | Read body from file |
| `--base`, `-B` | Base branch (default: repo default branch) |
| `--head`, `-H` | Head branch (default: current branch) |
| `--draft`, `-d` | Create as draft PR |
| `--web`, `-w` | Open in web browser after creation |
| `--assignee`, `-a` | Assign users (comma-separated) |
| `--reviewer`, `-r` | Request reviewers (comma-separated) |
| `--label`, `-l` | Add labels (comma-separated) |

## Examples

### Example 1: Simple PR Creation

```bash
git add .
git commit -m "Add new feature"
git push -u origin feature-branch
gh pr create --title "Add user authentication" --body "Implements JWT-based authentication"
```

### Example 2: Draft PR with Reviewers

```bash
gh pr create \
  --draft \
  --title "WIP: Refactor database layer" \
  --body "This PR refactors the database access layer for better testability" \
  --reviewer alice,bob \
  --label enhancement
```

### Example 3: PR with Detailed Description from File

```bash
# Create description file
cat > pr-desc.md << 'EOF'
## Summary
This PR adds dark mode support to the application.

## Changes
- Added theme context provider
- Implemented dark mode toggle
- Updated all components to support theming

## Testing
- Tested on Chrome, Firefox, Safari
- Verified theme persistence across sessions
EOF

gh pr create --title "Add dark mode support" --body-file pr-desc.md
```

## Edge Cases and Troubleshooting

### Not Authenticated with GitHub

If `gh auth status` fails:

```bash
# Login to GitHub
gh auth login
```

### No Remote Repository

If the local repo has no remote:

```bash
# Add remote
git remote add origin https://github.com/username/repo.git
```

### Already on Main/Default Branch

If you're on the main branch, create a feature branch first:

```bash
# Create and switch to new branch
git checkout -b feature-branch-name
```

### PR Already Exists

If a PR already exists for the current branch:

```bash
# View existing PR
gh pr view

# Edit existing PR
gh pr edit
```

### Push Rejected (Force Push Needed)

If the remote branch has diverged:

```bash
# Review differences first
git fetch origin
git log HEAD..origin/<branch-name>

# Force push if safe (be careful!)
git push --force-with-lease origin <branch-name>
```

## Best Practices

1. **Write Clear Titles**: Use descriptive, concise titles that summarize the change
2. **Provide Context**: Include why the change is needed, not just what changed
3. **Reference Issues**: Mention related issue numbers (e.g., "Fixes #123")
4. **Use Draft PRs**: Create draft PRs for work-in-progress to get early feedback
5. **Request Reviewers**: Assign appropriate reviewers who are familiar with the code
6. **Add Labels**: Use labels to categorize PRs (bug, feature, documentation, etc.)

## Related Commands

```bash
# List all PRs
gh pr list

# View a specific PR
gh pr view <number>

# Check PR status
gh pr status

# Edit PR details
gh pr edit

# Close a PR
gh pr close <number>

# Merge a PR
gh pr merge <number>
```

## Notes

- The `gh` CLI respects your repository's default branch settings
- PR templates (`.github/pull_request_template.md`) are automatically used if present
- You can configure default PR options in `~/.config/gh/config.yml`
