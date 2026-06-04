# /docs-sync-fix

Interactively fix stale documentation references.

## Usage

```
/docs-sync-fix
/docs-sync-fix --apply
/docs-sync-fix --file README.md
```

## Flags

| Flag | Default | Description |
|------|---------|-------------|
| `--apply` | false | Auto-apply all suggested fixes without prompting (use with care) |
| `--file` | all | Limit fixes to a specific doc file |
| `--dry-run` | false | Show diffs only, write nothing |

## What it does

Invokes `skills/docs-fix` to step through each drift issue interactively.

For each issue:
1. Shows the stale doc section with surrounding context
2. Shows what the code/command/config currently looks like
3. Proposes a corrected doc text
4. Asks: apply / edit manually / skip / ignore

All applied fixes are logged to `docs-sync.log`.

Without `--apply`, nothing is written without explicit per-issue confirmation.

## Example session

```
Issue 1 of 4 — README.md:42

  41: ## Running Tests
  42: npm run test:e2e          ← STALE: script not in package.json
  43:

  Current scripts: test, test:watch, e2e

  Suggested fix: npm run e2e

  [a]pply  [e]dit  [s]kip  [i]gnore  [q]uit > a

  Applied. README.md updated.

Issue 2 of 4 — docs/api.md:87
  ...
```
