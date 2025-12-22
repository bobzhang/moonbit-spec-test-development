# Templates

Use these as starting points. Replace placeholders and keep `///|` between top-level items.

## moon.mod.json
```json
{
  "name": "username/<project>",
  "version": "0.1.0",
  "source": ".",
  "deps": {}
}
```

## moon.pkg.json
```json
{}
```

## spec.mbt (skeleton)
```mbt
///|
/// Formal error type for parsing.
pub(all) suberror ParseError {
  Invalid(String)
} derive(Show, Eq, ToJson)

///|
/// Opaque placeholder type.
#declaration_only
pub type Yaml

///|
/// Convert to a readable string.
#declaration_only
pub fn Yaml::to_string(value : Yaml) -> String raise {
  ...
}

///|
/// Parse YAML content.
#declaration_only
pub fn parse_yaml(input : String) -> Yaml raise ParseError {
  ...
}
```

## spec_easy_test.mbt (type-check only)
```mbt
///|
#skip
test "spec parses simple yaml" {
  let result : Result[Yaml, ParseError] = try? parse_yaml("a: 1")
  ignore(result)
}
```

## spec_difficult_test.mbt (type-check only)
```mbt
///|
#skip
test "spec handles nested yaml" {
  let result : Result[Yaml, ParseError] = try? parse_yaml("a:\n  b: [1, 2]")
  ignore(result)
}
```

## Optional: spec-driven executable tests (after implementation)
```mbt
///|
test "parse_yaml returns expected structure" {
  let value = parse_yaml("a: 1")
  @json.inspect(value, content={ "a": 1 })
}
```
