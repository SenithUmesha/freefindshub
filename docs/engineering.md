# FreeFindsHub engineering notes

This document describes the production architecture at a public-showcase level. The production source, operational configuration, private growth reports, content registry, and privileged workflow implementation remain private.

## 1. Product shape

FreeFindsHub is a static-first utility publisher for useful free digital resources and high-intent geek content.

The public information architecture is intentionally consolidated into five hubs:

- **Gaming** — codes, rewards, free claims, and unlock utilities
- **Freebies** — real free offers and high-value promotions
- **Software** — software, AI, and mobile utilities or useful free tiers
- **Perks** — student and creator benefits
- **Tools** — FreeFindsHub-owned tools, templates, and practical assets

Legacy detail routes can remain stable when a page survives the content audit, but a historical category is not automatically allowed to keep producing new public pages.

The production system now separates four concerns:

1. **Publishing correctness** — can this resource safely become a real page?
2. **Site delivery** — can the visitor get a fast, useful, crawlable page?
3. **Owner operations** — can the owner inspect and make bounded changes without creating a second source of truth?
4. **Growth feedback** — what should be researched or improved next?

Those systems exchange data, but none is allowed to silently take control of the others.

---

## 2. Static-first public architecture

The common public request path does not need a database round trip.

```text
structured resource records
        ↓
schema + collection validation
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
- fast edge delivery
- fewer runtime failure modes
- simple cache behavior
- deterministic production output
- easy local and CI verification of the exact site that will ship

Dynamic infrastructure is intentionally added only when a use case actually needs it.

---

## 3. Typed publishing model

The production content registry is not treated as arbitrary frontmatter.

Publishable records are validated against typed contracts covering things such as:

- stable identity
- content vertical
- slug and canonical route
- title and summary metadata
- real action destination
- source and verification information
- publication and verification timestamps
- optional expiry state
- relationships to other records
- article sections and utility content
- canonical image alt text
- optional media metadata
- indexability state

Collection-level validation also protects invariants that an individual record cannot check alone, such as duplicate IDs, duplicate effective routes, malformed relationships, and collisions across published resources.

The important architectural rule is:

> becoming representable in the schema is not the same as becoming publishable.

A supported record type can exist in the platform without automatically gaining an indexable production page.

---

## 4. Reader-first rendering

The public page does not expose every internal field just because the publishing model tracks it.

Gaming code guides are a good example. Internally, individual codes can carry evidence and freshness state. Publicly, the page is organized around the reader's task: usable code history, redemption steps, troubleshooting, game context, and updates.

Current, reported, and expired entries can remain visible when they are useful. Internal verification language is kept out of the main reader experience unless it materially helps someone make a decision.

That same rule applies across the site: strict evidence and structured metadata behind the scenes; natural article language on the page.

---

## 5. Route generation

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
 ├── freshness / source UI
 └── optional responsive media
```

This allows the platform to grow across different resource categories while keeping the publishing policy centralized.

---

## 6. Search and local discovery

The site generates a compact static index from eligible records.

Search uses deterministic signals from fields such as titles, tags, metadata, and useful page text. Filters and sorts operate on that generated dataset without turning query-string result states into competing indexable pages.

The discovery layer also includes:

- newest and recently verified surfaces
- expiring-resource surfaces where relevant
- deterministic related-resource fallbacks
- Saved Finds stored locally in the browser
- recently viewed state stored locally in the browser

This keeps lightweight personalization possible without requiring an account or profile backend.

---

## 7. Owner control room

The production project now has a separate owner control room rather than relying on raw repository files as the day-to-day operating interface.

High-level shape:

```text
owner
  ↓
Cloudflare Access
  ↓
isolated Admin Worker
  ├── Statistics
  ├── Analytics
  ├── Publications
  ├── Media
  └── bounded operational / audit surfaces
  ↓
GitHub + bounded D1 operational state
```

The control room is deliberately separate from the public Astro site.

Key boundaries:

- Git remains canonical for publications and final media.
- D1 stores recoverable operational state, action history, and reconciliation data rather than becoming a second content database.
- owner mutations are identity-bound and restricted to expected operations
- browser actions do not get an arbitrary direct-to-`main` write surface
- repository operations remain narrow and auditable

This gives the project a usable owner interface without weakening the source-of-truth model.

---

## 8. Privacy-bounded analytics

Owner analytics combines signals that answer different questions without pretending they are interchangeable.

The production control room can surface aggregate information from areas such as:

- Google Search Console query and page performance
- Cloudflare Web Analytics traffic and real-user performance
- publication metadata and freshness
- bounded first-party interaction signals when explicitly enabled
- operational media and publication state

The project intentionally avoids manufacturing precision that the underlying providers do not offer. Search visibility, site traffic, page interactions, and advertising data remain separately defined inputs.

The resulting reporting layer is useful for prioritization, but it cannot grant publish permission or rewrite content on its own.

---

## 9. GitHub-native Daily Publisher

FreeFindsHub has recurring publishing automation, but generic repository auto-merge is not the model.

The current scheduler runs from trusted repository automation and gives the research/writing model a narrow job: propose one to three new structured content records.

