# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) preset for every Rackbops-owned repo that
runs Renovate. **Owned here, consumed by `extends`** -- the same "one standard, several
consumers" shape as `labels.json` / `discord_routing.json` in
[`Rackbops/Tooling`](https://github.com/Rackbops/Tooling#standardized-issue-labels).

## Consuming this preset

A repo's `renovate.json` is one line:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>Rackbops/renovate-config"]
}
```

**Changing `default.json` here changes every consumer on Renovate's next run** -- there is
no per-repo copy to drift, and no version pinning on the extends reference. Treat a change
here the same way a change to `labels.json` is treated in Tooling: it is a live, shared
contract, not a one-repo tweak.

## Policy

- `dependencyDashboardApproval: true` -- nothing becomes a PR until a human ticks the
  checkbox on the repo's Dependency Dashboard issue.
- `automerge: false` -- Renovate never merges anything itself.
- GitHub Actions bumps group into one PR; Docker (Dockerfile + compose) bumps group into
  another.
- `customManagers` cover `node-version:` / `bun-version:` literal pins in workflow YAML,
  which no built-in manager reaches. **Verified 2026-09-07 against a real PR** (Tooling#423,
  `rackbops-discord-bot` PR #145): Renovate's own `bun` manager already moves a
  `"packageManager": "bun@x.y.z"` field in `package.json` in the same PR as the dependency
  bump, so no separate regex rule is needed for that pin -- one was tried and dropped.

## Status

Piloted on `rackbops-discord-bot` only
([Tooling#423](https://github.com/Rackbops/Tooling/issues/423)). Org-wide rollout is
[Tooling#428](https://github.com/Rackbops/Tooling/issues/428), gated on this pilot's
2-week noise measurement. Design:
[`research/software-inventory-and-update-surfacing.md`](https://github.com/Rackbops/Tooling/blob/main/research/software-inventory-and-update-surfacing.md)
in Tooling.

## No CI runner here

This repo is public and carries no self-hosted GitHub Actions runner, and never should --
if a validator workflow is ever added here, it must run on `ubuntu-latest`.
