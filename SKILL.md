---
name: moonbit-spec-test-development
description: Create formal spec-driven MoonBit APIs and test suites. Use when asked to set up a spec.mbt, spec-driven tests, or a formal contract-first workflow (e.g., "set up a formal spec & test suite for Yaml in MoonBit"), including moon.mod.json/moon.pkg.json scaffolding and guidance to implement in separate files.
---

# MoonBit Spec & Test Development

## Overview
Define a formal API contract in `spec.mbt`, write type-checked tests against it, and scaffold a minimal MoonBit module so `moon check` passes before implementation work begins.

## Workflow

### 1) Confirm  name
- Ask for the module name/path (default `username/<project>` if not provided).
- Confirm the API surface: core types, entry points, error types, and any versioning needs.

### 2) Scaffold the module
- Create a new directory for the module.
- Add `moon.mod.json` and `moon.pkg.json` so the spec can pass `moon check`.
- Keep the module minimal: no imports unless required by the spec.

### 3) Write the formal spec (`spec.mbt`)
- Use `#declaration_only` for all types/functions that will be implemented later.
- Functions with `#declaration_only` must still have a body: `{ ... }`.
- Keep the spec as the contract: treat it as read-only after creation; implementation goes in new files under the same package.
- Use `pub(all)` for types you need to construct in tests; keep `pub` for opaque types.
- Separate top-level items with `///|`.

### 4) Write spec-driven tests
- Create `spec_easy_test.mbt` and `spec_difficult_test.mbt` (or similar) in the same package.
- If tests call unimplemented APIs, mark the test with `#skip` so it only type-checks.
- Prefer black-box tests using public APIs; use `@json.inspect(...)` for complex values.
- Use explicit labels in records (avoid label pun in record literals if it confuses parsing).

### 5) Validate
- Run `moon check` to confirm the spec + tests type-check.
- Run `moon test` only after implementations exist or tests are unskipped.

## Output checklist
- `moon.mod.json` created
- `moon.pkg.json` created
- `spec.mbt` with `#declaration_only` types/functions
- `spec_easy_test.mbt` and `spec_difficult_test.mbt` written
- Implementation intentionally left to new files (spec/tests treated as read-only)

## Templates
- See `references/templates.md` for scaffolding and file templates.
