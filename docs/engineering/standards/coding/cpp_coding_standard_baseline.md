# C++ Coding Standard

**Status:** Authoritative  
**Supersedes:**  
- C++ Coding Style Policy  
- C++ Project Structure Policy  
- Non-Syntax / Tooling Policy  

---

## 1. Document Authority and Scope

### 1.1 Status

This document is the single authoritative C++ engineering standard.  
It consolidates previously published policies without altering their normative meaning.

No previously locked **SHALL** requirement is weakened by consolidation.

### Governance Framework Reference

This document operates within the repository’s Engineering Governance Framework.

See:

```
docs/engineering/engineering_governance.md
```

The Governance Framework defines authority hierarchy, enforcement posture, and document immutability requirements across all engineering policy artifacts.

In case of ambiguity regarding cross-document authority or change control, the Governance Framework applies.

### 1.2 Scope

This standard governs:

- Project structure
- File organization
- Naming conventions
- Formatting
- Language usage discipline
- Tooling and enforcement

It does not govern:

- Domain modeling
- Business logic design
- Architectural decisions beyond structural discipline

### 1.3 Normative Language

The keywords **SHALL**, **MUST**, **SHOULD**, and **MAY** retain their previously defined meanings.

- **SHALL** indicates a mandatory requirement.
- **SHOULD** indicates strong recommendation with limited justified exceptions.
- **MAY** indicates optional behavior.

---

## 2. Governance and Enforcement

### 2.1 CI Authority

CI is the final enforcement authority for this standard.

CI **SHALL** reject builds for:

- Formatting violations
- Structural rule violations
- Compilation warnings
- Compilation failures

Standards without enforcement degrade. This standard is CI-enforced.

### 2.2 Tooling Hierarchy

Authoritative tooling hierarchy (highest structural authority first):

1. `.editorconfig` — indentation, encoding, line endings, whitespace  
2. `.clang-format` — formatting and include handling  
3. Centralized MSBuild properties — compiler settings and build behavior  
4. `.gitattributes` — line-ending normalization and text handling  

IDE configuration **MUST** conform to repository configuration.  
IDE preferences **SHALL NOT** override repository policy.

### 2.3 Warnings-as-Errors

All compiler warnings **SHALL** be treated as errors.

Projects **SHALL NOT**:

- Lower warning levels
- Globally disable warnings
- Relax treat-warnings-as-errors

Local suppression requires explicit justification and minimal scope.

Compiler configuration **SHALL**:

- Use a high warning level equivalent to `/W4`
- Enable standards-conforming mode (`/permissive-`)
- Enable the standard preprocessor
- Enable security development lifecycle checks
- Compile as UTF-8

Compiler policy **SHALL** be centralized via shared MSBuild property files.

### 2.4 Conformance Definition

A project conforms if and only if:

- It compiles with zero warnings
- It passes formatting checks
- It adheres to structural layout rules
- It respects include discipline rules
- It does not violate public surface containment

Conformance is determined by CI.

---

## 3. Language Version Policy

### 3.1 Minimum Standard

Minimum supported language standard: **C++17**.

All projects **SHALL** compile in standards-conforming mode.

### 3.2 Default Standard

Default language standard: **C++17**, unless explicitly elevated.

Use of **C++20** (or newer) features:

- **SHALL** be explicitly declared at the project level.
- **SHALL NOT** be introduced implicitly into C++17 projects.
- **SHOULD** be justified during migration or architectural review.

Projects that opt into C++20 **SHALL** document this elevation in their project configuration.

The compiler **SHALL** operate in standards-conforming mode.  
Source files **SHALL** be compiled as UTF-8.

### 3.3 Migration Discipline

During transition from C++17 to C++20:

- New code **SHOULD NOT** introduce C++20-only features into C++17 projects.
- Standard library facilities unavailable in C++17 (e.g., `std::ranges`, `std::format`, `std::span`) **SHALL NOT** be used unless the project explicitly targets C++20.
- Version elevation **SHALL** be deliberate and repository-visible.

This policy ensures deterministic migration and prevents accidental feature leakage across projects.

### 3.4 Version Independence Constraint

This standard is minimally tied to language version.  
Structural and formatting rules **SHALL NOT** depend on optional language features.

---

## 4. Project Structure Requirements

### 4.1 Repository Layout

Canonical layout:

- `/include/<project_name>/`
- `/src/<project_name>/`
- `/src/<project_name>/detail/`
- `/build/`

