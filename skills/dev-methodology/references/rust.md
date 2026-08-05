# Rust & Hexagonal Architecture Specifics

Load this file before the first architecture or implementation work on a codebase written in Rust, or following a hexagonal / port-adapter architecture in any language. Once per session, not per file. Do not load it merely to read Rust, review a diff, or answer a question about the code. Apply these rules on top of the core methodology.

## Hexagonal boundaries

- The domain layer imports nothing from infrastructure. No SQL types, no HTTP types, no framework types inside domain entities or use cases. If a domain entity knows a SQL table name, the boundary is already broken — flag it and propose the fix.
- Ports are traits (or interfaces) defined *by the domain*, implemented by adapters. Dependencies point inward, always.
- Conversions happen at boundaries with dedicated types: `TryFrom`/`From` implementations between DTOs and domain types, never dispersed validation inside handlers or repositories.
- One adapter per external concern (persistence, transport, messaging). Swapping an adapter must never require touching the domain.

## Dispatch and abstraction

- Prefer static dispatch: generics and enums before trait objects. `Box<dyn Trait>` is acceptable at composition roots or plugin boundaries, not as a default.
- For port/adapter wiring with a small closed set of implementations, prefer enum-based dispatch over `dyn`: exhaustive matching, zero vtable cost, and the compiler enforces completeness when a variant is added.
- Prefer `impl Future` / native `async fn` in traits over `async-trait` boxing when the MSRV allows it.

## Imports and paths

- Import the item, call it bare. Every type, trait, and free function used in a file gets a `use` at the top; the call site carries the shortest path possible. Fully-qualified inline paths are noise repeated at every occurrence.

```rust
// Avoid
let t = tokio::time::Instant::now();

// Prefer
use tokio::time::Instant;

let t = Instant::now();
```

- Two exceptions, both about ambiguity: on a name collision (`io::Error` vs `fmt::Error`), import the parent module and qualify (`io::Error`) or alias explicitly (`use std::io::Error as IoError`) — never leave the reader guessing which one is in scope. On a bare name that says nothing on its own (`Handle`, `Config`, `Builder`), keep the parent module as the qualifier.
- Enum variants are imported when the enum is the file's subject (`use Direction::*` inside its own module), qualified otherwise.
- No glob imports outside test modules and preludes.

## Type-driven invariants

- Make invalid states unrepresentable: newtypes for identifiers and secrets, exhaustive enums for state machines, `NonZero*`/`Option` instead of sentinel values.
- Never ignore a `Result`. `.unwrap()`/`.expect()` are acceptable only in tests, examples, and provably-infallible cases — with a comment stating why.
- Use `#[must_use]` on types and functions whose result being dropped is a bug.
- Sensitive material (keys, tokens, passwords) gets deterministic cleanup: `zeroize` on drop, no `Debug`/`Display` derivation, no accidental logging.

## Persistence and SQL

- Prefer compile-time-checked SQL (sqlx macros) over string-built queries; migrations are versioned and reversible.
- Repository traits live in the domain; sqlx types never cross the port.

## Testing in Rust

- Unit-test the domain with no I/O and no tokio runtime where possible; integration-test adapters against real backends (testcontainers or CI services), not mocks of the driver.
- Property-based tests (proptest) for parsers, converters, and state machines — anywhere `TryFrom` can fail.
- Benchmarks are versioned and thresholded (criterion / CodSpeed); a performance regression is a failing test.

## Error handling

- Library/domain code: explicit error enums (thiserror), one per module boundary, no `anyhow` leaking through ports.
- Binary/composition code: `anyhow`/`eyre` acceptable at the top level only.
- Errors carry enough context to be actionable without a debugger; attach context at each boundary crossing, not deep inside.
