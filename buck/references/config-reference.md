# Buck Configuration Reference

## Config File: `.buck.yaml`

Searched in CWD first, then `$HOME`. Supports `${ENV_VAR}` expansion.

## Minimal Config

```yaml
workspace: my-workspace

api_token:
  email: ${BITBUCKET_EMAIL}
  token: ${BITBUCKET_API_TOKEN}
```

## Full Config

```yaml
workspace: my-workspace

# Auth method: "api_token" (default) or "oauth"
# auth:
#   method: oauth

# Option 1: API Token (default, no login needed)
# Create at: Bitbucket > Personal settings > Security > API tokens
# Scopes: read:repository:bitbucket, write:repository:bitbucket
api_token:
  email: ${BITBUCKET_EMAIL}
  token: ${BITBUCKET_API_TOKEN}

# Option 2: OAuth 2.0 + PKCE (requires 'buck login')
# oauth:
#   client_id: ${BITBUCKET_OAUTH_CLIENT_ID}
#   client_secret: ${BITBUCKET_OAUTH_CLIENT_SECRET}

# Repo groups for batch operations
groups:
  backend:
    - repo-api
    - repo-worker
  frontend:
    - repo-web
    - repo-mobile

# Default values
defaults:
  source_branch: master
  branch_prefix: "feature/"
```

## Auth Methods

| Method | Config | Setup | Token Location |
|--------|--------|-------|----------------|
| API Token | `api_token.email` + `api_token.token` | `buck setup` | `.buck.yaml` |
| OAuth 2.0 | `oauth.client_id` + `oauth.client_secret` | `buck login` | `~/.buck/token.json` |

API token = Basic auth (default). OAuth = Bearer token with auto-refresh.

## Groups

Define repo groups for batch targeting:

```yaml
groups:
  backend:
    - repo-api
    - repo-worker
  frontend:
    - repo-web
```

Use with `--group backend` on any repo-targeting command.

## Shell Completion

```bash
buck completion bash|zsh|fish|powershell
# Add to shell profile for persistent completion
```
