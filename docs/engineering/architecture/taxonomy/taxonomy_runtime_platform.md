# Runtime / Platform Taxonomy

**Status:** Authoritative Architectural Classification (Review-Enforced)
**Scope:** Runtime-layer and platform capability structure

This document operates within the Engineering Governance Framework:

```
<workspace>/docs/engineering/engineering_governance.md
```

It defines structural capability segmentation for the runtime and platform layers.

It does not define coding style or repository layout rules.
Those are governed by the C++ Coding Standard.

Formal dependency direction rules are defined in:

```
<workspace>/docs/engineering/dependency_rules.md
```

------

# 1. Path Context

All repository paths are relative to `<workspace>` unless explicitly stated otherwise.

Projects reside under:

```
<workspace>/projects/<project_name>/
```

Taxonomy paths shown such as:

```
src/runtime/platform/
```

are shorthand for:

```
<workspace>/projects/<project_name>/src/<project_name>/runtime/platform/
```

Public headers reside under:

```
<workspace>/projects/<project_name>/include/<project_name>/
```

Taxonomy segmentation applies within a project’s:

```
src/<project_name>/
```

subtree.

------

# 2. Purpose

The runtime layer defines how the program runs.

The platform sublayer defines system effects and their implementations.

Conceptually:

- `runtime/` = lifecycle, execution model, orchestration
- `runtime/platform/` = portable abstractions of system effects
- `runtime/platform_<os>/` = concrete OS implementations

Runtime builds on foundation.
Platform implementations must not leak OS-specific details into portable contracts.

------

# 3. Architectural Rules

## 3.1 Layering

Within a project’s runtime subtree:

- `runtime/**` MAY depend on `foundation/**`
- `runtime/platform/**` MAY depend on `foundation/**`
- `runtime/platform/**` MUST NOT depend on `runtime/**`
- `runtime/platform/**` MUST NOT depend on `runtime/platform_<os>/**`
- `runtime/platform_<os>/**` MAY depend on:
  - `runtime/platform/**`
  - `foundation/**`

Foundation MUST NOT depend on runtime (defined in dependency rules).

------

## 3.2 Platform Contract Rules

### runtime/platform/

This layer contains portable contracts only.

Includes:

- Interfaces (`*_service`, `*_provider`, `*_api`)
- Small value types
- Option/config structs
- Enums
- Portable error categories

Must NOT:

- Include OS headers
- Perform syscalls
- Leak OS-specific types
- Perform OS error translation

------

### runtime/platform_/

Concrete OS implementations (e.g., `platform_windows`, `platform_posix`).

Includes:

- OS API calls
- Syscalls
- Handle or descriptor management
- Error translation
- OS-only helpers

Must not expose OS-specific types through portable interfaces.

------

# 4. Canonical Runtime Layout

Within a runtime project’s:

```
src/<project_name>/runtime/
```

tree, the following capability structure is canonical:

```
app/
    include/
framework/
pipeline/
resource_manager/
state_machine/
platform/
    clock/
    console/
    environment/
    filesystem/
    process/
    registry/
    system_info/
    terminal/
    threading/
    user/
platform_windows/
    clock/
    console/
    environment/
    filesystem/
    process/
    registry/
    system_info/
    terminal/
    threading/
    user/
    debug/
platform_posix/
    clock/
    console/
    environment/
    filesystem/
    process/
    system_info/
    terminal/
    threading/
    user/
    debug/
```

Public API surfaces SHOULD mirror this segmentation under:

```
include/<project_name>/
```

Not all runtime capabilities must be public.
However, any public surface MUST respect:

- Layering constraints
- Namespace-directory alignment
- Platform boundary rules

------

# 5. Design Principles

- Contracts are defined once in `runtime/platform/`.
- OS-specific logic lives only in `runtime/platform_<os>/`.
- No system effects exist in foundation.
- Runtime orchestrates; foundation provides reusable primitives.
- Portable interfaces must remain stable and platform-neutral.

Architectural clarity and portability take precedence over convenience shortcuts.

------

# 6. Enforcement

Enforcement: Review.

Platform boundary violations and improper layering are review-blocking issues.

CI checks MAY be introduced for deterministic validation, including:

- Detecting OS header inclusion within `runtime/platform/`
- Detecting forbidden dependency edges
- Include-graph validation

The C++ Coding Standard remains authoritative where structural rules overlap.

------

# End of Document
