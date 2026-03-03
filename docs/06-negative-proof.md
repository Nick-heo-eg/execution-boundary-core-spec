# 06. Negative Proof

## Definition

A **negative proof** is a verifiable record that an action was proposed and explicitly denied — not merely absent from the execution log.

## Requirement

Every `DENY` decision MUST produce:

1. A complete Decision object with `result: DENY`
2. A ledger entry containing that Decision
3. A `proof_hash` computed from the Decision fields

## Why This Matters

Execution logs record what happened.
Negative proof records what was stopped.

Without negative proof:

- A missing execution log entry is ambiguous: was it denied, or was it never proposed?
- Denial cannot be audited independently of execution.
- The boundary between "not attempted" and "attempted and blocked" collapses.

## Verification

Given a `proof_hash`, an independent verifier can:

1. Retrieve the Decision from the ledger by `decision_id`
2. Recompute `hash(decision_id + action_id + result + timestamp)`
3. Confirm the hash matches
4. Confirm `result == DENY`

This confirms that a specific action was evaluated and denied at a specific time — without relying on the absence of an execution record.

## Relationship to Ledger

The ledger is the substrate for negative proof.

A ledger that only records `ALLOW` decisions cannot support negative proof.
A conformant ledger records all decisions unconditionally.
