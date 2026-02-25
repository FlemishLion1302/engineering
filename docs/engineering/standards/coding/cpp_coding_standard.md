# C++ Coding Standard

## Authority Model

This document defines the authority and amendment model for the C++ Coding Standard.

The effective standard consists of:

1. `cpp_coding_standard_baseline.md`
2. All non-deprecated delta documents located in the `deltas/` directory adjacent to this file.

Delta documents:

- Are versioned.
- Declare an operation type (ADD, AMEND, REPLACE, DELETE).
- Explicitly declare their target section(s).
- Supersede the baseline where they apply.

If multiple deltas affect the same target, the delta with the higher version prevails.

The `deltas/` directory constitutes the complete amendment history.