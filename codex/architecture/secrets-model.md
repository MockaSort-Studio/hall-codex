---
icon: material/lock
---

# Secrets Model

## What lives where

| Asset | Location | Type | Purpose |
|-------|----------|------|---------|
| `APP_ID` | Hall repo secret | Public identifier | GitHub App identity; used to mint installation tokens |
| `APP_PRIVATE_KEY` | Hall repo secret | Signs JWT requests | Generates GitHub App installation tokens |
| `CLAUDE_CODE_OAUTH_TOKEN` | `invoker/<handle>` Environment secret | Claude Pro/Max OAuth token | Credential billed to the invoker's subscription; pool-selected at dispatch time |
| `HALL_USAGE_COUNT` | `invoker/<handle>` Environment variable | Integer | Weekly invocation count for this invoker |
| `HALL_WEEKLY_CAP` | `invoker/<handle>` Environment variable | Integer | Configured weekly cap for this invoker |

---

## GitHub App secrets (`APP_ID`, `APP_PRIVATE_KEY`)

Stored as **org-level secrets** scoped to the Hall repo only (set by the relay during App installation). Used by `actions/create-github-app-token@v1` at the start of each dispatch job to mint a short-lived installation token scoped to the target repo owner.

**What the installation token can do:** whatever permissions the App was granted at install time (Contents, Issues, Pull Requests R/W; Members R; Environments R/W for counter updates).
**Lifetime:** 1 hour. Never stored; minted fresh per job.

If `APP_PRIVATE_KEY` leaked, an attacker could mint installation tokens. Mitigation: immediate key rotation in the App settings (Settings → Private keys → Revoke).

---

## Invoker pool OAuth tokens (`CLAUDE_CODE_OAUTH_TOKEN`)

Contributors who donate quota register as invokers via the onboarding issue template. Each invoker runs `claude setup-token` on their machine, authenticates via their Claude Pro/Max subscription, and stores the resulting OAuth token as a secret in their `invoker/<handle>` GitHub Environment.

**Pool selection:** at dispatch time, `scripts/detect-invoke-context.js` queries all `invoker/*` environments, filters out members at or over cap, sorts by `HALL_USAGE_COUNT` ascending, and selects the least-used member. The selected invoker's environment is declared on the dispatch job.

**Isolation:** GitHub Environments allow environment-level secrets that are only accessible when the job explicitly declares `environment: invoker/<handle>`. No job can access another invoker's token unless it targets that environment.

**Billing:** consumption is billed against the invoker's Claude subscription, not a shared API key. There is no Anthropic API key.

**Rotation:** if a token is compromised, the invoker runs `claude setup-token` again and updates the Environment secret. The old token is revoked automatically when the new one is issued.

---

## Invoker usage variables (`HALL_USAGE_COUNT`, `HALL_WEEKLY_CAP`)

Stored as **environment variables** (not secrets) on the `invoker/<handle>` Environment. Variables are readable and writable via the GitHub Environments API.

`HALL_USAGE_COUNT` is incremented after each successful dispatch using the Environments API (via the App's installation token). `HALL_WEEKLY_CAP` is set once during invoker onboarding and updated manually when the cap needs adjustment.

**Weekly reset:** `weekly-reset.yml` runs every Monday at 00:00 UTC and zeroes `HALL_USAGE_COUNT` across all invoker environments.

**Pool exhaustion:** if all invokers are at cap, the `notify-queued` job posts a comment on the issue and applies the `hall:invoker-queued` label. The task waits for the next weekly reset.

---

## Agent catalog (`agents.yml`)

The live agent registry is the `agents.yml` file in the Hall repo. It is checked out as part of every dispatch workflow run. Old Major reads from and writes to it directly during automaton onboarding. No external store or deployment payload is involved.

---

## Agent personas (`roster/*.md`)

Each agent's character sheet lives as a Markdown file in the `roster/` directory of the Hall repo. The dispatch workflow writes a two-line `CLAUDE.md` using `@`-imports pointing at `automaton_base.md` and the agent's persona — Claude Code resolves them at runtime. Personas are version-controlled alongside the rest of the codebase.

---

## Token masking

The dispatch action masks the OAuth token immediately upon access:

```yaml
- name: Mask OAuth token
  run: echo "::add-mask::${{ inputs.oauth-token }}"
```

This prevents the token value from appearing in any subsequent log output, including debug mode.

---

## What GitHub does not hold

- No Anthropic API keys
- No billing credentials
- No invoker personal passwords or SSH keys

---

## Token management procedures

**OAuth token rotation:** run `claude setup-token` again, then update the `CLAUDE_CODE_OAUTH_TOKEN` secret in your `invoker/<handle>` environment. The old token is revoked when the new one is issued.

**App key rotation:** generate a new private key in the GitHub App settings (Settings → Developer settings → GitHub Apps → hall-of-automata → Private keys), update `APP_PRIVATE_KEY` in the org secrets, and delete the old key. Revocation is immediate.
