# /docs-sync

Scan the entire project for documentation drift.

## Usage

```
/docs-sync
/docs-sync --strict
/docs-sync --ignore docs/legacy/**
```

## Flags

| Flag | Default | Description |
|------|---------|-------------|
| `--strict` | false | Treat CHANGED references (signature drift) as errors, not warnings |
| `--ignore` | — | Glob pattern of doc files to skip |
| `--format` | `table` | Output format: `table`, `json`, `markdown` |

## What it does

Invokes `skills/docs-scan` across all documentation files in the project.

Checks all:
- Code references in backtick spans
- File paths mentioned in docs
- CLI commands in code blocks
- Config keys described in prose
- Import statements in code examples

Outputs a drift table sorted by severity (STALE before CHANGED). Exits non-zero if any STALE issues found (useful in CI).

## Example output

```
Documentation Drift Report

4 issues found:

README.md:42   STALE    npm run test:e2e  — script removed
docs/api.md:87 CHANGED  calculateTotal(items)  — signature changed to calculateTotal(cart)
docs/config.md:113 STALE database.poolSize  — key renamed to db.pool
README.md:61   STALE    src/utils/shared.ts  — file moved to lib/shared

Run /docs-sync-fix to resolve interactively.
```