No alternate structural patterns are permitted.

Tests **SHALL** reside outside the public include tree.

#### 4.1.1 Canonical Solution Layout Example (Informative)

The following example illustrates a canonical repository layout for a solution containing multiple projects.
This example is informative and does not introduce additional requirements beyond those already specified.

```text
/<repo_root>/
    .editorconfig
    .gitattributes
    .gitignore
    .clang-format
    Directory.Build.props
    Directory.Build.Targets
    <solution_name>.sln

    /docs/
        cpp_engineering_standard.md
        cpp_engineering_standard_decision_record.md

    /build/                          (generated; git-ignored)

    /projects/
        /foo/                         (project)
        /bar/                         (project)

    /tests/
        /foo_tests/
        /bar_tests/
```

Notes:

- Solution-wide policy **SHALL** remain centralized in repository-root configuration files.
- Projects **SHALL NOT** duplicate or override centralized compiler policy.

#### 4.1.2 Canonical Project Layout Example (Informative)

The following example illustrates a canonical layout for a single project named `foo`. This example is
informative and does not introduce additional requirements beyond those already specified.

```text
/projects/foo/
    foo.vcxproj
    foo.props                       (project-specific deltas only; optional)

    /include/foo/
        foo_api.h
        widget.h

    /src/foo/
        widget.cpp
        detail/
            widget_impl.h
            widget_impl.cpp
```

Notes:

- The `include/foo/` tree defines the public API surface for `foo`.
- The `src/foo/detail/` tree contains internal implementation headers and sources.
- Tests **SHALL** reside outside the public include tree.

### 4.2 Public Header Placement

Public headers **SHALL** reside under:

- `include/<project_name>/`

This directory defines the entire public API surface.

Public headers **MUST** compile independently.

### 4.3 Private Header Placement

Private headers **SHALL** reside under:

- `src/<project_name>/detail/`

Private headers **SHALL NOT** be included from public headers.

### 4.4 Namespace–Directory Alignment

Namespace hierarchy **MUST** mirror directory hierarchy.

Example:

- `include/foo/bar/baz.h` → `namespace foo::bar`

This rule is mandatory and CI-enforced.

### 4.5 Build Artifact Containment

All generated artifacts **SHALL** reside under:

- `/build`

No `obj/`, `bin/`, or intermediate artifacts may exist elsewhere.

The `/build` directory is git-ignored.

### 4.6 File Extensions

- Headers: `.h`
- Sources: `.cpp`

Rejected alternatives:

- `.hpp`
- `.cc`
- Mixed extension conventions

### 4.7 Header Policy

Headers **SHALL** use:

- `#pragma once`

Traditional include guards **SHALL NOT** be combined with `#pragma once`.

Each `.cpp` file **SHALL** include its corresponding header first.

### 4.8 Directory and File Naming

#### 4.8.1 Directories

Directories **SHALL** use `lower_snake_case`.

Directory names **SHALL**:

- Use only lowercase letters (`a-z`), digits (`0-9`), and underscores (`_`)
- Use underscores as word separators
- Contain no spaces
- Contain no hyphens
- Avoid mixed casing

Examples:

- `detail`
- `memory_pool`
- `platform_win32`

#### 4.8.2 Files

Source and header filenames **SHALL** use `lower_snake_case`.

Filenames **SHALL**:

- Use only lowercase letters (`a-z`), digits (`0-9`), underscores (`_`), and the extension dot (`.`)
- Use underscores as word separators
- Contain no spaces
- Contain no hyphens
- Avoid mixed casing

Examples:

- `widget.h`
- `widget.cpp`
- `widget_impl.h`
- `widget_impl.cpp`

Filenames SHOULD contain exactly one dot separating the basename and extension.

Additional dots in filenames are permitted but strongly discouraged.

#### 4.8.3 Extensions

File extensions **SHALL** conform to §4.6:

- Headers: `.h`
- Sources: `.cpp`

#### 4.8.4 Namespace Alignment

When namespaces map to directories, the directory name **SHALL** match the namespace segment exactly, consistent with §4.4.

Example:

```text
namespace foo::memory_pool
→ directory: foo/memory_pool/
```

#### 4.8.5 Project and solution files

Project and solution filenames **SHALL** use `lower_snake_case`.

This applies to:

- Solution files: `<name>.sln`
- MSBuild project files: `<name>.vcxproj`, `<name>.vcxproj.filters`, `<name>.vcxproj.user`
- Project-specific property files: `<name>.props`, `<name>.targets` (when used per-project)

