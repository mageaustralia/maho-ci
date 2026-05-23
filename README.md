# maho-ci

Shared CI for the `maho-module-*` repositories.

Hosts a **reusable GitHub Actions workflow** so the Maho coding-standards checks
live in one place instead of being copy-pasted into every module repo.

## What it checks

`.github/workflows/module-checks.yml` runs on the calling repo:

- `composer validate --strict`
- `php -l` on every file under `app/`
- **Maho standards** — greps for classes/APIs Maho removed, which otherwise fatal
  at runtime:
  - `Zend_*` classes (Log, Json, Date, …)
  - `Varien_Date` (removed, no alias → use `Mage_Core_Model_Locale::nowUtc()`)
  - `escapeHtmlAttr()` (does not exist → use `escapeHtml()`)
  - `getUserParam()` (→ `getParam()`)
  - Prototype.js patterns (`$$()`, `Event.observe()`, `Ajax.Request()`, `$('id')`)

These mirror the dev backend's pre-commit hook, so the same issues are caught at
PR time in the repo where the code is authored.

## Use it from a module repo

`.github/workflows/ci.yml`:

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:
jobs:
  checks:
    uses: mageaustralia/maho-ci/.github/workflows/module-checks.yml@main
```

Optionally pin a PHP version: `with: { php-version: '8.3' }`.
