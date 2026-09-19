# Invoiceje

**Self-hostable invoicing and light-accounting for freelancers and small studios.**

Customers, products, tax rates, invoices with gap-free numbering, payments, quotations that convert into invoices, recurring invoices that auto-fire on a schedule, credit notes, expenses with attached receipts, dunning reminders, bank reconciliation against CSV statements, an audit log per organisation, and platform-wide announcements. It also drafts invoices from plain-English prompts or forwarded customer emails.

```
Self-hostable  |  RLS-clamped  |  Multi-tenant  |  Feature-complete V1
NestJS 10      |  Next.js 16   |  Postgres 16   |  better-auth 1.6
```

## Why it's built this way

- **Multi-tenancy is a database-level guarantee, not an application convention.** Every domain table carries an `organization_id` and a row-level-security policy that clamps reads and writes to the active tenant — the database refuses to return another tenant's rows even if the application code has a bug. The connection role the API uses has no bypass privilege and doesn't own the tables.
- **No paid services required to run it.** Postgres, Redis, an S3-compatible object store, and a local SMTP catcher all run as containers. The whole system boots on a fresh machine in minutes. Production swaps these for managed equivalents, but nothing in the default setup requires a hosted product you can't run yourself.
- **Money is never a floating-point number.** `numeric(19,4)` in the database, strings on the wire, decimal arithmetic with banker's rounding in between.
- **Closed signup, admin-controlled access.** A platform admin creates a business, a visitor requests access and gets approved, or a business owner invites a teammate — no open public signup.

## Try it

Self-hostable end to end — clone it, bring up the containers, and it runs on your own infrastructure with no hosted dependency required. Interested in a hosted version, or want to run your own studio's invoicing on it? Reach out via the contact on this GitHub profile.

## Architecture

See [`ARCHITECTURE.md`](./ARCHITECTURE.md) for the system shape: the multi-tenant request path, the authentication model, and the money-handling invariant.

## What this repository is

This is the **public** half of Invoiceje — an architecture and product showcase. The application code (NestJS backend, Next.js frontend) lives in a private repository. This repo contains:

- ❌ No API keys, tokens, credentials, internal URLs, or customer data.
- ✅ Only the product overview, architecture docs, and (as they're added) screenshots.
