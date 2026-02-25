# C++ Engineering Guidelines

## Baseline Edition

**Status:** Authoritative Guidance
**Supersedes:** None
**Companion To:** C++ Coding Standard

------

# 1. Document Authority and Scope

## 1.1 Status

This document defines the baseline edition of the C++ Engineering Guidelines.

The effective Guidelines consist of:

- This baseline document
- All non-deprecated delta documents defined by the authority model

This document does not alter or weaken any requirement defined in the C++ Coding Standard.

------

## 1.2 Scope

These Guidelines govern cross-cutting engineering strategy, including:

- Command-line processing
- Error handling models
- Logging and observability
- Resource ownership patterns
- Configuration layering
- Dependency boundaries
- Concurrency discipline
- Testing strategy

These Guidelines do not govern:

- Formatting
- Naming conventions
- Repository structure
- CI enforcement
- Compiler configuration

Those concerns remain exclusively governed by the Coding Standard.

------

## 1.3 Normative Language

The keywords in this document have the following meaning:

- **SHOULD** — Strong recommendation.
- **RECOMMENDED** — Preferred practice.
- **AVOID** — Strongly discouraged.
- **MAY** — Optional.

The keyword **SHALL** is intentionally avoided except where safety or correctness requires it.

Guidelines are review-enforced, not CI-enforced.

------

# 2. Command-Line Processing

## 2.1 Library Usage

Projects **SHOULD** use a dedicated argument parsing library rather than manually parsing `argv`.

Manual parsing is acceptable only for trivial tools.

------

## 2.2 Configuration Object Pattern

Command-line processing **SHOULD**:

1. Parse input arguments.
2. Validate inputs.
3. Produce a configuration object.
4. Transfer control to application logic.

The `main()` function **SHOULD NOT** contain business logic.

------

## 2.3 Deterministic Behavior

CLI options **SHOULD**:

- Have deterministic precedence rules.
- Provide clear help output.
- Fail fast on invalid input.

------

# 3. Error Handling Strategy

## 2.4 Application Bootstrap Structure

Executable applications **SHOULD** follow a deterministic bootstrap sequence:

1. Parse CLI arguments.
2. Validate inputs.
3. Construct a configuration object.
4. Initialize infrastructure (logging, configuration loading, dependency graph).
5. Invoke the application entry point.
6. Return a well-defined exit code.

The `main()` function **SHOULD** contain orchestration only and **SHOULD NOT** contain business logic.

---

## 2.5 Configuration Object Pattern

CLI parsing **SHOULD** produce a strongly-typed configuration object.

The configuration object:

- **SHOULD** represent validated inputs.
- **SHOULD** be immutable after construction.
- **SHOULD NOT** expose raw parsing artifacts (e.g., raw string maps).

Business logic **SHOULD** depend on the configuration object, not on `argc`/`argv`.

---

## 2.6 Command Dispatch Pattern (Multi-Command Tools)

For tools supporting subcommands:

- A command-dispatch layer **SHOULD** map subcommands to discrete handlers.
- Each command handler **SHOULD** receive a typed configuration specific to that command.
- Command registration **SHOULD** be centralized to avoid scattered dispatch logic.

Avoid large conditional chains in `main()` for command routing.

---

## 2.7 Deterministic Precedence Rules

When CLI options interact with configuration files or environment variables:

Precedence **SHOULD** be deterministic and documented.

Recommended precedence order:

1. Built-in defaults
2. Configuration file(s)
3. Environment variables
4. Command-line arguments

The precedence model **SHOULD** remain stable across minor versions.

---

## 2.8 Exit Code Discipline

Applications **SHOULD** define explicit exit code semantics.

- Success and failure codes **SHOULD** be documented.
- Distinct failure categories (validation error, runtime failure, configuration error) **MAY** use distinct exit codes.
- Exit codes **SHOULD NOT** be implicitly derived from unrelated error values.

---

## 2.9 Error Integration

The CLI layer **SHOULD** integrate with the project’s declared error model:

- Exception-based projects **SHOULD** translate unhandled exceptions into deterministic exit codes.
- Result-based projects **SHOULD** propagate errors to a single exit-point translator.

