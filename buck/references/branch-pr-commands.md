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
| `--from` | `-f` | Source branch (default: config `defaults.source_branch`) |
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

# Custom title (buck >= 0.9.0; default title is derived from branch name)
buck pr feature/login --repos repo-api --title "Add login feature"

# Preview
buck pr feature/login --group backend --dry-run
```

To add reviewers, create the PR first then use the `buck pr reviewers`
subcommand (see `pr-management-commands.md`):

```bash
buck pr reviewers feature/login --add "{uuid1},{uuid2}" --repos repo-api
```

### Flags

| Flag | Short | Description |
|------|-------|-------------|
| `--repos` | `-r` | Comma-separated repo slugs |
| `--group` | `-g` | Repo group from config |
| `--destination` | `-d` | Target branch (default: master) |
| `--title` | `-t` | PR title (default: derived from branch name; buck >= 0.9.0) |
| `--interactive` | `-i` | Force interactive selection |
| `--dry-run` | | Preview only |

### Auto-detect Mode

Running `buck pr` without arguments:
1. Detects current branch from git
2. Detects Bitbucket remote from git origin
3. Creates PR for current repo with current branch
