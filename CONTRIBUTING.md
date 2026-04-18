# Contributing to Hugo → dev.to Crosspost Action

Thanks for your interest in contributing.

## Ground rules

- **PRs welcome.** No CLA. By submitting a PR you agree your contribution is licensed under the project's MIT license.
- **Scope.** This project is an intentionally small, one-way Hugo → dev.to mirror. Changes that expand scope (two-way sync, non-Hugo support, in-place article edits) are likely to be declined; feel free to open an issue first.
- **Support posture.** Best-effort. Reviews may take time — the maintainer works on this on their own time.

## Development setup

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r crosspost/requirements.txt
pip install pytest responses pip-tools
pytest
```

## Tests

- Every logic change needs `pytest` coverage. See `tests/` for the established fixture patterns (`tmp_git_repo`, `responses` for mocked HTTP).
- Tests MUST NOT make real HTTP requests to dev.to (see `.specify/memory/constitution.md`, Principle IV).

## Changing dependencies

If you modify `crosspost/requirements.txt`, regenerate the lock file in the same PR:

```bash
pip-compile --generate-hashes \
  --output-file crosspost/requirements.lock \
  crosspost/requirements.txt
```

Commit both `requirements.txt` and `requirements.lock`.

## Releases

Releases are automated — see `RELEASING.md`. Contributors do not cut releases.

## Code of conduct

Be kind. This is a small project; bring good faith.
