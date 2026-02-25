# Servicing and Maintenance Strategy

**Status:** Authoritative Governance Procedure
**Scope:** Change management for engineering governance documents

------

## 1. Purpose

This document defines how engineering governance artifacts are amended, versioned, consolidated, and archived.

It governs:

- The C++ Coding Standard
- The C++ Engineering Guidelines
- Taxonomy documents
- Dependency rules
- Any future engineering governance artifacts

It defines change mechanics.
Authority hierarchy is defined in the Engineering Governance Framework.

------

## 2. Effective Document Definition

For any governed document:

The effective content consists of:

1. The current baseline document
2. All non-deprecated delta documents associated with it

Deltas:

- Are versioned
- Declare operation type
- Declare target sections
- Supersede baseline content where applicable

If multiple deltas target the same section, the delta with the highest version prevails.

------

## 3. Delta Requirements

Each delta MUST include:

- ID (date + semantic version)
- Operation type (`ADD`, `AMEND`, `REPLACE`, `DELETE`)
- Target section(s)
- Status (`Proposed`, `Accepted`, `Deprecated`)
- Rationale
- Enforcement posture (if changed)

Delta documents MUST reside in the governed document’s `deltas/` directory.

Delta filenames MUST match their internal version ID.

------

## 4. Delta Lifecycle

### 4.1 Proposed

- Not yet effective.
- Under review.

### 4.2 Accepted

- Effective immediately.
- Part of the authoritative standard.

### 4.3 Deprecated

- No longer effective.
- Retained for audit history.

Only `Accepted` deltas affect the effective document state.

------

## 5. Version Precedence

Deltas are applied in ascending semantic version order.

If two deltas modify the same section:

- The delta with the higher version number prevails.
- No partial merging is implied.

Semantic versioning guidelines:

- Major: Structural or architectural change
- Minor: Additive rule or clarification
- Patch: Wording correction or non-normative clarification

------

## 6. Rebase Policy

A rebase consolidates:

- The current baseline
- All accepted, non-deprecated deltas

into a new baseline document.

After rebase:

- The prior baseline and deltas are archived.
- The `deltas/` directory is reset (except template).
- The new baseline becomes authoritative.

------

## 7. Rebase Threshold

A rebase SHOULD occur when:

- Active deltas reach 5 or more, or
- Interpretation becomes ambiguous, or
- Multiple deltas target the same section repeatedly.

The threshold is a governance hygiene rule, not a hard technical constraint.

------

## 8. Archive Requirements

Archived materials MUST:

- Preserve prior baselines and deltas intact.
- Remain immutable.
- Be clearly versioned.

Archives MAY reside:

- Within the engineering repository, or
- In a dedicated documentation repository.

If externalized, a deterministic pointer MUST be maintained.

------

## 9. Immutability

Baselines and accepted deltas are immutable artifacts.

They SHALL NOT be modified directly.

All changes MUST occur through:

- A new delta, or
- A formal rebase event.

------

## 10. Tagging and Versioning

After a rebase or significant governance change:

- A repository tag SHOULD be created (e.g., `v1.0.0`, `v2.0.0`).
- Projects MUST reference tagged versions, not moving branches.

Tags represent governance release points.

------

## 11. Stability Principle

Governance documents evolve deliberately.

Convenience edits that bypass the delta system are prohibited.

------

# End of Document