Unhandled failures **SHOULD NOT** produce ambiguous termination behavior.

---

## 2.10 Testability

CLI parsing logic **SHOULD** be testable independently from process startup.

It is RECOMMENDED that:

- Parsing be factored into a function separate from `main()`.
- Command handlers be invocable without process-level side effects.

## 3.1 Explicit Error Model

Each project **SHOULD** explicitly define and document its primary error model.

New modules **SHOULD** conform to the declared model.

Cross-module boundaries **SHOULD NOT** mix exception-based and result-based error signaling unless explicitly
justified and documented.

If no model is declared, result-based error handling is **RECOMMENDED** for new code.

**Clarification**

The selected error model:

- **SHOULD** be documented in the project README or architecture documentation.
- **SHOULD** remain stable across minor versions.
- **SHOULD NOT** change without architectural review.

## 3.2 Exceptions

If exceptions are used:

- Use for exceptional conditions, not control flow.
- Prefer strong or basic exception safety guarantees.
- Avoid throwing across library ABI boundaries.

Destructors **SHOULD NOT** throw.

------

## 3.3 Result-Based Patterns

If using result-based error handling:

- Prefer expressive result types over primitive booleans.
- Provide diagnostic context.
- Avoid silent failure.

------

## 3.4 Error Domain Design

Projects **SHOULD** define:

- Clear error categories.
- Stable error semantics.
- Consistent mapping between internal and external errors.

------

# 4. Logging and Observability

## 4.1 Abstraction

Logging **SHOULD** be abstracted behind an interface.

Core modules **SHOULD NOT** depend directly on a concrete logging framework.

------

## 4.2 Severity Model

Projects **SHOULD** define a consistent severity taxonomy:

- trace
- debug
- info
- warning
- error
- critical

Naming and semantics should be consistent across modules.

------

## 4.3 Structured Logging

Structured logging is RECOMMENDED over free-form string concatenation.

Prefer key/value context when practical.

------

## 4.4 Logging Discipline

Avoid:

- Logging inside tight performance loops
- Logging as a substitute for error propagation
- Global singleton loggers without injection mechanisms

------

# 5. Resource Ownership and Lifetime

## 4.5 Logging Abstraction Pattern

Projects **SHOULD** define a minimal logging interface that:

- Represents severity as an enum (or equivalent)
- Accepts a message plus optional structured fields
- Is safe to call from core modules without binding to a specific logging backend

Core modules **SHOULD NOT** depend directly on a concrete logging framework.

---

## 4.6 Logger Injection and Lifetime

Libraries and reusable components **SHOULD** receive a logger via injection:

- Constructor injection is RECOMMENDED.
- Passing a logger explicitly per operation **MAY** be used for short-lived utilities.

Global singleton loggers **SHOULD** be avoided.

If a process-wide logger is required for practical reasons, it **SHOULD** be:

- Initialized exactly once during application bootstrap
- Accessible only through an abstraction boundary
- Replaceable in tests

---

## 4.7 Structured Logging

Structured logging is RECOMMENDED.

Log calls **SHOULD** prefer key/value context over string concatenation when diagnostics benefit.

Examples of useful fields:

- `component`
- `operation`
- `error_code` or `error_category`
- `request_id` / `trace_id`
- `duration_ms`

---

## 4.8 Severity Semantics

Projects **SHOULD** define consistent severity semantics aligned with:

- trace
- debug
- info
- warning
- error
- critical

Severity meaning **SHOULD** be consistent across modules.

---

## 4.9 Logging Discipline

Avoid:

- Logging in tight performance loops without explicit sampling/rate limiting
- Logging as a substitute for error propagation
- Emitting logs from lower-level libraries that the caller cannot control

When logging errors, it is RECOMMENDED to include:

- What failed (operation)
- Why (error code/category/message)
- Relevant identifiers (ids/paths) when safe and appropriate

## 5.1 Explicit Ownership

Ownership semantics **SHOULD** be explicit and visible in APIs.

Use:

