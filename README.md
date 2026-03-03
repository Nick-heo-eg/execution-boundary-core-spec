# Execution Boundary Core Spec (v0.1)

Vendor-neutral structural layer for separating proposal, authorization, and execution in side-effecting systems.

---

## Layered Model

```
execution-boundary-core-spec        ← this repository (structural definition)
         ↑
execution-boundary-transport-profile  (transport application profile)
ai-execution-boundary-spec            (AI agent application profile)
execution-observability-profile       (OTel observability profile)
         ↑
execution-gate                        (reference implementation)
agent-execution-guard                 (AI agent engine)
```

Profiles extend Core. Implementations conform to Core via a profile.
Core fields are never modified by profiles — extension is add-only.

---

## 1. Purpose

This specification defines a minimal structural layer that separates:

- Proposal of an action
- Authorization of that action
- Execution of side-effects

The model is transport-agnostic and domain-neutral.

It does not define policy semantics.
It defines structural boundaries.

---

## 2. Problem Definition

In many systems:

- Validation is conflated with authorization.
- Execution authority is embedded inside runtime logic.
- Denied actions leave no verifiable trace.
- Side-effects occur before deterministic authorization.

This specification introduces a boundary layer to ensure:

1. Authorization is explicit and deterministic.
2. Execution cannot occur without authorization.
3. Denials are recorded as verifiable events.
4. Side-effects are structurally suppressible.

---

## 3. Reference Architecture

```
Proposal
   ↓
Action Envelope
   ↓
Evaluator
   ↓
Decision
   ↓
Ledger Append
   ↓
Runtime (ALLOW only)
```

Validation and authorization are distinct operations.

Execution authority resides exclusively in the Decision layer.

---

## 4. Core Components

### 4.1 Proposal

An intent to perform an action.
No side-effect occurs at this stage.

### 4.2 Action Envelope

A structured representation of the proposed action.

Required properties:

```json
{
  "action_id": "uuid",
  "action_type": "string",
  "resource": "string",
  "parameters": "object",
  "context_hash": "string",
  "timestamp": "string"
}
```

The envelope must be immutable after evaluation begins.

### 4.3 Evaluator

A deterministic function:

```
evaluate(envelope) -> Decision
```

The evaluator:

- Must produce one of: `ALLOW`, `DENY`, `HOLD`
- Must be side-effect free
- Must not execute the action

### 4.4 Decision Object

```json
{
  "decision_id": "uuid",
  "action_id": "uuid",
  "result": "ALLOW | DENY | HOLD",
  "reason_code": "string",
  "authority_token": "string",
  "proof_hash": "string",
  "timestamp": "string"
}
```

Only `ALLOW` enables execution.

### 4.5 Ledger

An append-only record of decisions.

The ledger must:

- Record both `ALLOW` and `DENY` decisions
- Support deterministic replay
- Preserve decision integrity

### 4.6 Runtime Adapter

Executes side-effects only when:

```
decision.result == ALLOW
```

No other component may trigger execution.

---

## 5. State Model

```
PROPOSED
   ↓
EVALUATED
   ↓
ALLOWED → EXECUTED
DENIED  → CLOSED
HOLD    → PENDING
```

Rules:

- Only `ALLOWED` actions may transition to `EXECUTED`.
- `DENIED` actions must not execute.
- `HOLD` requires explicit re-evaluation.

---

## 6. Fail-Closed Requirement

If deterministic evaluation cannot be completed, the system MUST default to `DENY`.

Execution is never the default state.

---

## 7. Negative Proof

Denial must be recorded.

A denied action must produce:

- A Decision object
- A ledger entry
- A verifiable `proof_hash`

Absence of execution is not implicit.
It must be structurally provable.

---

## 8. Non-Goals

This specification does not define:

- Policy language
- Risk scoring algorithms
- Compliance frameworks
- Domain-specific governance models

It defines structural separation only.

---

## 9. Applicability

The model applies to any side-effecting system, including:

- Network transport layers
- Tool invocation runtimes
- File mutation systems
- Financial gateways
- Infrastructure automation

---

## 10. Versioning

v0.1 defines the minimal structural boundary model.

Future revisions may define:

- Deterministic replay protocol
- Decision integrity chaining
- Cross-system authority federation

---

## Structure

```
/docs
  00-overview.md
  01-architecture.md
  02-state-model.md
  03-interfaces.md
  04-decision-object.md
  05-fail-closed.md
  06-negative-proof.md
/spec
  action-envelope.schema.json
  decision.schema.json
README.md
```
