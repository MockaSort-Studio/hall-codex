---
icon: material/shield-account
---

# Privacy Policy

*Last updated: 2026-03-30*

Hall of Automata is a GitHub App that provides AI agent orchestration for GitHub organisations. This policy describes what data the App accesses, how it is used, and how it is protected.

---

## What data the App accesses

The App requests the following GitHub permissions:

| Permission | Scope | Why |
|------------|-------|-----|
| Issues | Read & Write | Receive issue events; post status comments; apply labels |
| Pull requests | Read & Write | Open PRs on behalf of automata; post review responses |
| Contents | Read & Write | Read repo files; push branches |
| Actions | Read & Write | Trigger and monitor workflow runs |
| Members | Read | Verify team membership before authorising an invocation |
| Administration | Write | Create the `hall-of-automata` repo and `automata-invokers` team on first install |
| Organisation secrets | Write | Seed `APP_ID` and `APP_PRIVATE_KEY` as org-level secrets scoped to `hall-of-automata` |
| Metadata | Read | Required by GitHub for all Apps |

The App receives webhook events for: Issues, Issue comments, Branch or tag creation, Installation, and Repository creation.

---

## What data is collected and stored

**The App collects no user data.** Specifically:

- No personal information (names, emails, account details) is stored anywhere outside GitHub
- Webhook payloads are processed in memory and immediately discarded
- The relay server (which routes webhook events) logs only pseudonymised identifiers — org and repo names are replaced with short deterministic hashes before writing to any log
- No analytics, tracking, or telemetry of any kind is performed

The only persistent state the App creates is within your own GitHub organisation:

- The `hall-of-automata` repository (created in your org on install)
- GitHub Actions workflow run logs and artifacts (stored in your org's GitHub account, subject to GitHub's own retention policies)
- Organisation-level secrets `APP_ID` and `APP_PRIVATE_KEY` (scoped exclusively to `hall-of-automata`)

---

## Claude API usage

Hall of Automata agents run using **your invokers' own Claude Pro or Max accounts**. The App does not hold any Anthropic API keys. OAuth tokens belong to the individuals who register as invokers and are stored in your org's GitHub Environments — they never leave GitHub's infrastructure.

Any data sent to the Claude API is subject to [Anthropic's Privacy Policy](https://www.anthropic.com/legal/privacy).

---

## Data sharing

No data is sold, rented, or shared with third parties. The relay server is operated by MockaSort Studio solely to route GitHub webhook events to your org's Hall instance.

---

## Data retention

The App holds no database. There is nothing to retain or delete. Uninstalling the App removes its access to your organisation immediately. GitHub workflow logs and artifacts follow GitHub's standard retention policies (90 days by default, configurable by your org).

---

## Security

See the [Security page](security.md) for the full threat model and hardening measures.

For security disclosures, contact **mockasortstudio@gmail.com**.

---

## Changes to this policy

If this policy changes materially, the updated version will be published here with a revised date. Continued use of the App constitutes acceptance of the updated policy.

---

## Contact

MockaSort Studio · [github.com/MockaSort-Studio](https://github.com/MockaSort-Studio) · mockasortstudio@gmail.com
