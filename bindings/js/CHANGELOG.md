# @kurrent/gaffer-runtime

## 0.2.0

### Minor Changes

- e9dfaff: The quirks-selecting option and the quirk registry are renamed to retire the misleading "DB version" / "bug" framing.
  - **`dbVersion` is now `quirksVersion`** across the runtime, the JS bindings (`SessionOptions`), and the testing library (`ProjectionOptions`). The value is unchanged: a `MAJOR.MINOR.PATCH` string, where unset still reproduces every known quirk and a set version turns off quirks fixed upstream as of it. Only the key moves. `dbVersion` read as passive info when it actively selects which quirks to emulate, and it collided with `engineVersion`.
  - **`knownBugs()` is now `knownQuirks()`**, and **`KnownBug` is now `KnownQuirk`**, in the JS and Go bindings. Most registry entries are deliberate KurrentDB quirks gaffer reproduces, not bugs to report upstream.
  - **CLI**: the `gaffer.toml` key `db_version` is now `quirks_version`, the env var `GAFFER_DB_VERSION` is now `GAFFER_QUIRKS_VERSION`, and the MCP resource `gaffer://docs/db-version-bugs` is now `gaffer://docs/quirks`. The connected-server-version telemetry (the `db_version` event property) is unaffected, since it genuinely reports the connected DB version.

  No deprecation period: pre-1.0, hard break. An old `dbVersion` or `db_version` key is silently ignored rather than rejected, so update existing call sites and `gaffer.toml` files.

## 0.1.2

### Patch Changes

- 3b5392c: Documentation links in the README now point at `gaffer.kurrent.io` rather than the `docs.kurrent.io/gaffer/` placeholder.

## 0.1.1

### Patch Changes

- 2675301: Republish the per-platform native packages with their compiled `gaffer.so` / `.dylib` / `.dll`. 0.1.0 shipped those packages empty due to a CI workflow bug (`upload-artifact@v4` strips directory paths for single-file uploads, so the download step at publish time saw colliding bare-named files at the workspace root instead of files in their per-platform package dirs). Installing 0.1.0 left koffi unable to load the runtime. Reinstall `>=0.1.1` to pick up the fix.

## 0.1.0

### Minor Changes

- 5b85426: Low-level Node.js bindings for the gaffer projection runtime. Runs the same JavaScript engine KurrentDB uses for server-side projections, wrapping the NativeAOT shared library.
  - `ProjectionSession` - feed events, query state by partition, observe emits / logs / state changes, dispose.
  - `knownBugs()` - the runtime's list of known engine bugs by version, surfaced as actionable warnings in editor tooling.
  - Typed `ProjectionError` subclasses (handler errors, malformed events, compilation / execution timeouts, serialization failures) with structured fields.
  - Underpins [`@kurrent/projections-testing`](https://www.npmjs.com/package/@kurrent/projections-testing) and the [KurrentDB Projections VS Code extension](https://marketplace.visualstudio.com/items?itemName=kurrent-io.gaffer).
