# Core / Foundation Taxonomy

**Status:** Authoritative Architectural Classification (Review-Enforced)
**Scope:** Foundation-layer capability structure

This document operates within the Engineering Governance Framework:

```
<workspace>/docs/engineering/engineering_governance.md
```

It defines structural capability segmentation for the foundation layer.

It does not define coding style or repository layout rules.
Those are governed by the C++ Coding Standard.

------

# 1. Path Context

All repository paths are relative to `<workspace>` unless explicitly stated otherwise.

Projects reside under:

```
<workspace>/projects/<project_name>/
```

Taxonomy paths shown such as:

```
src/foundation/core/
```

are shorthand for:

```
<workspace>/projects/<project_name>/src/<project_name>/foundation/core/
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

The foundation layer provides platform-agnostic, reusable building blocks.

It contains:

- Core primitives
- Data structures
- Text processing
- Diagnostics
- Configuration
- Cryptography
- I/O abstractions (non-OS)
- Metaprogramming utilities
- Optional design patterns

Foundation MUST NOT depend on runtime.

Layering rules are defined formally in `dependency_rules.md`.

------

# 3. Architectural Rules

## 3.1 Layering

- `foundation/**` MUST NOT depend on `runtime/**`.
- `patterns/**` MAY depend on `core/**`.
- `core/**` MUST NOT depend on `patterns/**`.

These constraints apply within the foundation project and any equivalent foundation-scoped module.

------

## 3.2 Responsibility Boundaries

### Diagnostics vs Logging

- `diagnostics/` owns the API surface.
- `io/sinks/logger/` owns backend implementations used by diagnostics.

`diagnostics/` MAY depend on logger sinks.
Logger sinks MUST NOT depend on `diagnostics/`.

------

### Console vs UI

- `io/console/` = low-level stream I/O and formatting only
- `ui/` = user interaction, controls, prompts, flows

If it models a device → `io/`.
If it models user experience → `ui/`.

------

### Meta vs Runtime

- `meta/` contains compile-time helpers only.
- No runtime behavior belongs in `meta/`.

------

# 4. Canonical Foundation Layout

Within a foundation project’s:

```
src/<project_name>/foundation/
```

tree, the following capability structure is canonical:

```
archive/
config/
    cli/
        cmd_args/
    env/
        env_vars/
    settings/
        core/
        app_specific/
    validation/
core/
    byte/
    err/
    mem/
        byte/
        buffer/
        bitset/
    time/
        date/
        time/
        datetime/
        calendar/
        duration/
        timezone/
        archive/
crypto/
    hash/
    digest/
        sha256/
            spec/
data/
    canonical/
    color/
    counter/
    dictionary/
    keyval/
    sparse_table/
    tree/
        red_black_tree/
diagnostics/
    core/
        assert/
        diag/
        error/
        exception/
        source_location/
io/
    console/
    file/
    media/
    serialization/
    sinks/
        logger/
        terminal/
math/
    stats/
meta/
    canonical/
    interfaces/
    type_traits/
patterns/
    facades/
    mvc/
    observer/
    producer_consumer/
text/
    char/
    encoding/
    lex/
    parser/
    regex/
    string/
    template_engine/
ui/
    color/
    controls/
    framework/
    mvc/
    ux_utils/
```

Public API surfaces SHOULD mirror this segmentation under:

```
include/<project_name>/
```

However, not all capabilities must be public.

------

# 5. Design Principles

- Foundation is reusable and platform-agnostic.
- No system effects occur here.
- Runtime builds on foundation.
- Clear separation between:
  - API vs backend
  - UX vs device
  - Compile-time vs runtime

Architectural clarity takes precedence over convenience refactoring.

------

# 6. Enforcement

Enforcement: Review.

Capability misplacement and foundation/runtime boundary violations are review-blocking issues.

CI checks MAY be introduced for deterministic validation.

The C++ Coding Standard remains authoritative where structural rules overlap.

------

# End of Document