The same rules as §4.8.2 apply: lowercase letters, digits, underscores only; no spaces; no hyphens.

**Exception:** The well-known MSBuild file names `Directory.Build.props` and `Directory.Build.Targets` at the repository root **MAY** use the tooling’s conventional PascalCase; they are excluded from the lower_snake_case requirement for tool compatibility.

Examples:

- `foundation.vcxproj`
- `platform_windows.vcxproj`
- `enforcer.sln`

---

## 5. Include and Dependency Discipline

### 5.1 Self-Header-First

Each `.cpp` file **SHALL** include its corresponding header first to enforce header self-sufficiency.

### 5.2 Header Self-Sufficiency

Public headers **MUST** compile independently.

They **SHALL NOT** rely on:

- Transitive includes
- Include order
- Other compilation units

### 5.3 Include Group Ordering

Includes **SHALL** be grouped in this order:

1. Self header
2. Standard library
3. Third-party libraries
4. Project headers

A single blank line **SHALL** separate groups.

Alphabetical ordering within a group **SHOULD** be used.

Formatting and grouping **SHALL** be enforced via `.clang-format`.

### 5.4 Explicit Includes

Code **SHALL NOT** rely on transitive includes.  
All dependencies **MUST** be explicitly included.

### 5.5 Public Header Restrictions

Public headers **SHALL NOT** include:

- `detail/` headers
- Platform-specific headers
- Heavy implementation headers unless strictly required

Public surface **SHALL** remain minimal and stable.

### 5.6 Internal Linkage

File-local entities **SHALL** use anonymous namespaces.

The `static` keyword for internal linkage is prohibited.

### 5.7 `using namespace` Restrictions

`using namespace` is:

- Prohibited in headers
- Restricted and deliberate in source files

Public headers **SHALL NOT** introduce namespace pollution.

---

## 6. Naming Conventions

### 6.1 Namespaces

Namespaces **SHALL** use `lower_snake_case`.

### 6.2 Types

Classes, structs, enums, and type aliases **SHALL** use `PascalCase`.

### 6.3 Functions

Free and member functions **SHALL** use `lower_snake_case`.

### 6.4 Variables

Parameters and local variables **SHALL** use `lower_snake_case`.

### 6.5 Private Members

Private data members **SHALL** use `lower_snake_case_` (trailing underscore).

Rejected:

- `m_` prefix
- Leading underscore

### 6.6 Enumerations

Enumerations **SHALL** use `enum class`.  
Enumerators **SHALL** use `PascalCase`.

### 6.7 Traits

Traits and metafunctions **SHALL** use:

- `is_xxx`
- `is_xxx_v`
- `xxx_t`

### 6.8 Factories

Factory functions **SHALL** use:

- `make_xxx()`

### 6.9 Constants

Constants **SHALL**:

- Use `inline constexpr`
- Follow `lower_snake_case`

### 6.10 Macros

Macros **SHALL** use `ALL_UPPER_CASE`.  
Macros **SHALL** be used sparingly.

---

## 7. Language Usage Rules

### 7.1 Const Correctness

Observational methods **SHALL** be marked `const`.

### 7.2 `noexcept` Policy

Functions that do not throw **SHALL** be marked `noexcept`.

### 7.3 `[[nodiscard]]`

`[[nodiscard]]` **SHALL** be used where ignoring the return value is likely a defect.

### 7.4 `constexpr`

`constexpr` **SHALL** be used when natural and beneficial.

Compile-time overengineering is prohibited.

### 7.5 Destructor Policy

Destructors **SHALL NOT** throw.

---

## 8. Formatting Rules

Formatting **SHALL** be machine-enforced via `.clang-format` and `.editorconfig`.

### 8.1 Brace Style

Allman brace style is mandatory.

Opening braces **SHALL** appear on their own line for:

- Namespaces
- Classes
- Functions
- Control blocks
- Lambdas

### 8.2 Indentation

- 4 spaces
- No tabs

### 8.3 Line Endings

All files **SHALL** use **LF** line endings.

### 8.4 Encoding

All files **SHALL** use **UTF-8 without BOM**.

### 8.5 Whitespace

Mandatory:

- No trailing whitespace
- Final newline at end of file
- No mixed indentation

Maximum line width **SHOULD NOT** exceed 120 characters.

### 8.6 Include Formatting

`.clang-format` **SHALL** enforce:

