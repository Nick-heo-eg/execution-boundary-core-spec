# 01. Architecture

## Reference Flow

```
Proposal
   ↓
Action Envelope  (structured, immutable)
   ↓
Evaluator        (deterministic, side-effect free)
   ↓
Decision         (ALLOW | DENY | HOLD)
   ↓
Ledger Append    (unconditional)
   ↓
Runtime Adapter  (executes only on ALLOW)
   ↓
Side-Effect
```

## Component Responsibilities

| Component | Responsibility | May produce side-effects |
|---|---|---|
| Proposal | Express intent | No |
| Action Envelope | Represent intent structurally | No |
| Evaluator | Produce Decision | No |
| Ledger | Record Decision | Yes (append-only write) |
| Runtime Adapter | Execute action | Yes (only on ALLOW) |

## Key Constraints

**Validation ≠ Authorization**

Schema validation confirms the envelope is well-formed.
Authorization determines whether execution is permitted.
These are separate operations performed by separate components.

**Execution authority is singular**

Only the Decision layer grants execution authority.
No other component may initiate side-effects.

**Ledger append is unconditional**

Every decision — `ALLOW`, `DENY`, or `HOLD` — is appended to the ledger before execution is considered.
