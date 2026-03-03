# 02. State Model

## States

| State | Description |
|---|---|
| `PROPOSED` | Action intent has been expressed. No side-effect has occurred. |
| `EVALUATED` | Evaluator has produced a Decision. |
| `ALLOWED` | Decision result is `ALLOW`. |
| `DENIED` | Decision result is `DENY`. |
| `HOLD` | Decision result is `HOLD`. Awaiting re-evaluation. |
| `EXECUTED` | Side-effect has been performed. |
| `CLOSED` | Action terminated without execution. |

## Transitions

```
PROPOSED → EVALUATED
EVALUATED → ALLOWED
EVALUATED → DENIED
EVALUATED → HOLD
ALLOWED → EXECUTED
DENIED → CLOSED
HOLD → EVALUATED  (re-evaluation)
HOLD → CLOSED     (timeout or explicit cancellation)
```

## Rules

1. `EXECUTED` is reachable only from `ALLOWED`.
2. `DENIED` actions must not reach `EXECUTED`.
3. `HOLD` actions may not reach `EXECUTED` without passing through `EVALUATED` again.
4. Evaluator failure transitions to `DENIED` (fail-closed).

## Invalid Transitions

The following transitions are prohibited:

- `PROPOSED → EXECUTED`
- `DENIED → EXECUTED`
- `HOLD → EXECUTED`
- Any transition that bypasses the Evaluator
