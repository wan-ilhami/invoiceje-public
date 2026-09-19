# Architecture

High-level, public-safe view of how Invoiceje is built. No internal URLs, credentials, or infrastructure identifiers — just the shape of the system.

## Two-part system

- **Backend**: a NestJS API plus a separate worker process for scheduled jobs (recurring invoices, dunning reminders), backed by a queue.
- **Frontend**: a Next.js application — the authenticated app shell, a platform-admin area, and a public read-only invoice page for sharing with customers.

## Multi-tenancy: Postgres Row-Level Security

Every business is an organisation. Every domain row belongs to exactly one organisation, and the boundary is enforced at the database, not in application code. Every domain table has row-level security enabled and *forced* — meaning even the table owner is subject to the policy, not just ordinary application connections. The API connects through a role with no bypass privilege and no ownership of the tables it queries. A request's tenant identity is set once per transaction and every query in that transaction is clamped to it. If a code path forgets to set the tenant context, the policy returns zero rows rather than leaking another tenant's data — the failure mode is an empty result, not a cross-tenant leak.

## Authentication

Sessions are opaque tokens backed by a database row, not signed JWTs. Validation is a database lookup per request; revocation is an immediate row delete rather than waiting for token expiry. This trades a small amount of per-request latency for instant, unconditional session revocation — the right trade for a modular monolith with one shared database.

## Money handling

Monetary values are never represented as floating-point numbers anywhere in the system. They're stored as fixed-precision decimals, transmitted as strings (never JSON numbers), and only ever operated on through a decimal arithmetic wrapper with banker's rounding. This closes off an entire class of precision bugs that a naive float-based implementation would be exposed to.

## Deployment model

Designed to be self-hostable with zero required paid dependencies: a relational database, a queue-backing store, an S3-compatible object store, and outbound email are the only infrastructure pieces, and all of them run as ordinary containers for local/self-hosted use. A production deployment can swap any of these for a managed equivalent without changing application code — nothing in the core system assumes a specific hosted vendor.

## What this repo publishes

This public repository carries the product overview and this architecture summary. It intentionally does not carry application source, infrastructure configuration, or database schema — enough to understand how the system is built, not enough to reconstruct it.
