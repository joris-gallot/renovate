# renovate

Self-hosted Renovate for every repo of the `joris-gallot` account, run from GitHub Actions.

## How it works

- `.github/workflows/renovate.yml` runs every 6 hours (also manual). It authenticates as the `joris-gallot-renovate` GitHub App, installed on all repos of the account (secrets `RENOVATE_BOT_APP_CLIENT_ID` and `RENOVATE_BOT_APP_PRIVATE_KEY`), so pull requests come from the app's bot user and trigger CI.
- Renovate autodiscovers `joris-gallot/*` (forks and archived repos are skipped) but is **opt-in**: a repo is processed only if it has a Renovate config (`renovate.json`, `.github/renovate.json`...). No onboarding PR is ever opened.
- `default.json` is the shared preset. A repo opts in with:

  ```json
  {
    "$schema": "https://docs.renovatebot.com/renovate-schema.json",
    "extends": ["github>joris-gallot/renovate"]
  }
  ```

  and adds its own `packageRules` there when needed.
- The Mend-hosted Renovate app must stay off for these repos: both would push to the same `renovate/*` branches.
- Repos in the `Kinora-dev` and `reviu-dev` orgs have their own per-repo workflow and are not covered here.

## Debugging

- `gh run list -R joris-gallot/renovate -w Renovate` then `gh run view <id> -R joris-gallot/renovate --log` (or `--log-failed`).
- Rerun on one repo with debug logs and no changes:
  `gh workflow run Renovate -R joris-gallot/renovate -f repository=<name> -f logLevel=debug -f dryRun=true`
- A repo missing from the logs has no Renovate config, or the app is not installed on it.
