# .github

Organization-level shared workflows and configurations for MyFML.

## Shared Workflows

### `deploy-issue-on-direct-push.yml`

Reusable workflow that auto-creates a GitHub Issue when someone pushes directly to `main` without a PR. This ensures all changes have visibility and a deployment record, even when bypassing the PR workflow.

**What it does:**

- Triggers on every push to `main` (called from each repo)
- Checks if the push came from a PR merge (via GitHub API)
- If it's a **direct push**: creates an issue with commit details, changed files, and repo-specific deploy commands
- If it's a **PR merge**: does nothing (the PR is already the record)

**Usage in a repo:**

Create `.github/workflows/deploy-tracker.yml`:

```yaml
name: Track Direct Push Deploys

on:
  push:
    branches: [main]

jobs:
  deploy-tracker:
    uses: myfplminileague/.github/.github/workflows/deploy-issue-on-direct-push.yml@main
    with:
      deploy-commands: |
        ssh user@your-server
        cd /path/to/app
        git pull origin main
        npm run build
        pm2 restart app-name
```

**Labels auto-created:**

- `ready-for-deployment` (green) — signals the issue needs a deploy
- `direct-push` (orange) — distinguishes from regular issues

**Lifecycle:**

1. Senior dev pushes directly to `main`
2. Workflow detects it's not from a PR
3. Issue created with `[Deploy]` prefix and full context
4. Team member deploys and closes the issue
