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

The stable production baseline was finalized in October 2026. The project is intentionally in a quiet operating phase while AdSense approval remains external and pending.

The production system separates four concerns:

1. **Publishing correctness** — can this resource safely become a real page?
2. **Site delivery** — can the visitor get a fast, useful, crawlable page?
3. **Owner operations** — can the owner inspect and make bounded changes without creating a second source of truth?
4. **Growth feedback** — what should be researched or improved next?

Those systems exchange data, but none silently takes control of the others.

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
static hubs + detail pages + search data + feeds + responsive media
        ↓
Cloudflare Workers Static Assets
        ↓
edge-cached public site
```

This fits the product because most resource pages are read-heavy and change through publishing events rather than per-request transactions.

Benefits include predictable crawlable HTML, fast edge delivery, fewer runtime failure modes, simple cache behavior, and deterministic production output.

---

## 3. Typed publishing model

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

Collection-level validation protects invariants that an individual record cannot check alone, such as duplicate IDs, duplicate effective routes, malformed relationships, and collisions across published resources.

The important rule is:

> becoming representable in the schema is not the same as becoming publishable.

---

## 4. Reader-first rendering

The public page does not expose every internal field just because the publishing model tracks it.

Gaming code guides are a good example. Internally, individual codes can carry evidence and freshness state. Publicly, the page is organized around the reader's task: usable code history, redemption steps, troubleshooting, game context, and updates.

Current, reported, and expired entries can remain visible when useful. Strict evidence stays behind the scenes; natural article language stays on the page.

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
 └── responsive media
```

This lets the platform grow across different resource categories while keeping publishing policy centralized.

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

This keeps lightweight personalization possible without requiring an account backend.

---

## 7. Owner control room

The production project has a separate owner control room rather than relying on raw repository files as the day-to-day operating interface.

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

Key boundaries:

- Git remains canonical for publications and final media.
- D1 stores recoverable operational state and action history rather than becoming a second content database.
- owner mutations are identity-bound and restricted to expected operations.
- browser actions do not get an arbitrary direct-to-`main` write surface.
- repository operations stay narrow and auditable.

---

## 8. Privacy-bounded analytics

Owner analytics combines signals that answer different questions without pretending they are interchangeable.

The production control room can surface aggregate information from areas such as:

- Google Search Console query and page performance
- Cloudflare Web Analytics traffic and real-user performance
- publication metadata and freshness
- bounded first-party interaction signals when explicitly enabled
- operational media and publication state

Search visibility, site traffic, page interactions, and advertising data remain separately defined inputs. Reporting can suggest where to investigate next; it cannot grant publishing permission.

---

## 9. GitHub-native Daily Publisher

FreeFindsHub has recurring publishing automation, but generic repository auto-merge is not the model.

The scheduler runs from trusted repository automation and gives the research/writing model a narrow job: propose one to three new structured production records and, when useful, one directly relevant image candidate per article.

Conceptually:

```text
scheduled research
        ↓
structured article + image-source metadata
        ↓
trusted deterministic staging
        ├── validate publication payload
        ├── download selected media once
        ├── decode / validate / crop
        └── create article-owned responsive variants
        ↓
narrow branch + PR
        ↓
exact-revision policy checks
        ↓
main
        ↓
normal production build
```

The research model does not choose final repository media paths and does not receive shell access, deployment authority, workflow mutation, or arbitrary repository writes.

The Daily Publisher is the only scheduled GitHub Actions workflow. The production schedule currently runs daily at 23:30 Asia/Colombo.

---

## 10. Trusted editorial media

Article eligibility and media availability are related but not identical.

A strong article can publish without a hero image. When a useful image candidate is selected, the trusted stage materializes it into FreeFindsHub-owned delivery assets.

High-level flow:

```text
relevant source page / direct image
        ↓
one-time trusted download
        ↓
format / size / decode validation
        ↓
16:9 editorial crop
        ↓
640 / 960 / 1440 WebP variants
        ↓
local article media + provenance metadata
```

Important properties:

- the public site does not hotlink the source image;
- the source host is not required at render time;
- the record retains owner/publisher/developer credit, source-page URL, original image URL, import time, dimensions/MIME type, and source hash;
- if import fails, the article can still publish without an image;
- an absent image does not produce a fake placeholder;
- Gaming strongly prefers recognizable real game imagery where suitable instead of generic generated replacement art;
- another publication's edited/watermarked thumbnail is not the preferred source.

The owner control room still supports bounded media add/replace operations and a Delete image action. Deleting a managed hero removes its record binding and article-owned variants while leaving the article live.

---

## 11. SEO architecture

Technical SEO is generated from the same validated content state as the visible page.

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
- image/search discovery considerations

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

Demand can tell the editorial process where to look, but demand alone does not make a page useful or trustworthy.

---

## 13. GitHub Actions cost boundary

The production repository intentionally minimizes hosted-runner usage.

The Daily Publisher is the only scheduled workflow. Narrow owner-media and recovery workflows exist only for their specific trusted boundaries.

One-time migration/backfill automation is removed when its job is done. The October 2026 existing-article image refresh/rescue batch was explicitly cleaned out before the stable pre-AdSense release.

This is a product decision as much as a cost decision: temporary operational machinery should not quietly become permanent architecture.

---

## 14. Monetization architecture

Advertising is modeled as a separately gated system.

The repository can support AdSense verification, `ads.txt`, manual-unit configuration, reserved placements, and later measurement, but live serving remains behind external owner/provider readiness.

The stable release is currently waiting for AdSense approval. The initial plan is deliberately controlled:

1. satisfy account/consent prerequisites;
2. enable only the first small manual baseline;
3. verify UX/performance/no-fill behavior;
4. collect real revenue and traffic evidence;
5. test stronger placements or formats one controlled change at a time.

Affiliate/referral monetization is a separate future path and should remain provider-scoped and transparent rather than becoming a generic redirect layer.

---

## 15. Why the architecture stays deliberately boring

A large part of this project is choosing what *not* to make dynamic.

The common reading path stays static. Automation writes through narrow structured boundaries. Search data is advisory. Media is self-hosted after trusted validation. Admin is bounded. Monetization fails closed.

That combination keeps the core product understandable:

```text
real demand
   ↓
verified useful resource
   ↓
relevant durable media
   ↓
fast static page
   ↓
discovery / action
   ↓
measured, transparent monetization
```

The production repository remains private; this public showcase intentionally stops at the architecture and engineering decisions rather than exposing privileged implementation details.
