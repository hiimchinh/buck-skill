# Branch & PR Creation Commands

## Create Branches

Create branches across multiple repos simultaneously.

```bash
# Basic: create branch from default source
buck create feature/login --repos repo-api,repo-web

# From specific source branch
buck create feature/login --repos repo-api,repo-web --from develop

# Using repo group
buck create feature/login --group backend

# With branch prefix from config
buck create login --group backend
# Creates: feature/login (if defaults.branch_prefix: "feature/")

# Preview without creating
buck create feature/login --group backend --dry-run

# Interactive repo selection
buck create feature/login -i
```

### Flags

| Flag | Short | Description |
|------|-------|-------------|
| `--repos` | `-r` | Comma-separated repo slugs |
| `--group` | `-g` | Repo group from config |
| `--from` | | Source branch (default: config `defaults.source_branch`) |
| `--interactive` | `-i` | Force interactive selection |
| `--dry-run` | | Preview only |

## Create Pull Requests

Create PRs across repos. Auto-generates description from commits.

```bash
# Auto-detect branch and repo from git context
buck pr

# Specify branch and repos
buck pr feature/login --repos repo-api,repo-web

# Custom destination branch
buck pr feature/login --group backend --destination develop

# Custom title
buck pr feature/login --repos repo-api --title "Add login feature"

# With reviewers
buck pr feature/login --repos repo-api --reviewers "{uuid1},{uuid2}"

# Preview
buck pr feature/login --group backend --dry-run
```

### Flags

| Flag | Short | Description |
|------|-------|-------------|
| `--repos` | `-r` | Comma-separated repo slugs |
| `--group` | `-g` | Repo group from config |
| `--destination` | `-d` | Target branch (default: config `defaults.source_branch`) |
| `--title` | `-t` | PR title (default: branch name) |
| `--reviewers` | | Comma-separated reviewer UUIDs |
| `--interactive` | `-i` | Force interactive selection |
| `--dry-run` | | Preview only |
| `--close-source-branch` | | Delete source branch after merge |

### Auto-detect Mode

Running `buck pr` without arguments:
1. Detects current branch from git
2. Detects Bitbucket remote from git origin
3. Creates PR for current repo with current branch
