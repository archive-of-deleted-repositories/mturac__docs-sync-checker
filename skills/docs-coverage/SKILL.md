# docs-coverage

Find public API functions and exports that have no documentation.

## Steps

### 1. Find All Public Exported Functions

Scan source files by language:

**TypeScript / JavaScript:**
```
export function X(...)
export const X = (...)
export class X { ... }
export { X } from '...'
export default function X(...)
```
Exclude: functions prefixed with `_`, marked `@internal`, or in `*.test.*` / `*.spec.*` files.

**Python:**
```
def X(...):   (at module level, not prefixed with _)
class X:      (at module level)
```
Exclude: `__dunder__` methods, `_private`, files in `tests/`.

**Go:**
```
func X(...)   (capital first letter = exported)
type X struct
```

**Rust:**
```
pub fn X(...)
pub struct X
pub trait X
```

For each discovered export, record:
- Name
- File path + line number
- Signature (parameter names and types if available)
- Language

### 2. Check for Existing Documentation

For each exported function, check:

**JSDoc / TSDoc present in source:**
```ts
/**
 * Calculates the total price of all cart items.
 */
export function calculateTotal(cart: Cart): number
```
→ DOCUMENTED

**Mentioned in any `.md` file:**
```
grep -r "calculateTotal" docs/ README.md *.md
```
→ DOCUMENTED (at least referenced)

**No JSDoc AND not in any .md:** → UNDOCUMENTED

### 3. Rank by Usage Frequency

For each undocumented function, count how many times it is called/imported across the codebase:

```
usage_count = grep -r "functionName" src/ --include="*.ts" | wc -l
```

Higher usage = higher documentation priority. Functions called in many places but undocumented are the biggest risk.

### 4. Output

```markdown
## Documentation Coverage Report

Public exported functions: 48
Documented (JSDoc or .md mention): 31
Undocumented: 17
Coverage: 64.6%

### Undocumented Functions — by Priority

| Function              | File                        | Line | Callers | Priority |
|-----------------------|-----------------------------|------|---------|----------|
| `formatCurrency`      | src/utils/format.ts         |   12 |      23 | HIGH     |
| `useAuthContext`      | src/hooks/auth.tsx          |   45 |      18 | HIGH     |
| `validateEmail`       | src/validators/email.ts     |    8 |      14 | HIGH     |
| `parseQueryString`    | src/utils/url.ts            |   34 |       7 | MEDIUM   |
| `buildApiUrl`         | src/api/client.ts           |   56 |       5 | MEDIUM   |
| `normalizeSlug`       | src/utils/string.ts         |   21 |       2 | LOW      |

Document the HIGH priority functions first — they are called in the most places
and are most likely to cause confusion when their behavior is unclear.

Run /docs-sync-fix to add JSDoc stubs to undocumented functions.
```
