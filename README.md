# Boat CRM

Cloud-based, multi-tenant SaaS for boat brokers and boat dealers. Din Båtmäklare is the first operating organization and design partner.

## Product vision

One boat card is the operational source of truth throughout the full lifecycle:

`New object -> Brokerage assignment or Purchase -> Inventory -> Photos -> Condition report -> AI listing -> Marketplace publishing -> Leads -> Sale contract -> Signing -> Invoicing -> Delivery -> Sold -> Historical valuation data`

The product should feel premium, calm, minimal and pedagogical. It must not resemble a generic CRM or an AI-generated dashboard. The interface should prioritize the next relevant action and progressively disclose detail instead of presenting everything at once.

## Initial product domains

- Organizations and users
- Contacts
- Boats and engines
- Brokerage assignments
- Purchases and owned inventory
- Inventory lifecycle
- Valuations and comparable boats
- Images and documents
- Condition reports / varudeklaration
- AI-assisted listing generation
- Marketplace publishing
- Leads, activities, viewings and bids
- Contracts and signing
- Deals and delivery
- Fortnox invoicing and financial data
- Analytics and sales statistics
- Audit log

## Engineering standard

This repository is intended to become production software, not a prototype-only codebase.

### Core principles

1. Security, data integrity and tenant isolation take precedence over development speed.
2. Never trust the client. Authorization and validation for sensitive operations must happen server-side.
3. Every tenant-owned resource must be scoped to an organization. Cross-tenant access must be prevented at application and, where appropriate, database policy level.
4. Use strict TypeScript. Avoid `any` except for documented boundary cases.
5. PostgreSQL is the source of truth for structured business data.
6. Signed contracts and condition reports used in a transaction are immutable snapshots. Later edits to a boat must never alter signed records.
7. Secrets, API credentials and OAuth tokens must never be exposed to the browser or committed to source control.
8. External integrations are isolated behind explicit adapters/services and must not leak provider-specific logic throughout the application.
9. Webhooks must be authenticated/verified where supported and handlers must be idempotent. Repeated signing events must never create duplicate invoices or duplicate business actions.
10. Critical business transitions must be auditable.
11. Validate untrusted input at system boundaries with explicit schemas.
12. Financial calculations use appropriate decimal/integer representations; never rely on floating-point arithmetic for money.
13. Critical authorization, contract, invoicing and tenant-isolation flows require automated tests.
14. Dependencies should be intentionally selected, pinned/locked and kept current. Avoid unnecessary packages.
15. Accessibility, responsive behavior, observability, backups and recoverability are product requirements, not post-launch extras.

## Proposed stack

The stack will be confirmed in an Architecture Decision Record before application scaffolding.

Current direction:

- Next.js + React
- TypeScript
- PostgreSQL
- Supabase for managed PostgreSQL/Auth/Storage where it provides clear value
- Server-side application/service layer for privileged business operations
- Cloud deployment with separate development, staging and production environments
- GitHub Actions for CI

The architecture should avoid unnecessary vendor lock-in. Core domain logic should remain portable even when managed infrastructure is used.

## Multi-tenancy

Din Båtmäklare is organization #1, but the data model must support multiple independent boat dealers from the beginning.

Users belong to organizations through memberships and roles. Tenant context must be derived from authenticated membership, not accepted blindly from client input.

Potential roles include:

- Owner/Admin
- Broker/Sales
- Finance
- Marketing/Assistant
- Read-only

Permissions will be capability-based where practical rather than scattered role-name checks.

## Primary workflows

### Brokerage

Create object -> enter owner and boat data -> generate brokerage agreement -> send for signing -> signed -> inventory/preparation -> photos -> condition report -> generate listing -> approve -> publish -> manage leads/bids -> create sales contract -> sign -> invoice -> deliver -> unpublish -> sold.

### Purchase

Create object -> register as purchase -> seller and acquisition data -> acquisition documentation -> owned inventory -> preparation/costs -> photos -> condition report -> listing -> publish -> leads/bids -> sales contract -> sign -> invoice -> deliver -> unpublish -> sold -> realized margin.

## Analytics direction

The system should distinguish brokerage economics from owned-stock economics.

Examples:

- Revenue
- Gross profit / contribution
- Costs
- Brokerage commission revenue
- Average brokerage commission
- Average commission percentage
- Profit per owned boat
- Inventory value / capital employed
- Inventory days
- Time to sale
- Asking price vs final price
- Lead -> viewing -> bid -> sale conversion
- Lead and sale attribution by marketplace

Fortnox remains the accounting system of record. Boat CRM presents operational and management information relevant to a boat brokerage/dealership.

## Integration boundaries

Planned integration adapters include:

- Fortnox
- Digital signing / BankID provider
- WordPress
- Blocket
- Boat24
- AI provider(s)

No external integration is assumed available until its API access, commercial terms and technical constraints have been verified.

## Development approach

Work in small, reviewable changes. Prefer feature branches and pull requests once scaffolding begins. Keep architectural decisions in `docs/adr/` and product specifications in `docs/product/`.

Before external SaaS launch, the product should undergo independent security review / penetration testing and appropriate legal/privacy review.
