# Engineering Governance Framework

**Status:** Authoritative Governance Policy
**Scope:** All engineering governance artifacts

------

# 1. Purpose

This document defines the governance model for engineering standards, guidelines, and architectural policy documents within this repository.

It governs:

- The C++ Coding Standard
- The C++ Engineering Guidelines
- Taxonomy documents
- Dependency rules
- Any future engineering governance artifacts

It does not define coding rules or architecture.
It defines how such rules are controlled and interpreted.

------

# 2. Definitions and Conventions

## 2.1 Workspace

`<workspace>` refers to the repository root directory.

It is:

- The Git repository root
- The directory containing the primary Visual Studio solution file (`*.sln`)
- The location of repository-wide configuration files (e.g., `.editorconfig`, `.clang-format`, `Directory.Build.props`)

All repository paths in governance documents are relative to `<workspace>` unless explicitly stated otherwise.

The solution name itself is not normative and may change without affecting governance structure.

------

## 2.2 Project

A **project** is a buildable target located under:

```id="yv8b0x"
<workspace>/projects/<project_name>/
```

Each project conforms to the canonical layout defined by the C++ Coding Standard.

`<project_name>` identifies the build target and namespace root.

------

## 2.3 Path Semantics

Unless otherwise specified:

- Paths beginning with `projects/` are relative to `<workspace>`.
- Paths such as `src/<project_name>/...` are relative to a project root.
- Taxonomy examples are shown relative to a project’s `src/<project_name>/` subtree.

This avoids ambiguity between repository-level and project-level structure.

------

# 3. Authority Hierarchy

Engineering governance follows this precedence order:

1. **C++ Coding Standard** — CI-enforced, structurally authoritative
2. **C++ Engineering Guidelines** — review-enforced guidance
3. **Taxonomy and Dependency Documents** — review-enforced architectural policy

In case of conflict:

- The higher document in the hierarchy prevails.
- Lower documents must not contradict higher ones.

This framework clarifies cross-document authority; it does not redefine subordinate authority models.

------

# 4. Enforcement Model

## 4.1 Coding Standard

- CI is the final enforcement authority.
- Structural and formatting violations result in build rejection.

## 4.2 Engineering Guidelines

- Enforcement is review-driven.
- Deviations require documented justification.
- CI does not reject builds for guideline variance unless explicitly escalated.

## 4.3 Taxonomy and Dependency Rules

- Enforcement is review-driven.
- CI checks MAY be introduced for deterministic validation (e.g., include-graph validation, forbidden dependency edges).
- Architectural layering must be preserved.

------

# 5. Document Immutability and Change Control

Baseline documents and accepted deltas are immutable artifacts.

They **SHALL NOT** be modified directly.

All changes to governance documents **MUST** occur through:

- A versioned delta document, or
- A formal rebase event under the Servicing and Maintenance Strategy (where applicable).

Inline edits, silent rewrites, or informal modifications are prohibited.

Automation, agents, and contributors **MUST NOT** alter governance documents without explicit instruction from a designated maintainer.

------

# 6. Change Mechanisms

Each governed document maintains its own:

- Authority model
- Delta directory
- Versioning scheme
- Rebase strategy (if applicable)

This framework does not replace those mechanisms; it ensures they are respected.

------

# 7. Stability Principles

Engineering governance exists to preserve:

- Deterministic structure
- Architectural clarity
- Auditability of change
- Long-term maintainability

Convenience edits that weaken structural determinism are prohibited.

------

# 8. Agent and Automation Policy

Automated systems and AI agents:

- Must treat governance documents as read-only unless explicitly instructed to create a delta.
- Must not rewrite baseline documents.
- Must not introduce structural changes without formal amendment.
- Must respect authority hierarchy when resolving conflicts.

------

# 9. Non-Goals

This document does not:

- Define coding style
- Define project layout rules
- Define naming conventions
- Define taxonomy segmentation
- Define dependency direction
- Override subordinate standards

It governs governance only.

------

# Why This Version Is Correct

- Introduces `<workspace>` cleanly.
- Removes product coupling.
- Removes solution-name coupling.
- Clarifies path semantics once.
- Avoids duplication.
- Strengthens immutability without redundancy.
- Keeps hierarchy explicit.
- Remains minimal and precise.
