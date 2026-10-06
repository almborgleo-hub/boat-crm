# MVP Product Specification

Status: Draft 0.1

## Goal

Deliver a secure cloud application that Din Båtmäklare can use for the core operational lifecycle of brokerage and owned-stock boats while establishing a multi-tenant foundation for future commercial SaaS use.

## UX principles

- Minimal, premium and Scandinavian rather than generic SaaS styling.
- One primary boat workspace/card per object.
- Progressive disclosure: show what is relevant to the current lifecycle state.
- A clear `Next step` is preferred over dashboards filled with actions.
- Desktop-first for brokerage work, fully responsive for field/mobile use.
- Destructive, financial and legally significant actions require explicit confirmation.
- AI proposes; a human approves externally published or legally significant content.

## Object creation

`+ New object` begins with an acquisition type:

1. Brokerage assignment (`förmedlingsuppdrag`)
2. Purchase / owned stock (`inköp`)

Both paths create a boat record immediately. Their workflows and economics differ.

## Brokerage workflow

1. Enter principal/contact information.
2. Enter boat, engine and core equipment information.
3. Enter assignment economics: valuation, asking price, commission and internal commercial fields.
4. Generate brokerage agreement from structured data.
5. Review and send for digital signing/BankID.
6. Once fully signed, transition object to inventory/preparation.
7. Photograph boat and upload/order images.
8. Complete structured condition report (`varudeklaration`).
9. Generate AI-assisted listing draft from structured boat data.
10. Broker reviews/edits/approves listing.
11. Select supported marketplaces and publish.
12. Capture and manage leads against the boat.
13. Record viewings, activities and bids.
14. Select buyer and create sale contract using existing boat, buyer, seller, condition-report and deal data.
15. Review and send sale contract for signing.
16. On completed signing, create invoice/facturaunderlag in Fortnox. Initial policy: require human review before sending unless organization explicitly enables safe auto-send later.
17. Track delivery readiness and payment state where integration supports it.
18. Mark delivered/sold.
19. Unpublish active listings.
20. Preserve completed transaction as historical valuation and analytics data.

## Purchase / owned-stock workflow

1. Enter seller/contact information.
2. Enter boat/engine data.
3. Enter acquisition price and acquisition terms.
4. Store purchase documentation.
5. Move boat to owned inventory.
6. Track direct costs against the boat (transport, service, detailing, advertising, repairs, other).
7. Continue through preparation, condition report, listing, publishing, leads, contract, invoicing and delivery.
8. Calculate realized gross margin from sale and attributable direct costs.

## Boat workspace

Primary tabs/sections:

- Overview
- Boat data
- Images
- Valuation
- Listing
- Condition report
- Publishing
- Leads
- Documents
- Deal / Finance
- History

The exact navigation can adapt by lifecycle state; not every section needs equal prominence at all times.

## Initial lifecycle states

States will be modeled explicitly rather than inferred only from UI:

- draft
- awaiting_assignment_signature
- preparing
- ready_to_publish
- for_sale
- reserved / contract_in_progress
- contracted
- awaiting_delivery
- sold
- archived

Transitions that trigger external side effects must be handled transactionally/idempotently.

## Condition report

Structured categories rather than only a PDF upload. Each relevant item can contain:

- status (no remark / remark / not inspected / not applicable)
- comment
- supporting images
- inspection timestamp
- inspector/user

The website can render the live approved report visually. At contract creation/signing, the relevant report is snapshotted/versioned and attached to the transaction so future edits cannot change what the buyer received.

## AI listing generation

AI generation uses structured boat information and approved free-text notes. Initial outputs:

- headline suggestion
- introduction
- object description
- equipment list normalization
- technical facts formatting

Generated text is always a draft until explicitly approved. Prompts/instructions should enforce Din Båtmäklare's concise, premium tone and avoid generic boating clichés.

## Publishing

Internal data is the source of truth. Marketplace adapters transform it into channel-specific payloads.

Initial targets:

- Din Båtmäklare WordPress website
- Blocket
- Boat24

Capabilities depend on verified API access. Publishing status, external IDs, last sync, errors and channel-specific limitations must be visible per channel.

## Leads

A lead belongs to an organization, contact and boat. Track at minimum:

- source/channel
- status
- assigned user
- created/last-contact timestamps
- notes/activities
- viewing state
- bids
- outcome

## Contracts and signing

Contracts are generated from structured, versioned data and presented through a professional responsive web UX before signing. Final signed artifacts are immutable and archived with verification metadata supplied by the signing provider.

Initial document types:

- brokerage agreement
- sale/purchase agreement
- condition-report snapshot/attachment
- optional addendum
- delivery confirmation (later if not required for first release)

## Fortnox

Fortnox remains accounting source of truth. CRM stores the relationship between deal and external invoice/customer identifiers plus operational status.

Signing completion can create an invoice draft/facturaunderlag. The first production version should favor a final human confirmation before email delivery of material invoices.

Webhook/retry behavior must be idempotent.

## Valuation

Initial valuation module stores:

- broker valuation range
- recommended asking price
- comparable objects
- notes/rationale
- confidence/data quality
- historical internal transactions

External market-data ingestion is a separate capability and must only use sources/APIs/licensing arrangements we are entitled to use.

## Analytics

Period filtering: month, quarter, year and custom range.

Core metrics:

- revenue
- costs
- contribution/gross profit
- sold boats
- brokerage commission revenue
- average brokerage commission
- average commission percentage
- owned-stock purchase cost
- direct cost per owned boat
- gross profit per owned boat
- inventory days / time to sale
- asking vs final price
- lead -> viewing -> bid -> sale conversion
- attribution by marketplace/source

Accounting definitions must be documented before metrics are treated as financial reporting.

## Multi-tenant foundation

Every organization-owned business entity must be tenant-scoped. Users access organizations through explicit memberships. Sensitive permissions are enforced server-side and protected with database-level policies where appropriate.

Initial roles can be simple, but the model must support multiple users and future capability-based permissions.

## Audit requirements

Record important actions such as:

- price changes
- lifecycle transitions
- contract creation/sending/signing
- condition-report version changes
- publishing/unpublishing
- invoice creation/send trigger
- relevant permission/organization administration changes

Audit entries should identify actor, organization, action, entity, timestamp and relevant safe metadata.

## MVP exclusions / later phases

Unless required during implementation discovery:

- Native iOS/Android applications
- Fully autonomous AI actions
- General accounting/bookkeeping replacement
- Scraping marketplaces without explicit permission
- Advanced cross-customer market intelligence
- Public self-service SaaS billing/onboarding
- Complex enterprise permissions

## First implementation milestone

A user can securely:

1. Sign in.
2. Enter their organization workspace.
3. View inventory.
4. Create a new object as Brokerage or Purchase.
5. Register contact, boat and engine information.
6. Save the object.
7. Open a clean boat workspace showing lifecycle status and the correct next step.

This milestone must already enforce tenant isolation and establish the production-quality architecture used by later modules.
