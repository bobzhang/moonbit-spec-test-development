# Templates

Use these as starting points. Replace placeholders and keep `///|` between top-level items.
Replace `<pkg>` with the package name (for example, `yaml` → `yaml_spec.mbt`).
If you ran `moon new`, the new `moon.mod` and `moon.pkg` manifests are already created (current toolchains use the DSL formats; legacy `moon.mod.json`/`moon.pkg.json` are no longer accepted — migrate with `moon fmt`).

## moon.mod
```
name = "username/<project>"
version = "0.1.0"
readme = "README.mbt.md"
license = "Apache-2.0"
preferred_target = "wasm"
description = ""
```

## moon.pkg (root package; add imports as needed)
```
import {
  // "moonbitlang/core/json",
}
```

## <pkg>_spec.mbt (skeleton)
```mbt
///|
/// Formal error type for parsing.
pub(all) suberror ParseError {
  Invalid(String)
} derive(Debug, Eq, ToJson)

///|
/// Opaque placeholder type.
declare pub type Yaml

///|
/// Convert to a readable string.
declare pub fn Yaml::to_string(value : Yaml) -> String raise

///|
/// Parse YAML content.
declare pub fn parse_yaml(input : String) -> Yaml raise ParseError
```

Note: the current form is the `declare` keyword with no body. The older `#declaration_only` attribute (which required a `{ ... }` body) is deprecated (warning `deprecated_syntax`, no longer listed by `moon explain --attribute`) though it still functions on moonc v0.10.17.

## <pkg>_easy_test.mbt

```mbt
///|
test "spec parses simple yaml" {
  // Black-box tests call the raising API directly; an unexpected raise fails the test.
  let value = @yaml.parse_yaml("a: 1")
  ignore(value)
}
... // more tests
```

## <pkg>_mid_test.mbt

```mbt
///|
test "spec handles lists in yaml" {
  try @yaml.parse_yaml("items:\n  - one\n  - two\n  - three") catch {
    @yaml.ParseError::Invalid(_) => ()
  } noraise {
    _ => fail("expected malformed input to raise")
  }
}
... // more tests
```

## <pkg>_difficult_test.mbt
```mbt
///|
test "spec handles nested yaml" {
  let value = @yaml.parse_yaml("a:\n  b: [1, 2]")
  json_inspect(value, content={ "a": { "b": [1, 2] } })
}
... // more tests
```

## <pkg>_feature_test.mbt (Additional tests added by Agents)

```mbt
///|
test "parse_yaml returns expected structure" {
  let value = @yaml.parse_yaml("a: 1")
  json_inspect(value, content={ "a": 1 })
}
```

Notes (verified on moonc v0.10.17):
- `try?` has been removed from the language; the templates above use direct calls, `try ... catch ... noraise`, and `try Ok(f()) catch { e => Err(e) }` (Result-as-data) instead.
- Use the prelude `json_inspect(value, ...)` without a package prefix (`@json.inspect` is deprecated).
- Prefer `derive(Debug, ...)` + `debug_inspect` over `derive(Show)`.
