# AI Change Control — Specification (aicc-spec)

**An open specification for governing AI-generated changes.**

AI Change Control (AICC) defines a **spec-first contract** for how AI agents are allowed to make changes in codebases and systems. The specification is open and implementation-agnostic. Anyone can implement it.

> **Open contract. Closed control planes.**
> The spec and validator are open and will remain open. Hosted execution engines may be proprietary.

---

## What this repository is

This repo contains:

* The **AICC specification** (human-readable semantics)
* The **machine-readable schema** for validation
* **Examples** of valid specs

This repo does **not** contain:

* Any execution engine
* Any AI models or prompts
* Any GitHub App or hosted service

---

## Why this exists

AI can now make real changes — code, infrastructure, data, workflows. Traditional prompts are flexible but **not auditable or enforceable**.

AICC exists to provide:

* **Intent** — what change is allowed
* **Scope** — where changes may occur
* **Guardrails** — constraints that must be enforced
* **Verification** — how correctness is proven
* **Approvals** — when humans must intervene

This mirrors how infrastructure evolved from scripts → declarative contracts (e.g., Terraform).

---

## Design principles

1. **Declarative, not imperative**
   Specs describe *what must be true*, not *how to do it*.

2. **Enforceable by design**
   Anything in the spec must be objectively checkable.

3. **Execution-agnostic**
   The same spec can be implemented by different engines.

4. **Audit-first**
   Specs are contracts that must stand up months later.

5. **Minimal surface area**
   Fewer fields, clearer semantics, fewer footguns.

---

## The spec at a glance

```yaml
version: 1

goal: "Add vendor confidence gate so 'Needs Attention' doesn't show with <3 invoices"

scope:
  allowed_paths:
    - backend/
    - frontend/

guardrails:
  require_tests: true
  backward_compatible: true
  no_network: true

acceptance_criteria:
  - "With <3 invoices, attention_level = BASELINE"
  - "Volatility displays N/A when gated"
  - "Filters include BASELINE"

plan:
  - step: "Implement backend confidence gate"
    verify: "pytest"
  - step: "Update frontend UI"
    verify: "pnpm test"
  - step: "Add migration"
    approval: required

outputs:
  artifacts:
    - pull_request
    - test_results
    - audit_log
```

---

## Field semantics (high level)

| Field                 | Meaning                                       |
| --------------------- | --------------------------------------------- |
| `goal`                | Human-readable intent of the change           |
| `scope`               | Where changes are allowed                     |
| `guardrails`          | Safety constraints that must be enforced      |
| `acceptance_criteria` | Verifiable conditions of success              |
| `plan`                | Ordered steps with verification and approvals |
| `outputs`             | Evidence artifacts expected from execution    |

Full semantics are defined in **spec.md**.

---

## Validation

Implementations **must** validate:

* Schema correctness
* Semantic correctness (e.g., approvals on risky steps)
* Internal consistency

See the `schema.json` file for the authoritative schema.

---

## Versioning

* The spec is versioned via the `version` field.
* Breaking changes require a new major version.
* Backward compatibility is a core goal.

---

## Governance

This specification is maintained in the open.

* Issues and proposals are discussed publicly.
* Breaking changes require an RFC.
* The maintainers act as neutral stewards of the spec.

---

## License

Apache License 2.0. See `LICENSE`.

---

## Relationship to implementations

This repository defines **what** an AI agent is allowed to do.

It does not define **how** an agent executes.

Hosted services may provide:

* Secure execution
* Sandbox isolation
* Approval workflows
* Audit storage

Those services **must consume this spec without modification**.

---

## Contributing

Contributions are welcome.

Please:

* Open an issue for discussion before large changes
* Include rationale and tradeoffs
* Avoid implementation-specific assumptions

---

## Status

This is **v1 (initial draft)**.

The goal of v1 is clarity and correctness, not completeness.
