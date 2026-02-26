# ADR-001: Adopt Schema-per-Tenant Multi-Tenancy Model

## Date
2026-02-26

## Status
Accepted

## Context

The platform is transitioning from a single-tenant, dealer-hosted deployment model
to a centralized SaaS architecture.

Key requirements:
- Strong tenant isolation
- Financial data integrity
- Controlled schema evolution
- Moderate tenant count (not hyperscale)
- PostgreSQL as primary database
- ACID transactional guarantees required

Alternative multi-tenancy models considered:

1. Shared Database, Shared Schema (tenant_id column)
2. Shared Database, Schema-per-Tenant
3. Database-per-Tenant

## Decision

We adopt **Schema-per-Tenant** within a shared PostgreSQL cluster.

Each tenant:
- Has its own database schema
- Shares the same physical database instance
- Shares infrastructure and connection pool
- Uses identical schema structure

Tenant resolution occurs at:
- Authentication boundary (JWT contains tenant identifier)
- Request-level context injection
- Schema switching at connection/session level

## Rationale

Schema-per-tenant provides:

- Strong logical isolation
- Reduced risk of cross-tenant data leakage
- Simpler backup per tenant
- Controlled migration execution
- Lower operational overhead than database-per-tenant
- Stronger integrity guarantees than shared-schema

This balances isolation and operational efficiency.

## Consequences

### Positive
- Clear tenant boundary
- ACID guarantees preserved per tenant
- Controlled schema evolution
- Simplified audit and export

### Negative
- Migration orchestration required per schema
- Increased catalog size in PostgreSQL
- Slightly higher operational complexity vs shared-schema

### Risk Mitigation
- Centralized migration runner
- Strict tenant resolution middleware
- Automated tenant provisioning process
- Monitoring for schema drift

## Related Decisions
- ADR-002: Transaction Boundary Enforcement