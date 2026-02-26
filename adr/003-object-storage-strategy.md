# ADR-003: Store Contracts and Documents in S3-Compatible Object Storage

## Date
2026-02-26

## Status
Accepted

## Context

The system manages:
- Contracts
- Generated PDFs
- Supporting documents
- Vehicle attachments
- Financial documentation

Storing large binary objects inside PostgreSQL
would:

- Increase database size
- Slow backups
- Affect performance
- Complicate replication

The platform is deployed on Hetzner infrastructure.

## Decision

We store all document binaries in:

- S3-compatible object storage (e.g., Hetzner Object Storage)

PostgreSQL stores:
- Metadata
- File references
- Hashes
- Ownership information
- Tenant association

Document workflow:
1. Generate file
2. Store in object storage
3. Persist metadata inside tenant schema
4. Maintain referential link

## Rationale

Object storage provides:
- Horizontal scalability
- Efficient large file handling
- Cheaper storage tier
- Independent scaling from transactional DB
- Simplified backup strategy

Separation preserves:
- DB performance
- Transactional integrity
- Clear responsibility boundaries

## Consequences

### Positive
- Smaller database footprint
- Better scalability
- Faster replication
- Clear separation of concerns

### Negative
- Additional infrastructure dependency
- Requires secure access management
- Requires object lifecycle policies

### Risk Mitigation
- Signed URLs
- Strict IAM policy
- Encryption at rest
- Periodic orphan cleanup job

## Related Decisions
- [ADR-001: Multi-Tenancy Strategy](./001-multitenancy-strategy.md)
- [ADR-002: Transaction Boundary Enforcement](./002-transaction-boundary-enforcement.md)
