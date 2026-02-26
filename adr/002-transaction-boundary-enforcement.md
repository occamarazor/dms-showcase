
# ADR-002: Enforce Explicit Transaction Boundaries in Domain Workflows

## Date
2026-02-26

## Status
Accepted

## Context

The system manages:

- Vehicle sales
- Financing arrangements
- Contract generation
- Invoicing
- Ledger updates

These workflows involve financial invariants that must not be violated.

Examples:
- A sale must not exist without a corresponding financial record
- Financing approval must be atomic
- Ledger balance must remain consistent
- Contract state must reflect actual financial state

Previous legacy system relied on implicit transaction behavior and procedural flow.

## Decision

We enforce:

- Explicit transaction boundaries at application service level
- Single transaction per business use-case
- Domain-driven invariant validation before commit
- No cross-aggregate distributed transactions
- Use PostgreSQL ACID guarantees as authoritative consistency mechanism

Transactions begin:
- At REST use-case entry point

Transactions end:
- After all domain invariants are validated and persisted

Read replicas are never used for transactional workflows.

## Rationale

Financial systems require:

- Atomicity
- Consistency
- Referential integrity
- Isolation guarantees

Eventual consistency is unacceptable for core financial invariants.

Explicit boundaries prevent:
- Partial writes
- Orphan records
- Phantom financial states
- Concurrency anomalies

## Consequences

### Positive
- Deterministic financial state
- Reduced data corruption risk
- Predictable concurrency behavior
- Simplified auditability

### Negative
- Longer transactions under complex workflows
- Need for careful lock scope management
- Requires strong discipline in service layer

### Risk Mitigation
- Avoid long-running transactions
- No external calls inside transactions
- Use optimistic locking where applicable
- Use read replicas strictly for reporting

## Related Decisions
- [ADR-001: Multi-Tenancy Strategy](./001-multitenancy-strategy.md)
- [ADR-003: Object Storage Strategy](./003-object-storage-strategy.md)
