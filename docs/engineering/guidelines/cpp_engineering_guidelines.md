## C++ Engineering Guidelines

### Authority Model

This document defines the authority and amendment model for the C++ Engineering Guidelines.

The effective Guidelines consist of:

1. `cpp_engineering_guidelines_baseline.md`
2. All non-deprecated delta documents located in the `deltas/` directory adjacent to this file.

Delta documents:

- Are versioned.
- Declare an operation type (`ADD`, `AMEND`, `REPLACE`, `DELETE`).
- Explicitly declare their target section(s).
- Supersede the baseline where they apply.
- May elevate, clarify, or refine guidance strength (e.g., RECOMMENDED → SHOULD).

If multiple deltas affect the same target, the delta with the higher version prevails.

The `deltas/` directory constitutes the complete amendment history.

**Rebase:** 2026-02-25-v2.0.0 consolidated prior deltas into the baseline. Post-rebase deltas live in `deltas/`.

### Governance Framework Reference

These Guidelines operate within the repository’s Engineering Governance Framework.

See:

```
docs/engineering/engineering_governance.md
```

The Governance Framework defines document immutability, enforcement posture, and cross-document authority relationships.

In case of conflict, the C++ Coding Standard prevails in accordance with the Governance Framework.

------

# 1. Authority Classification

The Engineering Guidelines are:

- Authoritative guidance
- Review-enforced
- Non-CI-enforced (unless explicitly escalated)

They complement but do not override the C++ Coding Standard.

In case of conflict, the Coding Standard prevails.

------

# 2. Normative Strength Model

This document uses the following normative keywords:

- **SHOULD** — Strong recommendation; deviation requires justification.
- **RECOMMENDED** — Preferred practice.
- **AVOID** — Strongly discouraged.
- **MAY** — Optional.

The keyword **SHALL** is reserved for safety, correctness, or escalation into the Coding Standard.

------

# 3. Escalation Model

Guidelines MAY be promoted to the Coding Standard when:

- Deterministic enforcement becomes possible, or
- Architectural uniformity becomes mandatory, or
- Safety/correctness requires structural enforcement.

Promotion requires:

- Formal versioned amendment
- Rationale
- Review approval

------

# 4. Conformance Model

A project is considered guideline-conformant if:

- Deviations are documented.
- Architectural decisions are deliberate.
- Review consensus accepts variance.

Unlike the Coding Standard:

- CI does not reject builds for guideline deviation.
- Enforcement is architectural and review-driven.

------

# 5. Directory Structure

Recommended layout:

```
docs/
    cpp_engineering_guidelines/
        cpp_engineering_guidelines.md              (authority bootstrap)
        cpp_engineering_guidelines_baseline.md
        deltas/
            2026-xx-xx-v1.1.0-some-amendment.md
```

------

# Design Rationale (Non-Normative)

This model:

- Preserves deterministic amendment history
- Maintains governance symmetry with the Coding Standard
- Prevents guideline drift
- Allows controlled evolution without structural instability