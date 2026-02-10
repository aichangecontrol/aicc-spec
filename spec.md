# AICC Specification v1

This document defines the v1 semantics for the AI Change Control (AICC) specification.
It describes the required fields, their types, and validation rules. Implementations
must treat this document and `schema.json` as authoritative.

## Non-goals

- Defining or standardizing an execution engine.
- Specifying AI models, prompts, or agent implementations.
- Defining hosting, control-plane, or approval workflows beyond the data captured
  in the spec.

## Top-level object

An AICC spec is a single YAML document with the fields described below. Unknown
top-level keys are not allowed.

### `version`

- **Type:** integer
- **Required:** yes
- **Semantics:** Major version of the spec. v1 is the initial draft.
- **Validation:** Must be `1` for this specification.
- **Example:**

```yaml
version: 1
```

### `goal`

- **Type:** string
- **Required:** yes
- **Semantics:** Human-readable intent for the change. This is the summary that
  reviewers and auditors should understand without reading implementation details.
- **Validation:** Non-empty string.
- **Example:**

```yaml
goal: "Add vendor confidence gate so 'Needs Attention' doesn't show with <3 invoices"
```

### `scope.allowed_paths`

- **Type:** array of strings
- **Required:** yes
- **Semantics:** The only repository paths where changes are allowed. Paths are
  relative to the repository root and are treated as path prefixes.
- **Validation:** Must include at least one entry. Entries must be non-empty.
- **Example:**

```yaml
scope:
  allowed_paths:
    - backend/
    - frontend/
```

### `guardrails`

- **Type:** object
- **Required:** no
- **Semantics:** Safety constraints that must be enforced by the execution engine.
  Implementations may enforce additional constraints, but those should not appear
  in the spec.
- **Common fields (non-exhaustive):**
  - `require_tests` (boolean)
  - `backward_compatible` (boolean)
  - `no_network` (boolean)
- **Validation:** If present, values must be booleans. Additional guardrail fields
  are allowed and must be booleans.
- **Example:**

```yaml
guardrails:
  require_tests: true
  backward_compatible: true
  no_network: true
```

### `acceptance_criteria`

- **Type:** array of strings
- **Required:** yes
- **Semantics:** Verifiable conditions of success. Each item must be testable
  through evidence (tests, screenshots, metrics, or manual verification).
- **Validation:** Must include at least one entry. Entries must be non-empty.
- **Example:**

```yaml
acceptance_criteria:
  - "With <3 invoices, attention_level = BASELINE"
  - "Volatility displays N/A when gated"
```

### `plan`

- **Type:** array of objects
- **Required:** yes
- **Semantics:** Ordered steps that describe the intended work. Steps may include
  verification commands and approval requirements.
- **Fields:**
  - `step` (string, required): A short description of the step.
  - `verify` (string, optional): Command or method used to verify the step.
  - `approval` (string, optional): If present, must be `required` to indicate
    a human approval gate before proceeding.
- **Validation:** Must include at least one step. At least one step must include
  a `verify` field.
- **Example:**

```yaml
plan:
  - step: "Implement backend confidence gate"
    verify: "pytest"
  - step: "Add migration"
    approval: required
```

### `outputs`

- **Type:** object
- **Required:** yes
- **Semantics:** Evidence artifacts expected from execution. These are produced
  by the engine and attached to the resulting PR or audit record.
- **Fields:**
  - `artifacts` (array of strings, required): Expected evidence types.
- **Validation:** `artifacts` must include at least one entry. Allowed values are:
  `pull_request`, `test_results`, `ui_snapshots`, `audit_log`.
- **Example:**

```yaml
outputs:
  artifacts:
    - pull_request
    - test_results
    - audit_log
```

## Validation requirements

Implementations must validate:

- Schema correctness (required fields and types).
- Semantic constraints described above (e.g., non-empty lists, verify present in
  at least one plan step).
- Internal consistency (e.g., approvals on risky steps may be required by policy
  in some environments, but such policies are outside the spec).

## Versioning

- The `version` field indicates the major version of the spec.
- Minor clarifications and schema tightening that do not break existing valid
  specs may be released as point updates within the same major version.

## Breaking changes policy

Breaking changes require:

- A new major `version` value.
- An RFC (see `rfcs/0001-rfc-template.md`).
- A migration guide that explains how to update existing specs.

