# /docs-sync-scan

Check a specific documentation file for drift.

## Usage

```
/docs-sync-scan README.md
/docs-sync-scan docs/api.md
/docs-sync-scan docs/
```

## Arguments

| Argument | Description |
|----------|-------------|
| `[file]` | Path to a `.md` file or directory of `.md` files. Defaults to all docs if omitted. |

## What it does

Invokes `skills/docs-scan` scoped to the specified file(s) only.

Useful for:
- Pre-commit check on a specific doc you just edited
- Verifying a single API reference page is current
- Spot-checking a directory of guides

Faster than a full `/docs-sync` scan on large projects.

## Example output

```
Scanning: docs/api.md

docs/api.md:87   CHANGED  calculateTotal(items)
  Function exists at src/cart/service.ts:34
  Current signature: calculateTotal(cart: Cart): number
  Documented as:     calculateTotal(items: Item[]): number

docs/api.md:102  OK  formatCurrency(amount, currency)
docs/api.md:118  OK  import { CartService } from './cart'

1 issue found. Run /docs-sync-fix to resolve.
```
