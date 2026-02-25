# Taxonomy Glossary

**Status:** Authoritative Architectural Classification (Review-Enforced)
**Scope:** Capability boundaries and placement discipline

This document operates within the Engineering Governance Framework:

```
<workspace>/docs/engineering/engineering_governance.md
```

It defines capability boundaries used for module classification and architectural placement.

It exists to:

- Prevent structural drift
- Eliminate ambiguous placement decisions
- Avoid creation of generic folders (e.g., `utils/`, `common/`, `misc/`)
- Preserve architectural layering discipline

------

# 1. Path Context

All repository paths are relative to `<workspace>` unless explicitly stated otherwise.

Projects reside under:

```
<workspace>/projects/<project_name>/
```

Taxonomy paths shown such as:

```
src/foundation/**
```

are shorthand for:

```
<workspace>/projects/<project_name>/src/<project_name>/foundation/**
```

Public headers reside under:

```
<workspace>/projects/<project_name>/include/<project_name>/
```

Taxonomy segmentation applies within a project’s `src/<project_name>/` subtree.

------

# 2. Foundation Capabilities

These capabilities typically reside under:

```
src/<project_name>/foundation/
```

## core/

Fundamental primitives used everywhere.

Includes:

- Error model types
- Memory primitives
- Byte utilities
- Time primitives
- Low-level value abstractions

Does NOT include:

- OS interactions
- File system calls
- UI logic
- Architectural patterns

If a module is foundational and extremely low-level, it belongs here.

------

## data/

Reusable data structures and domain-neutral value containers.

Includes:

- Trees
- Dictionaries
- Key-value stores
- Structural counters (not statistical algorithms)
- Canonical value representations

Does NOT include:

- Algorithms operating on data
- Serialization logic

------

## text/

Text processing and transformation.

Includes:

- Encoding/decoding
- String manipulation
- Lexing
- Parsing
- Regex
- Template engines

If it produces, consumes, or transforms text → it belongs here.

------

## io/

Platform-agnostic input/output utilities.

Includes:

- Stream abstractions
- Console stream formatting
- Serialization formats
- Logger sink backends

Does NOT include:

- OS file system access
- Syscalls

If it models a stream/device abstraction → `io/`.

------

## diagnostics/

Developer-facing observability surface.

Includes:

- Assertions
- Error reporting
- Exception policies
- Source location helpers

Diagnostics owns the API surface.

Logger sink implementations reside under:

```
foundation/io/sinks/logger/
```

Diagnostics MAY depend on logger sinks.
Logger sinks MUST NOT depend on diagnostics.

------

## config/

Configuration modeling and parsing.

Includes:

- CLI parsing models
- Environment variable models (non-OS)
- Settings schemas
- Validation rules

Does NOT include:

- Runtime environment access (belongs in runtime/platform).

------

## crypto/

Platform-agnostic cryptographic algorithms.

Includes:

- Hash algorithms
- Digest implementations

Does NOT include:

- OS certificate store access
- Platform security APIs

------

## math/

Mathematical primitives and statistical helpers.

Includes:

- Statistical calculations
- Numeric helpers

Structural counters remain in `data/`.

------

## meta/

Compile-time and type-system utilities only.

Includes:

- Type traits
- Detection idioms
- Interface scaffolding

Must not contain runtime behavior.

------

## patterns/

Optional architectural patterns.

Includes:

- MVC
- Observer
- Producer-consumer
- Facades

Patterns MAY depend on `core/`.
`core/` MUST NOT depend on `patterns/`.

------

## ui/

User interaction constructs.

Includes:

- Controls
- Prompts
- Forms
- UI frameworks
- UX utilities

If it models user experience → `ui/`.
If it models a stream/device → `io/`.

------

# 3. Runtime & Platform Capabilities

These capabilities typically reside under:

```
src/<project_name>/runtime/
```

## runtime/

Execution and orchestration layer.

Includes:

- Application lifecycle
- Pipelines
- State machines
- Resource management

May depend on foundation.

------

## runtime/platform/

Portable system-effect contracts.

Includes:

- Filesystem abstractions
- Process abstractions
- Console abstractions
- Environment abstractions
- Threading abstractions

Must NOT:

- Include OS headers
- Perform syscalls

------

## runtime/platform_/

Concrete OS implementations (e.g., `platform_windows`, `platform_posix`).

Includes:

- Syscalls
- OS APIs
- Handle management
- Error translation

Must not leak OS-specific types into portable interfaces.

------

# 4. Prohibited Generic Folders

The following generic folders are prohibited in first-party source trees:

- `utils/`
- `common/`
- `misc/`
- `shared/`
- `helpers/`
- `temp/`
- `scratch/`

This prohibition applies only to first-party source code under:

```
<workspace>/projects/
```

It does not apply to:

- `build/` output
- Tooling or script directories (e.g., `tools/`, `scripts/`)
- Generated artifacts
- Third-party or vendor code

Do not create generic catch-all folders for first-party modules.

------

# 5. Tie-Breaker Rules

If unsure between:

- `core` vs `data` → choose `core` only if primitive and universal.
- `data` vs `math` → `data` for structure; `math` for computation.
- `io` vs `ui` → `io` for device; `ui` for experience.
- `foundation` vs `runtime` → `foundation` if no system effects exist.

When in doubt:

Preserve layering and portability.

------

# 6. Enforcement

Enforcement: Review.

Capability misplacement and taxonomy drift are review-blocking issues.

CI checks MAY be introduced for deterministic validation.

The C++ Coding Standard remains authoritative where structural rules overlap.

------

# End of Document
