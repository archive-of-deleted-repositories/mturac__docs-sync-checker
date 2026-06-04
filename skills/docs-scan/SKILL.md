# docs-scan

Detect documentation drift — references in docs to code, commands, or config that no longer exists or has changed.

## Steps

### 1. Find Documentation Files

Collect all doc sources:
- `README.md`, `CHANGELOG.md`, `CONTRIBUTING.md` in project root
- `docs/**/*.md` recursively
- `*.md` in project root
- JSDoc / TSDoc comments in source files (`/** ... */` blocks)
- OpenAPI/Swagger YAML/JSON if present

### 2. Extract Code References from Docs

For each doc file, extract:

**Function/method names** — identifiers in backtick code spans:
```
`calculateTotal()`, `useUserData`, `AuthProvider`
```

**File paths** — path-like strings in code spans or code blocks:
```
`src/utils/shared.ts`, `config/database.yml`
```

**CLI commands** — lines in fenced code blocks with `bash`, `sh`, or `shell` lang tag:
```bash
npm run build
pnpm test:e2e
```

**Config keys** — YAML/JSON keys described in prose:
```
Set `database.poolSize` to configure connection pooling.
```

**Import statements** in code blocks:
```ts
import { calculateTotal } from './cart/service'
```

### 3. Verify Each Reference

For each extracted reference:

| Reference type | Verification method |
|---|---|
| Function name | Search codebase for `function <name>`, `const <name> =`, `def <name>`, class method |
| File path | `fs.existsSync(path)` or equivalent |
| CLI command (`npm run X`) | Check `package.json` scripts object |
| CLI command (binary) | Check `node_modules/.bin/` or system PATH |
| Config key | Parse config schema files (`*.schema.json`, Zod schemas, type definitions) |
| Import path | Resolve from doc location; check if file/package exists |

### 4. Detect Broken Code Examples

For code blocks containing:
- Import statements: resolve the import path — does it exist?
- Function calls: does the function exist with the documented signature?
- Constructor calls: does the class still accept the documented arguments?

Flag signature drift: function exists but arguments differ from what's documented.

### 5. Detect Stale CLI Examples

```
for each `npm run X` or `pnpm X` in docs:
  check package.json scripts
  if script "X" not found: STALE
  if script exists but command differs: CHANGED
```

### 6. Detect Stale Config Keys

```
for each config key described in docs:
  search for key in: *.config.ts, *.config.js, schema files, type definitions
  if key not found anywhere: STALE
  if key found but type changed: CHANGED
```

### 7. Output — Drift Table

```markdown
## Documentation Drift Report

| Doc File           | Line | Stale Reference         | Status  | Current State              |
|--------------------|------|-------------------------|---------|----------------------------|
| README.md          |   42 | `npm run test:e2e`      | STALE   | Script removed from pkg.json |
| docs/api.md        |   87 | `calculateTotal(items)` | CHANGED | Now: `calculateTotal(cart)` |
| docs/config.md     |  113 | `database.poolSize`     | STALE   | Key renamed to `db.pool`   |
| README.md          |   61 | `src/utils/shared.ts`   | STALE   | File moved to `lib/shared` |

4 drift issues found. Run `/docs-sync-fix` to resolve.
```
