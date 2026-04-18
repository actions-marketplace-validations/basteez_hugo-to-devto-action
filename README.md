# Hugo → dev.to Crosspost Action

> **Distribution**: Available via repository reference `basteez/hugo-to-devto-action`. [Available on Marketplace](https://github.com/marketplace/actions/hugo-to-dev-to-crosspost).

A composite GitHub Action that mirrors new Hugo blog posts to [dev.to](https://dev.to) as drafts. On each push, it inspects the push range with `git diff`, parses Hugo frontmatter for posts under your configured post directory, and creates dev.to drafts for posts that have `draft: false` and do not already exist on dev.to (deduplicated by title). Intended for Hugo-based blogs that want a low-friction, one-way mirror of new content to dev.to.

## Quick start

```yaml
name: Crosspost to dev.to

on:
  push:
    branches: [main]

permissions:
  contents: read

jobs:
  crosspost:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: basteez/hugo-to-devto-action@v1
        with:
          devto-api-key: ${{ secrets.DEVTO_API_KEY }}
          before: ${{ github.event.before }}
          after: ${{ github.event.after }}
```

> **Important:** `actions/checkout@v4` must be invoked with `fetch-depth: 0`. The action runs `git diff` across the `before..after` push range, and the default shallow checkout (`fetch-depth: 1`) will not contain the `before` SHA on any non-trivial push.

## Inputs

| Name | Required | Default | Description |
|---|---|---|---|
| `devto-api-key` | yes | — | dev.to Personal API Key. Pass via `${{ secrets.DEVTO_API_KEY }}`. |
| `before` | yes | — | "Before" SHA of the push range. Typically `${{ github.event.before }}`. |
| `after` | yes | — | "After" SHA of the push range. Typically `${{ github.event.after }}`. |
| `post-dir` | no | `content/post` | Directory (relative to the consumer repo root) that contains Hugo posts. |
| `dry-run` | no | `'false'` | When set to the literal string `'true'`, the action simulates crossposting and logs what would happen (no POST to dev.to). |

## Required secrets

Set `DEVTO_API_KEY` as a repository secret. The value is your dev.to Personal API Key (Settings → Extensions → DEV Community API Keys). The action reads it via `${{ secrets.DEVTO_API_KEY }}` and forwards it into the child process; it is never logged.

## Versioning

Three pin styles are supported, ordered from most convenient to strongest reproducibility:

- **`@v1`** (recommended) — a moving tag that always points at the latest `v1.x.y`. The maintainer force-updates `v1` on every non-breaking release; consumers get bug fixes and additive features automatically.
- **`@v1.x.y`** (opt-in immutable) — a full-semver pin that never changes. Use this when reproducibility matters more than automatic updates.
- **`@<commit-sha>`** (strongest reproducibility) — pin to the 40-character commit SHA. The resolved commit never changes for the lifetime of the ref. Use this when your organisation requires commit-level pinning.

Breaking changes always bump the major tag (`@v2`, `@v3`, …). The `v1` moving tag will **never** be force-updated to a breaking release — consumers pinned to `@v1` are protected from incompatible changes by construction.

## Support

Best-effort, no SLA. Maintained by a single author (Tiziano Basile). Please file bugs and feature requests via [GitHub Issues](https://github.com/basteez/hugo-to-devto-action/issues). For security concerns, see [SECURITY.md](./SECURITY.md).

## Contributing

Pull requests are welcome. See [CONTRIBUTING.md](./CONTRIBUTING.md) for guidelines. By submitting a PR, you agree your contribution is licensed under the project's MIT license.

## License

MIT. © 2026 Tiziano Basile. See [LICENSE](./LICENSE).
