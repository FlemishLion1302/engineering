# C++ Engineering Guidelines

## Authority Model

This document operates within the Engineering Governance Framework:

- `docs/engineering/governance/engineering_governance.md`

Change mechanics (delta lifecycle, precedence, rebase policy) are defined in:

- `docs/engineering/governance/servicing_and_maintenance_strategy.md`

This document uses the baseline + deltas authority model defined in the Servicing and Maintenance Strategy.

Effective content consists of:

- `cpp_engineering_guidelines_baseline.md`
- Accepted non-deprecated deltas under `deltas/`

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

# Design Rationale (Non-Normative)

This model:

- Preserves deterministic amendment history
- Maintains governance symmetry with the Coding Standard
- Prevents guideline drift
- Allows controlled evolution without structural instability