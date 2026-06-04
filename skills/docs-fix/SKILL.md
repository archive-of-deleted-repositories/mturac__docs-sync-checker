# docs-fix

Interactively fix stale documentation references identified by `docs-scan`.

## Steps

### 1. Load Drift Results

Read drift data from:
- Output of most recent `docs-scan` run (stored in `.docs-sync-cache.json` if present)
- Or run `docs-scan` first and proceed with results

Process issues one at a time, highest-severity first.

### 2. For Each Stale Reference — Show Context

Display:
```
File: README.md, line 42
──────────────────────────────────────────────────────
40: ## Running Tests
41:
42: ```bash
43: npm run test:e2e
44: ```
──────────────────────────────────────────────────────
Status: STALE — `test:e2e` script no longer exists in package.json

Current package.json scripts:
  test        → "vitest run"
  test:watch  → "vitest"
  e2e         → "playwright test"
──────────────────────────────────────────────────────
Suggested fix:
  Replace `npm run test:e2e` with `npm run e2e`
```

### 3. Present Options

For each issue ask:
```
[a] Apply suggested fix
[e] Edit manually (open in $EDITOR)
[s] Skip this issue
[i] Mark as intentional (suppress in future scans)
[q] Quit and save progress
```

Do not auto-apply any fix without explicit `[a]` or `--apply` flag confirmation.

### 4. Apply Fix

If user chooses `[a]`:
- Read the doc file
- Replace the exact stale text with the suggested fix
- Preserve surrounding formatting (indentation, blank lines, list markers)
- Show a unified diff before writing:
  ```diff
  -npm run test:e2e
  +npm run e2e
  ```
- Write file only after confirming diff is correct

### 5. Track Updates in `docs-sync.log`

Append each fix to `docs-sync.log` in the project root:

```
2024-01-15T10:30:00Z  FIXED   README.md:42  npm run test:e2e → npm run e2e
2024-01-15T10:31:00Z  SKIPPED docs/api.md:87  calculateTotal signature — deferred
2024-01-15T10:31:30Z  IGNORED docs/config.md:113  database.poolSize — intentional legacy docs
```

### 6. Summary

After processing all issues:
```
docs-sync-fix complete

Fixed:    2 issues
Skipped:  1 issue
Ignored:  1 issue

Updated files:
  README.md (1 change)
  docs/api.md (1 change)

Run /docs-sync to verify no drift remains.
```