- `std::unique_ptr` for exclusive ownership
- `std::shared_ptr` only when shared lifetime is required
- Raw pointers or references for non-owning access

Avoid ambiguous ownership contracts.

------

## 5.2 RAII

Resource lifetime **SHOULD** follow RAII principles.

Avoid separate `init()` / `shutdown()` patterns when RAII can enforce correctness.

------

## 5.3 Lifetime Boundaries

Module boundaries **SHOULD** define:

- Ownership transfer rules
- Destruction responsibilities
- Thread-affinity constraints (if any)

------

# 6. Configuration Management

## 6.1 Layered Configuration

Configuration **SHOULD** support deterministic layering:

1. Built-in defaults
2. Configuration files
3. Environment variables
4. Command-line overrides

Precedence rules should be clearly documented.

------

## 6.2 Immutability

Configuration objects **SHOULD** be immutable after construction.

Mutable global configuration is discouraged.

------

# 7. Dependency Management

## 7.1 Abstraction Boundaries

Modules **SHOULD** depend on abstractions rather than concrete implementations.

Prefer constructor injection over global state.

------

## 7.2 External Dependencies

External dependencies **SHOULD**:

- Be version-pinned
- Be centrally declared
- Avoid leaking into public headers unnecessarily

------

## 7.3 Transitive Coupling

Avoid exposing third-party types in public APIs unless required for performance or correctness.

------

# 8. Concurrency Strategy

## 7.4 Dependency Injection Principle

Modules **SHOULD** receive required dependencies explicitly rather than constructing them internally.

Dependencies **SHOULD NOT** be acquired via:

- Global singletons
- Hidden service locators
- Static global accessors

Explicit dependency passing improves clarity, testability, and lifecycle control.

---

## 7.5 Constructor Injection (Preferred)

Constructor injection is RECOMMENDED for required dependencies.

A component’s constructor **SHOULD**:

- Declare all mandatory dependencies as parameters.
- Avoid performing hidden dependency resolution.
- Establish ownership semantics clearly.

Optional dependencies **MAY** be injected via setters or builder patterns when justified.

---

## 7.6 Composition Root

Applications **SHOULD** define a composition root.

The composition root:

- Constructs concrete implementations.
- Wires dependencies together.
- Transfers fully constructed objects into application logic.

The composition root **SHOULD** exist at application bootstrap level (e.g., in or near `main()`).

Core modules **SHOULD NOT** act as composition roots.

---

## 7.7 Ownership and Lifetime

Injected dependencies **SHOULD** make ownership semantics explicit.

Guidance:

- Exclusive ownership → `std::unique_ptr`
- Shared lifetime → `std::shared_ptr` (only when necessary)
- Non-owning → references or raw pointers (clearly documented)

Ownership transfer **SHOULD** be unambiguous.

---

## 7.8 Interface Boundaries

Modules **SHOULD** depend on abstractions rather than concrete implementations.

Interfaces:

- **SHOULD** be minimal.
- **SHOULD** avoid leaking third-party types when possible.
- **SHOULD** remain stable across minor versions.

Avoid exposing framework-specific types in public APIs unless necessary.

---

## 7.9 Testability

Dependency injection **SHOULD** enable:

- Substitution with test doubles.
- Isolation of infrastructure (logging, persistence, networking).
- Deterministic unit testing.

Hidden global dependencies reduce test isolation and **SHOULD** be avoided.

---

## 7.10 Anti-Patterns (Avoid)

Avoid:

- Implicit singleton access (`get_instance()` patterns).
- Static service locators.
- Modules that both construct and consume infrastructure dependencies.
- Circular dependency graphs.

If a global facility is required (e.g., logging bootstrap), it **SHOULD** be established in the composition root and
passed explicitly thereafter.

## 8.1 Explicit Threading Model

Projects **SHOULD** document:

- Thread ownership
- Synchronization strategy
- Shutdown behavior

Ad-hoc thread spawning should be avoided.

------

## 8.2 Synchronization Discipline

Prefer:

- Scoped locking
- Clear lock ordering
- Minimal lock scope

Hidden blocking in APIs should be avoided.