- Group separation
- Deterministic include handling
- Stable formatting behavior

Manual divergence is prohibited.

---

## 9. Tooling Configuration

### 9.1 `.editorconfig`

Authoritative for:

- Indentation
- Line endings
- Encoding
- Whitespace

### 9.2 `.clang-format`

Authoritative for:

- Brace style
- Indentation width
- Alignment
- Include formatting
- Column width

### 9.3 Build System Properties

Centralized via:

- `Directory.Build.props`
- `Directory.Build.Targets`
- Shared `.props` files

Projects **SHALL NOT** duplicate or override centralized compiler policy.

### 9.4 Repository Hygiene Files

`.gitattributes` **SHALL** normalize line endings.  
`.gitignore` **SHALL** exclude `/build` and generated artifacts.

---

## 10. Conformance and Exceptions

### 10.1 Definition of Conformance

Conformance requires:

- Zero warnings
- Successful CI
- No formatting violations
- Structural compliance

### 10.2 CI Failure Handling

CI failure blocks integration.  
Violations **SHALL** be corrected before merge.

### 10.3 Review Enforcement

Code review **SHALL** enforce:

- Namespace-directory alignment
- Public surface containment
- Include discipline
- Structural layout

### 10.4 Exception Approval Process

Exceptions:

- **MUST** be explicitly justified
- **MUST** be minimal in scope
- **MUST NOT** weaken structural determinism
- **MUST** be documented

---

## 11. Amendment and Evolution

### 11.1 Principles

Future changes **SHALL**:

- Preserve deterministic formatting
- Preserve surface containment
- Preserve CI enforceability
- Avoid IDE lock-in
- Favor mainstream compatibility

### 11.2 Locked Decisions

Explicitly locked (per Decision Record):

- Allman brace style
- 4-space indentation
- LF everywhere
- UTF-8 (no BOM)
- `enum class` required
- Anonymous namespace over `static`
- No `using namespace` in headers
- Warnings as errors
- Namespace-directory alignment mandatory
- `/build` containment
- `.h` / `.cpp` only
- SHALL-based standard

### 11.3 Rejected Alternatives

Rejected:

- `.hpp` extensions
- `m_` member prefix
- Mixed brace styles
- Tabs for indentation
- Transitive include reliance
- IDE-dependent enforcement

### 11.4 Change Process

Amendments **MUST**:

- Be explicitly versioned
- Preserve locked decisions unless formally overturned
- Include rationale
- Be reviewed prior to adoption

---

## 12. Decision Record (Informative)

This section is non-normative.

It preserves:

- Rationale
- Tradeoffs
- Historical context
- Explicit locked decisions

It **SHALL NOT** introduce new requirements.

---

# Appendix A. Canonical C++ Example (Informative)

This example illustrates compliance with major rules in this standard, including:

- `.h` / `.cpp` usage
- `#pragma once`
- Include ordering and grouping
- No `using namespace` in headers
- Namespace + directory alignment
- PascalCase types; lower_snake_case functions/variables; trailing underscore private members
- `enum class` and PascalCase enumerators
- `[[nodiscard]]` usage
- const correctness
- `noexcept` usage
- Anonymous namespace for file-local helpers
- Allman braces, 4-space indentation

## A.1 Public Header Example (`include/foo/widget.h`)

```cpp
#pragma once

#include <cstddef>
#include <string>

namespace foo
{

    enum class WidgetState
    {
        Idle,
        Active,
        Failed
    };

    struct WidgetConfig
    {
        std::size_t max_items = 0;
        bool enable_feature = false;
    };

    class Widget
    {
    public:
        explicit Widget(WidgetConfig config);

        [[nodiscard]] WidgetState state() const noexcept;
        [[nodiscard]] std::size_t item_count() const noexcept;

        void activate() noexcept;

        [[nodiscard]] bool try_add_item(std::string item);

    private:
        WidgetConfig config_;
        WidgetState state_ = WidgetState::Idle;
        std::size_t item_count_ = 0;
    };

}
```

## A.2 Private Header Example (`src/foo/detail/widget_impl.h`)

```cpp
#pragma once

#include <string>
#include <vector>

namespace foo::detail
{

    class widget_impl
    {
    public:
        explicit widget_impl(bool enable_feature);

        void activate() noexcept;

        [[nodiscard]] bool try_add_item(std::string item);
        [[nodiscard]] std::size_t item_count() const noexcept;

    private:
        bool enable_feature_ = false;
        std::vector<std::string> items_;
    };

}
```

