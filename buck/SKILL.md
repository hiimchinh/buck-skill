---
name: buck
description: Multi-repo orchestration for Bitbucket Cloud. Create branches, PRs, merge/decline/approve PRs, status dashboard, branch cleanup across repos.
version: 1.0.1
license: MIT
---

# Buck — Multi-Repo Orchestration CLI

Bitbucket Cloud CLI for managing branches and PRs across multiple repositories simultaneously.

## ⚠️ SECURITY — read before any buck operation

**NEVER cat / display `~/.buck.yaml` or `~/.config/buck/env` without redaction.**

These files may contain a Bitbucket API token. If a user asks to inspect them, always redact:

```bash
# Safe pattern for showing config:
grep -vE '^\s*(token|TOKEN)' ~/.buck.yaml
sed -E 's/(token: ).*/\1<REDACTED>/' ~/.buck.yaml
```

If a token is ever displayed in a transcript, **immediately tell the user to revoke it** at https://id.atlassian.com/manage-profile/security/api-tokens.

## Auto-migration (run automatically on first invocation per host)

Before invoking any buck command, check if `~/.buck.yaml` stores the API token in plaintext. If so, transparently migrate to a separate env file. The user takes no action — buck keeps working and the token leaves the YAML.

### Detection

```bash
test -f ~/.buck.yaml && grep -qE '^[[:space:]]*token:[[:space:]]+[^$[:space:]]' ~/.buck.yaml && echo NEEDS_MIGRATION
```

If output is `NEEDS_MIGRATION`, run the migration steps below before any buck command. Skip if yaml already uses `${BITBUCKET_API_TOKEN}`.

### Migration steps (idempotent)

1. **Backup yaml**:
   ```bash
   cp ~/.buck.yaml ~/.buck.yaml.bak.$(date +%Y%m%d-%H%M%S)
   ```
2. **Extract email + token** (preserve special chars):
   ```bash
   EMAIL=$(awk '/^[[:space:]]*email:/ {sub(/^[[:space:]]*email:[[:space:]]*/, ""); gsub(/^["'\'']|["'\'']$/, ""); print; exit}' ~/.buck.yaml)
   TOKEN=$(awk '/^[[:space:]]*token:/ {sub(/^[[:space:]]*token:[[:space:]]*/, ""); gsub(/^["'\'']|["'\'']$/, ""); print; exit}' ~/.buck.yaml)
   ```
3. **Create secure env file** (700 dir, 600 file):
   ```bash
   mkdir -p ~/.config/buck && chmod 700 ~/.config/buck
   umask 077
   cat > ~/.config/buck/env <<EOF
   # Buck (Bitbucket multi-repo CLI) credentials.
   # Loaded by shell rc; referenced by ~/.buck.yaml via \${BITBUCKET_*}.
   # chmod 600 — never commit, never cat without redaction.
   #
   # Token: https://id.atlassian.com/manage-profile/security/api-tokens
   # Required scopes: read:repository:bitbucket, write:repository:bitbucket
   export BITBUCKET_EMAIL="$EMAIL"
   export BITBUCKET_API_TOKEN="$TOKEN"
   EOF
   chmod 600 ~/.config/buck/env
   ```
4. **Rewrite yaml** to use env vars (macOS sed; on Linux drop `''` after `-i`):
   ```bash
   sed -E -i '' \
     -e "s|^([[:space:]]*email:).*|\1 \${BITBUCKET_EMAIL}|" \
     -e "s|^([[:space:]]*token:).*|\1 \${BITBUCKET_API_TOKEN}|" \
     ~/.buck.yaml
   ```
5. **Append source line** to shell rc if missing (zsh + bash):
   ```bash
   for RC in ~/.zshrc ~/.bashrc; do
     [[ -f "$RC" ]] || continue
     grep -q "config/buck/env" "$RC" || cat >> "$RC" <<'RCEOF'

   # Buck (Bitbucket multi-repo CLI) — token loaded from secure env file
   [[ -f ~/.config/buck/env ]] && source ~/.config/buck/env
   RCEOF
   done
   ```
6. **Export env in current bash session** so the next buck command works:
   ```bash
   export BITBUCKET_EMAIL="$EMAIL"
   export BITBUCKET_API_TOKEN="$TOKEN"
   ```
7. **Notify user** (concise, do not embellish):
   > Migrated buck token to `~/.config/buck/env` (chmod 600). Old YAML backed up at `~/.buck.yaml.bak.<timestamp>`. Recommended (not required): revoke the old token at https://id.atlassian.com/manage-profile/security/api-tokens and paste a new one into the env file — the plaintext may already exist in backups (Time Machine, Dropbox...). Reload your shell or run `source ~/.config/buck/env`.

## Install

```bash
brew install hiimchinh/tap/buck
# Or: go install github.com/hiimchinh/buck@latest
```

## Setup

1. Create config: `buck setup` (interactive) or manually create `.buck.yaml`
2. Auth: API token (default) or OAuth (`buck login`)
3. **Store token in env file, not yaml** — put it in `~/.config/buck/env` (chmod 600), reference from yaml as `${BITBUCKET_API_TOKEN}`. See "Auto-migration" above for exact layout.

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
