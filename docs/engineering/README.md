# Engineering Governance Repository

This repository is the authoritative source of engineering governance artifacts.

All engineering standards, guidelines, and architectural policies are defined here.

Projects MUST reference a tagged version of this repository when adopting governance.

------

## Current Governance Model

Engineering governance is structured as follows:

1. **C++ Coding Standard** — CI-enforced, structurally authoritative
2. **C++ Engineering Guidelines** — review-enforced guidance
3. **Taxonomy and Dependency Rules** — review-enforced architectural policy

Hierarchy and immutability rules are defined in:

```
docs/engineering/governance/engineering_governance.md
```

Change mechanics (delta lifecycle, version precedence, rebase policy) are defined in:

```
docs/engineering/governance/servicing_and_maintenance_strategy.md
```

------

## Repository Structure

```
docs/engineering/
    README.md
    governance/
        engineering_governance.md
        servicing_and_maintenance_strategy.md
    standards/
        coding/
            cpp_coding_standard.md
            cpp_coding_standard_baseline.md
            deltas/
    guidelines/
        cpp/
            cpp_engineering_guidelines.md
            cpp_engineering_guidelines_baseline.md
            deltas/
    taxonomy/
    layering/
```

------

## Adoption by Projects

Projects MUST:

1. Reference a tagged version (e.g., `v1.0.1`), not `master`.
2. Declare the adopted governance version explicitly.
3. Treat governance artifacts as immutable.

Recommended raw reference pattern:

```
https://raw.githubusercontent.com/<owner>/engineering/<tag>/<path>
```

Example:

```
https://raw.githubusercontent.com/FlemishLion1302/engineering/v1.0.1/docs/engineering/governance/engineering_governance.md
```

------

## Versioning

- Tags represent governance release points.
- Governance evolves via versioned deltas.
- Rebase events consolidate baselines.
- Tags SHOULD be created after significant governance changes.

------

## Immutability

Governance documents are immutable.

Changes MUST occur through:

- A versioned delta, or
- A formal rebase event.

Informal edits are prohibited.

------

# End of Document