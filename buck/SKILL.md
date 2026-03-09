---
name: buck
description: Multi-repo orchestration for Bitbucket Cloud. Create branches, PRs, merge/decline/approve PRs, status dashboard, branch cleanup across repos.
version: 1.0.0
license: MIT
---

# Buck — Multi-Repo Orchestration CLI

Bitbucket Cloud CLI for managing branches and PRs across multiple repositories simultaneously.

## Install

```bash
brew install chinhstringee/tap/buck
# Or: go install github.com/chinhstringee/buck@latest
```

## Setup

1. Create config: `buck setup` (interactive) or manually create `.buck.yaml`
2. Auth: API token (default) or OAuth (`buck login`)

Config location: `.buck.yaml` in CWD or `$HOME`. See `references/config-reference.md`.

## Quick Reference

| Task | Command |
|------|---------|
| Create branches | `buck create <branch> --repos a,b` or `--group backend` |
| Create PRs | `buck pr <branch> --repos a,b` or auto-detect: `buck pr` |
| Merge PRs | `buck pr merge <branch> --repos a --strategy squash --yes` |
| Decline PRs | `buck pr decline <branch> --group backend --yes` |
| Approve PRs | `buck pr approve <branch> --repos a,b` |
| Add reviewers | `buck pr reviewers <branch> --add "{uuid}" --repos a` |
| List PRs | `buck pr list --state OPEN --mine --group backend` |
| Status dashboard | `buck status` or `buck status --group backend --mine` |
| Clean branches | `buck clean <branch> --repos a,b --yes` |
| Clean merged | `buck clean --merged --group backend --dry-run` |
| List repos | `buck list` |

## Common Flags

All repo-targeting commands support:
- `-r, --repos` — comma-separated repo slugs (fuzzy match)
- `-g, --group` — repo group from `.buck.yaml`
- `-i, --interactive` — force interactive multi-select
- `--dry-run` — preview without executing

Resolution order: `--interactive` > `--repos` > `--group` > interactive prompt.

## Detailed Command Usage

- Branch & PR creation: `references/branch-pr-commands.md`
- PR management (merge/decline/approve/reviewers/list): `references/pr-management-commands.md`
- Status dashboard & branch cleanup: `references/status-cleanup-commands.md`
