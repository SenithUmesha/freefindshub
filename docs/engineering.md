# FreeFindsHub engineering notes

This document describes the production architecture at a public-showcase level. The production source, operational configuration, private growth reports, content registry, and privileged workflow implementation remain private.

## 1. Product shape

FreeFindsHub is a static-first discovery and publishing system for useful free digital resources.

The supported content model spans multiple verticals, including deals, career resources, templates, AI resources, gaming, student resources, fonts, wallpapers, mobile, promotions, courses, and downloads.

The production system separates three concerns:

1. **Publishing correctness** — can this resource safely become a real page?
2. **Site delivery** — can the visitor get a fast, useful, crawlable page?
3. **Growth feedback** — what should be researched or improved next?

Those systems exchange data, but none is allowed to silently take control of the others.

---

## 2. Static-first architecture

The common request path does not need a database round trip.

```text
structured resource records
        ↓
schema validation
        ↓
Astro build
        ↓
static hubs + detail pages + search data + feeds
        ↓
Cloudflare Workers Static Assets
        ↓
edge-cached public site
```

This fits the product well because most resource pages are read-heavy and change through publishing events rather than per-request transactions.

Benefits include:

- predictable crawlable HTML
- fast CDN/edge delivery
- fewer runtime failure modes
- simple cache behavior
- deterministic production output
- easy local/CI verification of the exact pages that will ship

Dynamic infrastructure is intentionally added only when a use case actually needs it.

---

## 3. Typed publishing model

The production content registry is not treated as arbitrary frontmatter.

Publishable records are validated against typed contracts covering things such as:

- stable identity
- content vertical
- slug / canonical route
- title and summary metadata
- real action destination
- source/provenance information
- publication and verification timestamps
- optional expiry state
- relationships to other records
- media metadata
- indexability state
- rights/licensing information where relevant
- content-depth requirements

Collection-level validation also protects invariants that an individual record cannot check alone, such as duplicate IDs, duplicate effective routes, malformed relationships, and collisions across published resources.

The important architectural rule is:

> becoming representable in the schema is not the same as becoming publishable.

A supported vertical can exist in the platform without automatically gaining indexable production pages.

---

## 4. Route generation

A validated record drives the public page system instead of requiring a one-off route implementation for every resource.

At build time, the record can feed:

```text
record
 ├── hub card
 ├── detail page
 ├── canonical metadata
 ├── structured data
 ├── sitemap eligibility
 ├── related links
 ├── search index
 ├── freshness/source UI
 └── media output
```

This allows the platform to grow across different resource categories while keeping the publishing policy centralized.

---

## 5. Search and local discovery

The site generates a compact static index from eligible records.

Search uses deterministic signals from fields such as titles, tags, metadata, and useful page text. Filters and sorts operate on that generated dataset without turning query-string result states into competing indexable pages.

The discovery layer also includes:

- newest / recently verified surfaces
- expiring-resource surfaces where relevant
- deterministic related-resource fallbacks
- Saved Finds stored locally in the browser
- recently viewed state stored locally in the browser

This keeps lightweight personalization possible without requiring an account or profile backend.

---

## 6. SEO architecture

Technical SEO is generated from the same validated content state as the visible page.

That avoids a class of bugs where the page says one thing while the sitemap, schema, canonical, or feed says another.

The production system includes:

- canonical URLs
- sitemap filtering
- robots handling
- Open Graph metadata
- visible breadcrumbs
- JSON-LD breadcrumbs
- record-derived Article / CreativeWork structured data where appropriate
- RSS resource feed
- noindex handling for raw download assets and query-result states
- deterministic SEO diagnostics
- search verification hooks
- trusted post-publish IndexNow submission

The system deliberately avoids unsupported ranking claims or schema that does not match visible content.

---

## 7. Search Console and growth feedback

Search performance is observed through a private, read-only reporting path.

The growth loop is conceptually:

```text
Google Search Console
        +
aggregate traffic signals
        +
onsite search signals
        +
future revenue inputs
        ↓
normalized private report
        ↓
opportunity / diagnostic heuristics
        ↓
human editorial review
```

The scoring layer is advisory. It cannot grant publish permission, bypass source verification, weaken rights checks, or override duplicate-intent protections.

That separation is important: an SEO signal can tell the editorial process where demand exists, but demand alone does not make a page trustworthy.

---

## 8. Autonomous publishing boundary

FreeFindsHub has recurring publishing automation, but generic repository auto-merge is not the model.

The automation is restricted to a narrowly defined content proposal surface. Privileged merge logic runs from trusted default-branch code and verifies metadata and the exact successful CI commit before allowing eligible content changes through.

High-level shape:

```text
research/publisher automation
        ↓
content-only proposal
        ↓
normal CI validation
        ↓
trusted policy evaluation
        ↓
exact reviewed commit check
        ↓
merge
        ↓
production build
```

The public takeaway is the security property, not the private implementation:

- untrusted proposal code does not control the privileged merge policy
- content automation does not get authority over workflows or application code
- stale or mismatched revisions are rejected
- changed-file scope is bounded
- publishing invariants are enforced before build/deploy

---

## 9. Media pipeline

Images are part of the publishing contract.

For manually reviewed media, production records retain enough provenance/rights information to explain the source later.

For automation-assisted media, the system uses a separate validation/materialization boundary. Remote media is not simply accepted because a generated record contains a URL.

The production policy validates things such as:

- expected provider/provenance shape
- target record relationship
- allowed local output path
- decodability
- file size and dimensions
- final optimized format
- deterministic metadata association

The result is a local production asset with recorded provenance rather than a fragile hotlink.

---

## 10. Monetization architecture

Advertising is modeled as a separately gated system.

The page layout supports monetization, but activation is owner-controlled and fails closed.

The production architecture accounts for:

- minimum useful content depth
- exclusion zones around controls and primary CTAs
- indexability requirements
- page-type eligibility
- external account/policy readiness
- consent requirements where applicable

The goal is to avoid creating a layout whose business logic depends on misleading clicks or thin content.

---

## 11. Why Cloudflare Workers Static Assets

The site is deployed as static output through Cloudflare Workers Static Assets.

That keeps the main publishing path simple:

```text
GitHub main
   ↓
validated build
   ↓
static dist
   ↓
Cloudflare edge
```

This architecture leaves room for future Worker-side behavior without forcing the current content pages into a server-rendered request model.

D1/R2-style services are treated as use-case-driven additions rather than default dependencies.

---

## 12. Testing and release checks

The private production repository has focused checks around content, SEO behavior, automation policy, media validation, publishing output, and growth tooling.

Representative classes of checks include:

- Astro / TypeScript validation
- production build verification
- content schema tests
- collection/published-record validation
- structured-data tests
- search behavior tests
- monetization-policy tests
- autonomous publishing-policy tests
- media validation/materialization tests
- IndexNow tests
- growth-report tests
- production-output validation

This matters because a publishing site can fail without throwing a runtime exception. A broken canonical, accidental indexable query state, stale action URL, duplicate route, or bad rights record is still a production defect.

---

## 13. Things intentionally not published here

This showcase does not include:

- production application source
- the live content registry
- private growth reports
- privileged automation implementation
- deployment credentials or environment configuration
- advertising account identifiers
- analytics/provider credentials
- private editorial/research artifacts

The purpose of this repository is to make the engineering legible without weakening the separation between a public portfolio artifact and the production publishing system.