------

# 9. Testing Strategy

## 9.1 Determinism

Tests **SHOULD** be:

- Deterministic
- Independent
- Free of timing-based flakiness

------

## 9.2 Test Isolation

Tests **SHOULD NOT** depend on:

- Shared global mutable state
- External services without explicit mocking or isolation

------

# 10. Evolution and Amendment

Guidelines may evolve more frequently than the Coding Standard.

Amendments:

- SHOULD include rationale
- SHOULD preserve conceptual clarity
- SHOULD avoid contradicting structural rules
- MAY refine guidance strength

Promotion to the Coding Standard requires a formal amendment under that authority model.

------

# 11. Decision Record (Informative)

This section is non-normative.

It documents:

- Architectural tradeoffs
- Rejected alternatives
- Historical context
- Rationale for guidance strength

It introduces no new requirements.

------

## 11.1 Guideline vs Standard Separation

**Decision:**
 Engineering strategy is separated from structural enforcement.

**Rationale:**
 The Coding Standard governs deterministic structure and formatting.
 The Guidelines govern cross-cutting engineering decisions that may evolve faster and may not be CI-enforceable.

**Consequence:**
 Architectural flexibility is preserved without weakening structural determinism.

------

## 11.2 Error Model Explicitness

**Decision:**
 Projects should explicitly define their primary error model.

**Rationale:**
 Implicit error semantics create inconsistency across modules and reduce maintainability.

**Rejected Alternative:**
 Allow mixed error models across boundaries without restriction.

**Reason for Rejection:**
 Inconsistent error semantics introduce complexity and hidden coupling.

------

## 11.3 Logging Abstraction

**Decision:**
 Core modules should not depend directly on concrete logging frameworks.

**Rationale:**
 Decoupling improves portability and testability.

**Rejected Alternative:**
 Permit global singleton logging access across modules.

**Reason for Rejection:**
 Global state reduces test isolation and increases coupling.

------

## 11.4 Ownership Explicitness

**Decision:**
 Ownership semantics should be visible in APIs.

**Rationale:**
 Ambiguous ownership is a primary source of memory and lifetime defects.

------

## 11.5 Layered Configuration

**Decision:**
 Configuration layering should be deterministic and documented.

**Rationale:**
 Implicit precedence rules create deployment instability.

# Appendix A — Guideline Philosophy (Informative)

These Guidelines:

- Promote architectural clarity.
- Encourage deterministic behavior.
- Avoid over-engineering.
- Preserve portability and maintainability.
- Encourage explicitness over convention.

They intentionally avoid prescribing frameworks, vendors, or platform-specific patterns.

# Appendix B — Domain-Oriented Project Grouping (Informative)

This appendix captures preferred repository grouping guidance for large solutions.

It is organizational guidance only and does not override any structural requirements defined by the C++ Coding Standard.

## B.1 Domain-Oriented Grouping Under `/projects/`

When grouping is used, projects **SHOULD** be organized by domain responsibility (what the code owns) rather than by
artifact category (what it builds into).

Examples of domain groupings:

- `core/`
- `runtime/`
- `services/`
- `product/`
- `devtools/`
- `observability/`

Subdomains **MAY** exist where architecturally justified (e.g., `runtime/platform/`).

Grouping by artifact type such as `libs/` vs `apps/` **SHOULD** be avoided, except where it directly reflects a stable
architectural boundary.

## B.2 Informative Example

```text
/<repo_root>/
    <solution_name>.sln
    Directory.Build.props
    Directory.Build.targets

    /projects/
        /core/
            /foundation/              (project)
            /logging/                 (project)

        /runtime/
            /platform/                (project: portable interfaces)
            /platform_windows/        (project: Windows implementation)
            /platform_posix/          (project: POSIX implementation)
            /ipc/                     (project)

        /services/
            /telemetry/               (project)

        /product/
            /tool_1/                  (project)
            /tool_2/                  (project)

        /devtools/
            /codegen/                 (project)

        /observability/
            /tracing/                 (project)

    /tests/
        /foundation_tests/
        /platform_tests/
```
