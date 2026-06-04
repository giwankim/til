# Contributing

This repository uses [pre-commit](https://pre-commit.com/) to keep its
Markdown notes clean. The hooks run automatically before each commit and only
inspect staged `.md` files.

## What the hooks do

Configured in [`.pre-commit-config.yaml`](.pre-commit-config.yaml):

| Hook | Purpose |
| ---- | ------- |
| `end-of-file-fixer` | Ensures every file ends with exactly one newline |
| `trailing-whitespace` | Strips trailing spaces |
| `markdownlint` | Lints Markdown style (rules in [`.markdownlint.yaml`](.markdownlint.yaml)) |
| `typos` | Catches common spelling mistakes |

## One-time setup

`pre-commit` is a separate tool from this repo, and the git hook it installs
lives in `.git/hooks/` — which is **not** tracked by git. So each person who
clones this repository must do this once:

1. Install the `pre-commit` tool (only needed once per machine):

   ```bash
   # macOS (Homebrew)
   brew install pre-commit

   # or via pip
   pip install pre-commit
   ```

2. Install the git hook into this clone:

   ```bash
   pre-commit install
   ```

After this, the hooks run automatically on every `git commit`.

## Everyday use

- **On commit:** the hooks run against your staged `.md` files. If a hook
  modifies a file (for example, fixing trailing whitespace) or reports an
  error, the commit is aborted. Review the changes, `git add` them, and commit
  again.
- **Run manually against everything:**

  ```bash
  pre-commit run --all-files
  ```

- **Run manually against only staged files:**

  ```bash
  pre-commit run
  ```

## Updating hook versions

Hook versions are pinned with immutable git tags (the `rev:` fields in
`.pre-commit-config.yaml`) so that everyone runs identical tooling. To bump
them all to their latest releases:

```bash
pre-commit autoupdate
```

Commit the resulting change to `.pre-commit-config.yaml`.

## Bypassing the hooks

When you genuinely need to skip the checks for a single commit:

```bash
git commit --no-verify
```

Use this sparingly — the hooks exist to keep the notes consistent.
