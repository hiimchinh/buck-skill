# Status Dashboard & Branch Cleanup

## Status Dashboard

View PR status across repos in a colored table.

```bash
# Auto-detect from CWD
buck status

# Filter by group
buck status --group backend

# Show only my PRs
buck status --mine

# Filter by author
buck status --author alice

# Combine filters
buck status --group backend --mine
```

### Flags

| Flag | Description |
|------|-------------|
| `--group`, `-g` | Filter by repo group |
| `--repos`, `-r` | Filter by specific repos |
| `--mine` | Show only my PRs |
| `--author` | Filter by author username |

Output: colored table with repo, branch, title, author, reviewers, status.

## Branch Cleanup

Delete branches across repos. Supports single branch and merged-branch cleanup.

### Single Branch

```bash
# Delete specific branch
buck clean feature/login --repos repo-api,repo-web --yes

# Delete from group
buck clean feature/login --group backend --yes

# Preview
buck clean feature/login --group backend --dry-run
```

### Merged Branch Cleanup

```bash
# Clean all merged PR branches
buck clean --merged --group backend --yes

# Preview merged cleanup
buck clean --merged --group backend --dry-run

# Merged cleanup for specific repos
buck clean --merged --repos repo-api,repo-web --yes
```

### Flags

| Flag | Description |
|------|-------------|
| `--yes` | Skip confirmation prompt |
| `--merged` | Clean branches from merged PRs |
| `--dry-run` | Preview without deleting |
| `--repos`, `-r` | Target specific repos |
| `--group`, `-g` | Target repo group |

Protected branches (main, master, develop) are never deleted.