## A.3 Source Example (`src/foo/widget.cpp`)

```cpp
#include <foo/widget.h>

#include <utility>

#include "foo/detail/widget_impl.h"

namespace
{

    [[nodiscard]] bool is_valid_item(const std::string& item) noexcept
    {
        return !item.empty();
    }

}

namespace foo
{

    class Widget::impl final
    {
    public:
        explicit impl(bool enable_feature)
            : inner_(enable_feature)
        {
        }

        void activate() noexcept
        {
            inner_.activate();
        }

        [[nodiscard]] bool try_add_item(std::string item)
        {
            return inner_.try_add_item(std::move(item));
        }

        [[nodiscard]] std::size_t item_count() const noexcept
        {
            return inner_.item_count();
        }

    private:
        detail::widget_impl inner_;
    };

    Widget::Widget(WidgetConfig config)
        : config_(config)
    {
    }

    [[nodiscard]] WidgetState Widget::state() const noexcept
    {
        return state_;
    }

    [[nodiscard]] std::size_t Widget::item_count() const noexcept
    {
        return item_count_;
    }

    void Widget::activate() noexcept
    {
        state_ = WidgetState::Active;
    }

    [[nodiscard]] bool Widget::try_add_item(std::string item)
    {
        if (!is_valid_item(item))
        {
            state_ = WidgetState::Failed;
            return false;
        }

        ++item_count_;
        return true;
    }

}
```

Note: The sample intentionally avoids `using namespace`, uses an anonymous namespace for file-local
helpers, and uses Allman formatting throughout.

---

## Appendix A.4 — Rule Coverage Matrix (Informative)

This matrix identifies which sections of the standard are illustrated by the canonical examples in Appendix A.
It is provided for clarity and review traceability.  
It introduces no new normative requirements.

### A.4.1 Public Header Example (`include/foo/widget.h`)

Demonstrates compliance with:

- 4.2 Public Header Placement
- 4.4 Namespace–Directory Alignment
- 4.6 File Extensions (`.h`)
- 4.7 `#pragma once` usage
- 5.2 Header Self-Sufficiency
- 5.7 No `using namespace` in headers
- 6.1 Namespace naming (`lower_snake_case`)
- 6.2 Type naming (PascalCase)
- 6.3 Function naming (`lower_snake_case`)
- 6.4 Variable naming (`lower_snake_case`)
- 6.5 Private member naming (`lower_snake_case_`)
- 6.6 `enum class` usage and enumerator casing
- 6.9 `inline constexpr`-style constant pattern (implicit via config defaults)
- 7.1 Const correctness
- 7.2 `noexcept` policy
- 7.3 `[[nodiscard]]` usage
- 8.1 Allman brace style
- 8.2 4-space indentation

### A.4.2 Private Header Example (`src/foo/detail/widget_impl.h`)

Demonstrates compliance with:

- 4.3 Private header placement under `detail/`
- 4.4 Namespace–Directory alignment (`foo::detail`)
- 4.6 File Extensions (`.h`)
- 6.2 Type naming (PascalCase and internal class naming conventions)
- 6.5 Private member trailing underscore
- 7.1 Const correctness
- 7.2 `noexcept` usage
- 8.1–8.2 Formatting rules

### A.4.3 Source Example (`src/foo/widget.cpp`)

Demonstrates compliance with:

- 4.6 File Extensions (`.cpp`)
- 4.7 / 5.1 Self-header-first include discipline
- 5.3 Include grouping and blank-line separation
- 5.4 Explicit include usage
- 5.6 Anonymous namespace for internal linkage
- 5.7 No `using namespace`
- 6.3 Function naming (`lower_snake_case`)
- 6.5 Private member trailing underscore
- 6.6 `enum class` usage
- 7.1 Const correctness
- 7.2 `noexcept` usage
- 7.3 `[[nodiscard]]` usage
- 8.1–8.5 Formatting, indentation, line discipline

---

## Appendix A.5 — Conformance Characteristics Demonstrated (Informative)

The canonical example collectively demonstrates:

- Public/private surface containment
- Deterministic include structure
- Namespace-directory alignment
- Modern C++ contract discipline (`const`, `noexcept`, `[[nodiscard]]`)
- Enforced formatting determinism
- Anonymous namespace usage over `static`
- No namespace pollution in headers

The example is intentionally minimal but structurally representative.
