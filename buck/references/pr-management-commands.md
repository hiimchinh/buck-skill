# PR Management Commands

All subcommands of `buck pr`. Operate on PRs found by branch name.

## Merge PRs

```bash
# Merge with default strategy
buck pr merge feature/login --repos repo-api,repo-web --yes

# Squash merge
buck pr merge feature/login --repos repo-api --strategy squash --yes

# Merge entire group
buck pr merge feature/login --group backend --yes

# Preview without merging
buck pr merge feature/login --group backend --dry-run
```

### Merge Strategies
- `merge-commit` (default)
- `squash`
- `fast-forward`

| Flag | Description |
|------|-------------|
| `--strategy` | Merge strategy: merge-commit, squash, fast-forward |
| `--yes` | Skip confirmation prompt |
| `--close-source-branch` | Delete source branch after merge |
| `--message` | Custom merge commit message |

## Decline PRs

```bash
buck pr decline feature/login --repos repo-api --yes
buck pr decline feature/login --group backend --yes
buck pr decline feature/login --group backend --dry-run
```

## Approve PRs

```bash
buck pr approve feature/login --repos repo-api,repo-web
buck pr approve feature/login --group backend
```

## Add Reviewers

```bash
# Add reviewers by UUID
buck pr reviewers feature/login --add "{uuid1},{uuid2}" --repos repo-api

# Add to group
buck pr reviewers feature/login --add "{uuid}" --group backend
```

## List PRs

```bash
# List open PRs
buck pr list --state OPEN --group backend

# List my PRs only
buck pr list --mine --group backend

# List by author
buck pr list --author alice --repos repo-api

# All states
buck pr list --state MERGED --group backend
```

### PR States
- `OPEN` (default)
- `MERGED`
- `DECLINED`
- `SUPERSEDED`

| Flag | Description |
|------|-------------|
| `--state` | Filter by PR state |
| `--mine` | Show only my PRs |
| `--author` | Filter by author username |
