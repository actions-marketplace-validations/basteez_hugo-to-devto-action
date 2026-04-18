# Releasing

Releases are cut by the maintainer via an automated GitHub Actions workflow. There are no manual `git tag` / `git push --tags` / `gh release create` steps.

## Prerequisites (one-time)

- Repository secret `DEVTO_API_KEY_SMOKE` is set to a dev.to API key owned by the maintainer, scoped to a test account.
- Repository has `contents: write` permission available to the `GITHUB_TOKEN` in the release workflow.

## Cutting a release

1. Go to **Actions** → **Release** → **Run workflow**.
2. Fill in:
   - `version` — the target immutable tag, e.g. `v1.0.1` for a non-breaking release.
   - `breaking` — `false` for patch/minor changes on the current major line; `true` for major-bump breaking changes.
   - `release_notes` — optional. If empty, the workflow auto-populates from the commit log since the previous release on the same major line.
3. Click **Run workflow**.

The workflow will:

- Run the full `pytest` suite against the release commit.
- Run a dry-run smoke test against the real dev.to API (using the maintainer's test token; no published post created).
- If both gates pass: create the immutable tag, force-update the moving major tag (non-breaking only), and publish the GitHub Release.

If either gate fails, the workflow aborts. No tag or release is created. Fix the underlying issue in a PR and re-run the release.

## Marketplace publication

The workflow does NOT publish to Marketplace automatically. On the first release (and whenever the Marketplace listing needs refreshing):

1. Open the newly-published GitHub Release on github.com.
2. Tick "Publish this Action to the GitHub Marketplace."
3. Select category (Publishing) and submit for review.

If submission is blocked (name collision, publisher verification), proceed with the repository-only launch path and update the README's Distribution line to "Marketplace publication pending." Track Marketplace as a follow-up.

## Breaking releases

For a major-version bump:

- Cut a release with `version=vN.0.0` and `breaking=true`.
- The prior `vN-1` moving major tag is preserved (not updated).
- A new `vN` moving major tag is created pointing at this release.
- Consumers pinned to `@v<N-1>` are unaffected. Consumers pinned to `@v<N>` (rare at major-bump time) start resolving to the new release immediately.
