# Dependency Rules

**Status:** Authoritative Architectural Policy (Review-Enforced)
**Scope:** Dependency direction and layering discipline

This document operates within the Engineering Governance Framework:

```
<workspace>/docs/engineering/engineering_governance.md
```

It defines allowed dependency directions between major architectural layers and modules.

It exists to:

- Prevent cyclic dependencies
- Preserve portability boundaries
- Keep system effects isolated behind platform contracts
- Maintain long-term architectural clarity

------

# 1. Definitions

## 1.1 Workspace

`<workspace>` refers to the repository root directory as defined in the Engineering Governance Framework.

All repository paths in this document are relative to `<workspace>` unless explicitly stated otherwise.

------

## 1.2 Project

A project is a buildable target located under:

```
<workspace>/projects/<project_name>/
```

Each project follows the canonical layout defined by the C++ Coding Standard:

```
include/<project_name>/
src/<project_name>/
src/<project_name>/detail/
```

------

## 1.3 Layer Definitions

Within a project’s:

```
src/<project_name>/
```

tree, the following conceptual layers may exist:

- **foundation** — reusable, platform-agnostic building blocks
- **runtime** — execution and orchestration layer
- **platform contracts** — portable system-effect interfaces
- **platform implementations** — OS-specific backends
- **patterns** — optional architectural patterns

Paths shown such as:

```
src/foundation/**
```

are shorthand for:

```
<workspace>/projects/<project_name>/src/<project_name>/foundation/**
```

------

# 2. Global Layering Rules

## 2.1 Core Layering Constraints

- `foundation/**` MUST NOT depend on `runtime/**`.
- `foundation/**` MUST NOT depend on `runtime/platform/**`.
- `foundation/**` MUST NOT depend on `runtime/platform_<os>/**`.
- `runtime/**` MAY depend on `foundation/**`.
- `runtime/**` MAY depend on `runtime/platform/**`.
- `runtime/**` MAY depend on `runtime/platform_<os>/**` only via composition or wiring.
  - OS-specific types MUST NOT leak through portable contracts.
- `runtime/platform/**` MAY depend on `foundation/**`.
- `runtime/platform/**` MUST NOT depend on `runtime/**` (orchestration layer).
- `runtime/platform/**` MUST NOT depend on `runtime/platform_<os>/**`.
- `runtime/platform_<os>/**` MAY depend on:
  - `runtime/platform/**`
  - `foundation/**`
- `foundation/patterns/**` MAY depend on `foundation/core/**`.
- `foundation/core/**` MUST NOT depend on `foundation/patterns/**`.

------

## 2.2 Platform Boundary Rules

Within `runtime/platform/**`:

- OS headers MUST NOT be included.
- Syscalls MUST NOT be performed.
- OS-specific types MUST NOT appear in public portable interfaces.

OS error translation MUST occur exclusively in:

```
runtime/platform_<os>/**
```

------

# 3. Allowed Dependency Matrix

Legend:

- ✅ Allowed
- ❌ Forbidden

| Layer ↓ / Depends On → | foundation | runtime | runtime/platform | platform_ | patterns |
| ---------------------- | ---------- | ------- | ---------------- | --------- | -------- |
| foundation             | ✅          | ❌       | ❌                | ❌         | ✅*       |
| runtime                | ✅          | ✅       | ✅                | ✅         | ✅        |
| runtime/platform       | ✅          | ❌       | ✅                | ❌         | ❌        |
| platform_              | ✅          | ❌       | ✅                | ✅         | ❌        |
| patterns               | ✅          | ❌       | ❌                | ❌         | ✅        |

\* `foundation/patterns/**` may be depended upon by foundation modules other than `foundation/core/**`, but `foundation/core/**` must remain pattern-free.

------

# 4. Common Placement Examples

## 4.1 Filesystem

Portable interface:

```
src/<project_name>/runtime/platform/filesystem/
```

Windows implementation:

```
src/<project_name>/runtime/platform_windows/filesystem/
```

POSIX implementation:

```
src/<project_name>/runtime/platform_posix/filesystem/
```

Foundation may provide:

```
src/<project_name>/foundation/io/file/**
```

for path algorithms or helpers, but MUST NOT perform OS filesystem calls.

------

## 4.2 Diagnostics and Logging

API surface:

```
src/<project_name>/foundation/diagnostics/**
```

Logger backends:

```
src/<project_name>/foundation/io/sinks/logger/**
```

- `diagnostics/**` MAY depend on logger sinks.
- Logger sinks MUST NOT depend on diagnostics.

------

## 4.3 Console vs UI

Low-level stream/device I/O:

```
src/<project_name>/foundation/io/console/**
```

User interaction and UX flows:

```
src/<project_name>/foundation/ui/**
```

- `ui/**` MAY depend on `io/console/**`.
- `io/console/**` MUST NOT depend on `ui/**`.

------

# 5. Prohibited Patterns

The following are prohibited:

- Any cyclic dependency between `foundation/**` and `runtime/**`
- OS headers included in `runtime/platform/**`
- Syscalls performed from `runtime/platform/**`
- Platform-specific types exposed through `runtime/platform/**` public APIs
- Creating generic “utility” dependency hubs to avoid proper factoring

------

# 6. Public API and Project Boundary

Public headers reside under:

```
<workspace>/projects/<project_name>/include/<project_name>/
```

Public API segmentation SHOULD mirror the taxonomy structure.

Not all taxonomy capabilities must be public.
However, any public surface MUST respect:

- Layering constraints
- Namespace-directory alignment
- Platform boundary rules

------

# 7. Enforcement

Enforcement: Review.

Architectural dependency violations are review-blocking issues.

Deterministic validation MAY be introduced via CI, including:

- Preventing `foundation` from including `runtime` headers.
- Preventing `runtime/platform` from including OS headers.
- Include-graph validation to detect forbidden dependency edges.

The C++ Coding Standard remains authoritative where structural rules overlap.

------

# 8. Stability Principle

Dependency direction defines architectural integrity.

Convenience changes that weaken layering discipline are prohibited.

Architectural clarity takes precedence over short-term refactoring convenience.

------

# End of Document
