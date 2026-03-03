# 00. Overview

## What This Spec Defines

A minimal structural boundary between:

- **Proposal** — an intent to act
- **Authorization** — a deterministic decision on that intent
- **Execution** — the side-effect that results from authorization

These three are distinct stages. Conflating any two produces systems where execution authority is ambiguous, denials are unverifiable, and side-effects cannot be structurally suppressed.

## What This Spec Does Not Define

- Policy language or rule syntax
- Risk scoring or threshold models
- Domain-specific message formats
- Compliance or regulatory frameworks

## Scope

This spec applies to any system where:

1. An action is proposed before it is executed.
2. The action produces an observable side-effect.
3. The authorization of that action must be traceable.

## Relationship to Implementations

This spec defines interfaces and state transitions only. Implementations may use any language, transport, or storage backend.

Conformance requires:

- An Evaluator that produces Decision objects
- A Ledger that records all decisions
- A Runtime Adapter that executes only on `ALLOW`
- Fail-closed behavior on evaluator failure
