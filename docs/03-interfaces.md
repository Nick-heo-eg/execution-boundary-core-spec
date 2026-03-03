# 03. Interfaces

## Evaluator Interface

```
evaluate(envelope: ActionEnvelope) -> Decision
```

**Requirements:**

- Deterministic: same input produces same output
- Side-effect free: must not write, send, or mutate external state
- Total: must produce a Decision for every valid envelope
- Fail-closed: on any error, must return `result: DENY`

**Prohibited behaviors:**

- Executing the proposed action
- Writing to the ledger directly
- Returning without a Decision

---

## Ledger Interface

```
append(decision: Decision) -> void
get(decision_id: uuid) -> Decision
replay(from: timestamp, to: timestamp) -> Decision[]
```

**Requirements:**

- Append-only: existing entries must not be modified or deleted
- Complete: both `ALLOW` and `DENY` decisions must be recorded
- Ordered: entries must support deterministic replay by timestamp

---

## Runtime Adapter Interface

```
execute(envelope: ActionEnvelope, decision: Decision) -> ExecutionResult
```

**Requirements:**

- Must verify `decision.result == ALLOW` before proceeding
- Must verify `decision.action_id == envelope.action_id`
- Must not execute if either check fails
- Must return a result indicating success or failure of the side-effect

---

## Proof Hash Computation

The `proof_hash` field in a Decision must be computed as:

```
proof_hash = hash(decision_id + action_id + result + timestamp)
```

Hash function: SHA-256 minimum.
Encoding: hex string.

This allows independent verification that a denial occurred at a specific point in time for a specific action.
