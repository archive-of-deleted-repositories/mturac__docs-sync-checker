# /docs-coverage

Find public exported functions that have no documentation.

## Usage

```
/docs-coverage
/docs-coverage --lang ts
/docs-coverage --min-callers 3
```

## Flags

| Flag | Default | Description |
|------|---------|-------------|
| `--lang` | auto | Filter to a specific language: `ts`, `js`, `py`, `go`, `rs` |
| `--min-callers` | `1` | Only show undocumented functions called at least N times |
| `--format` | `table` | Output format: `table`, `json` |

## What it does

Invokes `skills/docs-coverage` to:
1. Discover all public exported functions across the codebase
2. Check each for JSDoc/TSDoc comment OR mention in any `.md` file
3. Count how many times each undocumented function is called
4. Rank by caller count (most-used = highest priority to document)

## Example output

```
Documentation Coverage: 31/48 (64.6%)

Undocumented functions — sorted by priority:

HIGH (10+ callers):
  formatCurrency      src/utils/format.ts:12     23 callers
  useAuthContext      src/hooks/auth.tsx:45       18 callers
  validateEmail       src/validators/email.ts:8   14 callers

MEDIUM (3–9 callers):
  parseQueryString    src/utils/url.ts:34          7 callers
  buildApiUrl         src/api/client.ts:56          5 callers

LOW (<3 callers):
  normalizeSlug       src/utils/string.ts:21        2 callers

Document HIGH priority functions first.
```
