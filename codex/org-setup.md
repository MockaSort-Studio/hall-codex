---
icon: material/office-building-cog
---

# Org-Wide Setup

Installing the Hall App provisions your org automatically. This page documents what the relay sets up on your behalf and what requires manual action.

---

## What App installation does automatically

When you install the Hall GitHub App on your org, the relay:

1. Creates `hall-of-automata` from the operator template
2. Creates the `automata-invokers` team
3. Creates the `.github` repo with the Automaton Task issue template
4. Seeds all Hall labels in `hall-of-automata`
5. Seeds `APP_ID` and `APP_PRIVATE_KEY` as org-level secrets scoped to `hall-of-automata`
6. Opens a welcome issue explaining next steps

Nothing else is required to make the Hall functional in your org.

---

## What requires manual action

### 1. Add an invoker

Dispatch cannot run without at least one registered invoker donating Claude quota. Follow the [Invoker Onboarding](invoker-onboarding.md) process — it walks through generating the OAuth token and registering it.

### 2. Seed labels in target repos (optional)

Hall labels are seeded in `hall-of-automata` automatically. If you want to invoke automata from other repos in your org, those repos need the labels too. Use the `gh` CLI:

```bash
# For each target repo
gh label create "hall:dispatch-automaton" --color "5319e7" --description "Routes to old-major for triage" --repo MockaSort-Studio/my-repo
```

Or copy the full label set from `hall-of-automata` to the target repo using the [`gh label clone`](https://cli.github.com/manual/gh_label_clone) command:

```bash
gh label clone MockaSort-Studio/hall-of-automata --repo MockaSort-Studio/my-repo
```

---

## Hall labels reference

| Label | Color | Purpose |
|---|---|---|
| `hall:dispatch-automaton` | purple | Standard invocation — routes to Old Major |
| `hall:old-major` | purple | Thread bound to Old Major |
| `hall:hamlet` | purple | Thread bound to Hamlet |
| `hall:mergio` | purple | Thread bound to mergio |
| `hall:pyrate` | purple | Thread bound to Captain Pyrate |
| `hall:aeeeiii` | purple | Thread bound to aeeeiii |
| `hall:frontenzo` | purple | Thread bound to Frontenzo |
| `hall:tomashco` | purple | Thread bound to Tomashco |
| `hall:awaiting-input` | yellow | Agent waiting for invoker reply |
| `hall:queued` | red | All invoker quota exhausted |
| `hall:invoker-queued` | red | No invoker available |
| `hall:active-invoker` | green | Registered active invoker |
| `hall:onboard-invoker` | violet | Invoker onboarding in progress |
| `hall:onboard-automaton` | violet | Automaton onboarding in progress |
| `hall:post-mortem` | dark red | Trigger post-mortem analysis by Old Major |

---

## Checklist — after App installation

- [ ] Welcome issue received in `hall-of-automata`
- [ ] At least one invoker registered ([Invoker Onboarding](invoker-onboarding.md))
- [ ] Labels present in any target repos you want to dispatch from
