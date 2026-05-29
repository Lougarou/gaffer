# @kurrent/projections-testing

## 0.2.0

### Minor Changes

- e9dfaff: The quirks-selecting option and the quirk registry are renamed to retire the misleading "DB version" / "bug" framing.
  - **`dbVersion` is now `quirksVersion`** across the runtime, the JS bindings (`SessionOptions`), and the testing library (`ProjectionOptions`). The value is unchanged: a `MAJOR.MINOR.PATCH` string, where unset still reproduces every known quirk and a set version turns off quirks fixed upstream as of it. Only the key moves. `dbVersion` read as passive info when it actively selects which quirks to emulate, and it collided with `engineVersion`.
  - **`knownBugs()` is now `knownQuirks()`**, and **`KnownBug` is now `KnownQuirk`**, in the JS and Go bindings. Most registry entries are deliberate KurrentDB quirks gaffer reproduces, not bugs to report upstream.
  - **CLI**: the `gaffer.toml` key `db_version` is now `quirks_version`, the env var `GAFFER_DB_VERSION` is now `GAFFER_QUIRKS_VERSION`, and the MCP resource `gaffer://docs/db-version-bugs` is now `gaffer://docs/quirks`. The connected-server-version telemetry (the `db_version` event property) is unaffected, since it genuinely reports the connected DB version.

  No deprecation period: pre-1.0, hard break. An old `dbVersion` or `db_version` key is silently ignored rather than rejected, so update existing call sites and `gaffer.toml` files.

- 223e9ab: The `TestEvent` schema now rejects inputs KurrentDB could never deliver to a handler: `eventType` and `streamId` must be non-empty strings, and `sequenceNumber` must be a non-negative integer. Previously a unit test could pass against events the projection would never see in production, such as a negative sequence number or an empty stream id.

### Patch Changes

- Updated dependencies [e9dfaff]
  - @kurrent/gaffer-runtime@0.2.0

## 0.1.2

### Patch Changes

- 3b5392c: Documentation links in the README now point at `gaffer.kurrent.io` rather than the `docs.kurrent.io/gaffer/` placeholder.
- Updated dependencies [3b5392c]
  - @kurrent/gaffer-runtime@0.1.2

## 0.1.1

### Patch Changes

- 2675301: Republish to track `@kurrent/gaffer-runtime@^0.1.1`. The runtime's native binary was missing in 0.1.0, so any test using this library at 0.1.0 failed to load the projection engine.
- Updated dependencies [2675301]
  - @kurrent/gaffer-runtime@0.1.1

## 0.1.0

### Minor Changes

- 5b85426: Test KurrentDB projections locally with any test runner (vitest, jest, mocha). Wraps `@kurrent/gaffer-runtime` to execute projections against test events with the same behaviour as a real KurrentDB instance.
  - `createProjection<TState>(source, options?)` - prepare a projection for repeated runs. Compiles lazily on first `validate` / `run` / `test`.
  - `projection.validate()` - compile and return the projection's source definition.
  - `projection.run(events)` - replay events from an array, async iterable, or live `KurrentDBClient` subscription. Yields a `StepResult` per event.
  - `projection.test()` - interactive session: feed events one at a time, query state by partition, inspect emitted events and logs.
  - `systemEvents` helpers for constructing KurrentDB system events (e.g. `streamDeleted`).
  - Typed `ProjectionError` subclasses propagated from the runtime with structured fields and formatted messages.

### Patch Changes

- Updated dependencies [5b85426]
  - @kurrent/gaffer-runtime@0.1.0
