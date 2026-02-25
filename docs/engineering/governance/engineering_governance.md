# Engineering Governance Framework

**Status:** Authoritative Governance Policy
**Scope:** All engineering governance artifacts

------

## 1. Purpose

This document defines the governance model for engineering standards, guidelines, and architectural policy documents within this repository.

It governs:

- The C++ Coding Standard
- The C++ Engineering Guidelines
- Taxonomy documents
- Dependency rules
- Any future engineering governance artifacts

This document does not define coding rules or architecture.
It defines how such rules are controlled, versioned, and interpreted.

------

## 2. Definitions and Conventions

### 2.1 `<workspace>`

`<workspace>` refers to the root directory of a repository that adopts these engineering documents.

It represents:

- The Git repository root
- The directory containing the primary solution file (if applicable)
- The location of repository-wide configuration files

All repository paths in governance documents are relative to `<workspace>` unless explicitly stated otherwise.

The specific solution or product name is not normative and may change without affecting governance structure.

------

### 2.2 Project

A **project** is a buildable target located under:

```
<workspace>/projects/<project_name>/
```

Each project must conform to the canonical layout defined by the C++ Coding Standard.

`<project_name>` identifies the build boundary and namespace root.

------

### 2.3 Path Semantics

Unless otherwise specified:

- Paths beginning with `projects/` are relative to `<workspace>`.
- Paths such as `src/<project_name>/...` are relative to a project root.
- Taxonomy examples are shown relative to a project’s `src/<project_name>/` subtree.

This prevents ambiguity between repository-level and project-level structure.

------

## 3. Authority Hierarchy

Engineering governance follows this precedence order:

1. **C++ Coding Standard** — CI-enforced, structurally authoritative
2. **C++ Engineering Guidelines** — review-enforced guidance
3. **Taxonomy and Dependency Documents** — review-enforced architectural policy

In case of conflict:

- The higher document in the hierarchy prevails.
- Lower documents must not contradict higher ones.

This framework clarifies cross-document authority.
It does not override or redefine subordinate authority models.

------

## 4. Enforcement Model

### 4.1 Coding Standard

- CI is the final enforcement authority.
- Structural and formatting violations result in build rejection.

### 4.2 Engineering Guidelines

- Enforcement is review-driven.
- Deviations require documented justification.
- CI does not reject builds for guideline variance unless explicitly escalated.

### 4.3 Taxonomy and Dependency Rules

- Enforcement is review-driven.
- Deterministic validation may be introduced via CI where appropriate.
- Architectural layering and platform boundaries must be preserved.

------

## 5. Document Immutability

Baseline documents and accepted deltas are immutable artifacts.

They SHALL NOT be modified directly.

All changes to governance documents MUST occur through:

- A versioned delta document, or
- A formal rebase event defined by the Servicing and Maintenance Strategy.

Inline edits, silent rewrites, or informal modifications are prohibited.

Automation, agents, and contributors MUST NOT alter governance documents without explicit instruction to create a formal amendment.

------

## 6. Servicing and Maintenance

The procedures for:

- Delta structure
- Version precedence
- Deprecation
- Rebase events
- Thresholds for consolidation

are defined in:

```
docs/engineering/governance/servicing_and_maintenance_strategy.md
```

This document defines governance structure; the servicing strategy defines change mechanics.

------

## 7. Stability Principles

Engineering governance exists to preserve:

- Deterministic structure
- Architectural clarity
- Auditability of change
- Long-term maintainability

Convenience edits that weaken structural determinism are prohibited.

------

## 8. Non-Goals

This document does not:

- Define coding style
- Define naming conventions
- Define project layout rules
- Define taxonomy segmentation
- Define dependency direction
- Introduce new technical requirements

It governs governance only.

------

# End of Document