Conceptually:

```text
scheduled research + writing
        ↓
bounded structured payload
        ↓
deterministic staging layer
        ↓
content-only branch + PR
        ↓
exact-revision policy checks
        ↓
merge to main
        ↓
normal production build
```

The staging layer, not the research agent, performs repository writes. It rejects existing IDs, unexpected paths, media payloads, unsafe URLs, and shapes outside the production content contract.

The pull request remains useful as a review and audit artifact, while the permission boundary stays narrow. The writing system does not receive shell access, Git authority, workflow mutation, or arbitrary repository writes simply because it is allowed to draft an article.

The production schedule currently runs daily at 23:30 Asia/Colombo, with the same path available for explicit owner-triggered runs.

---

## 10. Separate owner media path

Image state is intentionally decoupled from article eligibility.

A strong article can publish with canonical image alt text and no hero image. The owner can add original editorial artwork later through the Admin media queue.

The current workflow is deliberately simpler than the earlier automated media-discovery design:

```text
published article without image
        ↓
Admin: Needs image
        ↓
article context + deterministic image prompt
        ↓
owner-created source image
        ↓
browser crop / encode
        ↓
responsive 16:9 WebP variants
        ↓
narrow owner-media repository update
        ↓
Image added
```

The browser produces responsive 640px, 960px, and 1440px WebP variants. The same workflow keeps the publication's canonical alt text aligned with the final image.

The publisher itself does not search for images, scrape artwork, perform licensing research, create placeholders, or block an otherwise useful article because artwork is missing.

This makes the trust boundary easier to explain: automated publishing handles research and writing; the owner owns visual direction.

---

## 11. SEO architecture

Technical SEO is generated from the same validated content state as the visible page.

That avoids a class of bugs where the page says one thing while the sitemap, schema, canonical, or feed says another.

The production system includes:

- canonical URLs
- sitemap filtering
- robots handling
- Open Graph metadata
- visible breadcrumbs
- JSON-LD breadcrumbs
- record-derived structured data where appropriate
- RSS resource feed
- noindex handling for raw download assets and query-result states
- deterministic SEO diagnostics
- search verification hooks
- trusted post-publish IndexNow submission

The system deliberately avoids unsupported ranking claims or schema that does not match visible content.

---

## 12. Growth feedback

Search performance is observed through private, read-only reporting paths and the owner control room. Aggregate site behavior is measured separately so search visibility and on-site behavior do not get conflated.

The growth loop is conceptually:

```text
Search Console
        +
aggregate traffic signals
        +
optional bounded onsite signals
        +
future advertising inputs
        ↓
normalized private owner view
        ↓
opportunity / diagnostic review
        ↓
human editorial decision
```

The scoring or reporting layer is advisory. It cannot grant publish permission, bypass source verification, weaken duplicate-intent protections, or silently trigger content creation.

That separation is important: a demand signal can tell the editorial process where to look, but demand alone does not make a page useful or trustworthy.

---

## 13. Monetization architecture

Advertising is modeled as a separately gated system.

The page layout supports monetization, but activation depends on owner and provider prerequisites rather than content automation deciding that a page should show ads.

The production architecture accounts for areas such as:

- useful content depth
- page-type eligibility
- layout and interaction safety
- external account and policy readiness
- consent requirements where applicable
- search and performance health

The product rule is still straightforward: the page must be worth visiting even if every ad slot is mentally removed.

---

## 14. Why Cloudflare

The public site is deployed as static output through Cloudflare Workers Static Assets, while the owner control room runs as a separate Worker protected by Cloudflare Access.

That creates a useful split:

```text
PUBLIC
GitHub main
   ↓
validated Astro build
   ↓
static dist
   ↓
Cloudflare edge

OWNER
Cloudflare Access
   ↓
Admin Worker
   ↓
GitHub + bounded D1 state
```

The project also deliberately keeps infrastructure modest. D1 exists where owner operational state justifies it; the public content path remains static and does not require a database request.

---

## 15. Testing and release checks

The private production repository has focused checks around content, SEO behavior, owner operations, automation policy, media handling, publishing output, and growth tooling.

Representative classes of checks include:

- Astro and TypeScript validation
- production build verification
- content schema tests
- collection and published-record validation
- structured-data tests
- search behavior tests
- monetization-policy tests
- autonomous publishing-policy tests
- Admin security and operation tests
- owner-media workflow tests
- IndexNow tests
- growth and Search Console reporting tests
- browser QA and production-output validation

This matters because a publishing site can fail without throwing a runtime exception. A broken canonical, accidental indexable query state, stale action URL, duplicate route, misleading code state, or unsafe owner operation is still a production defect.

---

## 16. Things intentionally not published here

This showcase does not include:

- production application source
- the live content registry
- owner control-room source and configuration
- private growth reports
- privileged automation implementation
- deployment credentials or environment configuration
- advertising account identifiers
- analytics or provider credentials
- private editorial and research artifacts

The purpose of this repository is to make the engineering legible without weakening the separation between a public portfolio artifact and the production publishing system.
