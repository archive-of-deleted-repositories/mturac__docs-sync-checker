# docs-sync-checker

Claude Code plugin that detects documentation drift — when docs describe functions, CLI commands, file paths, or config keys that no longer exist or have changed.

## Install

```bash
claude plugin install docs-sync-checker
```

## Commands

| Command | Description |
|---------|-------------|
| `/docs-sync` | Scan all docs for drift |
| `/docs-sync-scan [file]` | Check a specific doc file |
| `/docs-sync-fix` | Fix stale references interactively |
| `/docs-coverage` | Find undocumented public API functions |

## Example Drift Report

```
Documentation Drift Report

4 issues found:

README.md:42       STALE    `npm run test:e2e`
                            Script removed from package.json
                            Current: npm run e2e

docs/api.md:87     CHANGED  `calculateTotal(items)`
                            Signature changed at src/cart/service.ts:34
                            Current: calculateTotal(cart: Cart): number

docs/config.md:113 STALE    `database.poolSize`
                            Key renamed to db.pool in config schema

README.md:61       STALE    `src/utils/shared.ts`
                            File moved to lib/shared.ts

Run /docs-sync-fix to resolve interactively.
```

## Skills

- `docs-scan` — extracts all code references from docs, verifies each exists in the codebase
- `docs-fix` — interactive fixer with diff preview and confirmation before writing; logs all changes to `docs-sync.log`
- `docs-coverage` — finds undocumented public exports, ranked by how many times they are called in the codebase
