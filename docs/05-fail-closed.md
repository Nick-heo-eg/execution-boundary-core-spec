# 05. Fail-Closed Requirement

## Rule

If deterministic evaluation cannot be completed, the system MUST default to `DENY`.

Execution is never the default state.

## Conditions That Trigger Fail-Closed

- Evaluator throws an exception or panics
- Evaluator times out before producing a Decision
- Decision object is malformed or missing required fields
- Ledger append fails before execution is considered
- `decision.action_id` does not match `envelope.action_id`

## Required Behavior

In any of the above conditions:

1. A synthetic Decision with `result: DENY` and `reason_code: EVALUATOR_FAILURE` MUST be generated.
2. The synthetic Decision MUST be appended to the ledger.
3. Execution MUST NOT proceed.

## Rationale

A system that executes on evaluator failure is not a gated system — it is an ungated system with an optional evaluator.

Fail-closed is not a fallback. It is the structural requirement that makes the boundary meaningful.

## What Fail-Closed Does Not Cover

Fail-closed applies to the Evaluator and the Decision layer.

It does not define behavior for:
- Ledger failures after a Decision has been produced (implementation-defined)
- Runtime Adapter failures after execution has begun (implementation-defined)
- Network partitions between components (implementation-defined)
