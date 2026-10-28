---
name: moonbit-spec-test-development
description: Create formal spec-driven MoonBit APIs and test suites. Use when asked to set up a spec.mbt, spec-driven tests, or a formal contract-first workflow (e.g., "set up a formal spec & test suite for Yaml in MoonBit"), including moon.mod/moon.pkg scaffolding and guidance to implement in separate files.
---

# MoonBit Spec & Test Development

> Validated 2026-10-28 against moon 0.1.20261023 / moonc v0.10.17+d971670cd by building the full staged workflow live: spec-only → spec + black-box tests → implementation, each stage probed with `moon check` / `moon test`. Key refreshed facts: `declare` keyword replaces the deprecated `#declaration_only` attribute, legacy `.json` manifests are hard-rejected, `try?` is removed, and `json_inspect` is a prelude function.

## Overview
Define a formal API contract in `<pkg>_spec.mbt`, write type-checked tests against it, and scaffold a minimal MoonBit module so `moon check` passes before implementation work begins.

## Workflow

###  Confirm API surface (if needed)
- Infer the package name from context; only ask if ambiguous.
- Confirm the API surface: core types, entry points, error types, and any versioning needs.

###  Scaffold the module using `moon new` (preferred for new projects)
- `moon new` generates the current `moon.mod` / `moon.pkg` DSL manifests (legacy `moon.mod.json` / `moon.pkg.json` are no longer accepted — "Run `moon fmt` to migrate"); if you didn't use `moon new`, fall back to the `references/templates.md` manifests.

###  Write the formal spec (`<pkg>_spec.mbt`)
- Use the `declare` keyword for all types/functions that will be implemented later (`declare pub fn f(...) -> T` — no body). The older `#declaration_only` attribute (which required a `{ ... }` body) is deprecated: it still functions on v0.10.17 but emits `deprecated_syntax` and is gone from `moon explain --attribute`.
- Keep the spec as the contract: treat it as read-only after creation; implementation goes in new files under the same package.
- Use `pub(all)` for types you need to construct in tests; keep `pub` for opaque types.
- Prefer `raise`-style signatures (`-> T raise ParseError`); `Result[T, E]` with an error type now warns `result_error_return`.

###  Write spec-driven tests
- Create `<pkg>_easy_test.mbt`, `<pkg>_mid_test.mbt`, and `<pkg>_difficult_test.mbt` (or similar) in the same package.
- Prefer black-box tests using public APIs (`@pkg.` prefix on every symbol, including error constructors); use the prelude `json_inspect(...)` for complex values (`@json.inspect` is deprecated).
- Call raising APIs directly in tests; `try?` has been removed (use `try ... catch ... noraise` for expected failures, or `try Ok(f()) catch { e => Err(e) }` when a `Result` must flow as data).
- Try to make the tests rich enough to validate the spec surface.

###  Validate (staged semantics, verified v0.10.17)
- Spec only: `moon check` exits 0 with `declaration_unimplemented` warnings — that is the green light to write tests.
- Spec + black-box tests (`*_test.mbt`), still no implementation: `moon check` still exits 0; `moon test` fails (exit 2) until implementations exist.
- White-box tests (`*_wbtest.mbt`) referencing unimplemented declarations fail `moon check` — during the gap, write black-box tests.
- After implementing: `moon check` warnings for implemented items disappear; `moon test` goes green.

## Templates
- See `references/templates.md` for scaffolding and file templates.
