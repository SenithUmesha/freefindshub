<p align="center">
  <img src="https://freefindshub.com/favicon.svg" width="96" alt="FreeFindsHub icon" />
</p>

<h1 align="center">FreeFindsHub</h1>

<p align="center">
  <strong>Useful free things, without the junk.</strong>
</p>

<p align="center">
  A utility-first geek publisher for current gaming codes and rewards, real free offers, useful software and AI free tiers, perks, tools, and templates.
</p>

<p align="center">
  <a href="https://freefindshub.com/">Live site</a>
  ·
  <a href="docs/engineering.md">Engineering notes</a>
  ·
  <a href="https://senithumesha.com/">Portfolio</a>
</p>

<p align="center">
  <code>Astro</code> · <code>TypeScript</code> · <code>Cloudflare Workers</code> · <code>Cloudflare Access</code> · <code>GitHub Actions</code> · <code>SEO</code> · <code>static-first</code>
</p>

<p align="center">
  <a href="https://github.com/SenithUmesha/freefindshub/actions/workflows/docs-check.yml"><img src="https://github.com/SenithUmesha/freefindshub/actions/workflows/docs-check.yml/badge.svg" alt="Docs integrity" /></a>
</p>

---

## current production shape

FreeFindsHub is live from a private production repository whose stable production branch is `main`. This public repository mirrors the released product and engineering shape without exposing private content, credentials, operational configuration, or privileged workflow implementation.

The current production baseline includes:

- the consolidated five-hub public taxonomy: Gaming, Freebies, Software, Perks, and Tools
- reader-first gaming code guides that keep current, reported, and expired code history useful instead of hiding old entries
- static search, filters, freshness surfaces, saved finds, recently viewed, and related-resource discovery
- typed publishing gates and deterministic build-time validation
- a GitHub-native Daily Publisher with a tightly bounded content-only write surface
- a separate owner control room protected by Cloudflare Access, with Git remaining canonical for publications and final media
- an owner editorial-media workflow that can add responsive original artwork after an article is already publishable
- privacy-bounded analytics and Search Console reporting inside the owner control room
- canonical/sitemap/robots/structured-data foundations, Cloudflare Web Analytics, and trusted IndexNow notification
- advertising readiness that remains separately gated by owner and provider prerequisites

---

## why i built it

The web is full of pages promising something free and then making you fight through expired offers, fake buttons, thin affiliate pages, recycled lists, or ten paragraphs before getting to the actual thing.

FreeFindsHub is my attempt at the opposite:

```text
search for something useful
        ↓
land on a page that explains exactly what it is
        ↓
get the useful answer, code, claim path, or tool
        ↓
understand freshness / eligibility where it matters
        ↓
discover related useful resources
```

The interesting engineering problem is not just rendering pages. It is building a publishing system where search visibility, content quality, freshness, media, automation, measurement, and monetization all have to agree before the site grows safely.

---

## what FreeFindsHub covers

The public information architecture is intentionally compact:

- **Gaming** — codes, rewards, free claims, and unlock utilities
- **Freebies** — real free offers and high-value promotions
- **Software** — software, AI, and mobile utilities or useful free tiers
- **Perks** — student and creator benefits
- **Tools** — FreeFindsHub-owned tools, templates, and practical assets

Older public detail URLs remain stable where a page survived the consolidation audit, while retired or merged hub routes are redirected or removed according to their reviewed disposition. The priority is usefulness and search intent, not hitting a page-count target.

Every publishable record carries structured information about its identity, source, destination, freshness, indexability, article content, and media state where relevant.

---

## reader-first article surfaces

The public site is deliberately less technical than the system behind it.

Gaming code pages, for example, keep useful history visible instead of turning the page into an internal verification dashboard. Current, reported, and expired codes can remain readable with practical redemption steps, troubleshooting, game context, and update information around them.

The same editorial rule applies elsewhere: internal evidence and automation metadata stay behind the scenes, while the public page leads with the thing a reader actually came to get.

---

## discovery is part of the product

FreeFindsHub is not just a pile of SEO landing pages.

The current platform includes:

- searchable eligible-resource index
- deterministic ranking across titles, tags, metadata, and useful page text
- controlled filters and sorts
- newest, recently verified, and expiring discovery surfaces
- related-resource fallbacks
- local **Saved Finds**
- local recently viewed history
- crawl-safe canonical detail pages behind the discovery UI

Saved and recently viewed state stay in the browser. No account is required just to keep a small personal list of finds.

---

## publishing with guard rails

A big part of the project is the content pipeline behind the site.

Production resources use a typed local content model with publish gates for things like:

- canonical identity and route uniqueness
- source and action URLs
- publication, verification, and expiry state
- relationships between resources
- article structure and useful content depth
- canonical image alt text
- optional media paths
- indexability

Collection-level validation also protects invariants that an individual record cannot check alone.

That means an invalid resource is a build or publishing-policy problem, not something I hope to notice after Google crawls it.

---

## a bounded Daily Publisher

FreeFindsHub has recurring autonomous publishing, but it is deliberately narrow.

The scheduled publisher researches and drafts only new structured article records. It cannot change routes, schemas, dependencies, scripts, workflows, deployment configuration, or arbitrary repository files. A deterministic staging layer accepts only the expected record shape and writes only the production content path.

The production schedule runs through GitHub-native automation. New-content pull requests are kept as a review and audit trail, checked against the exact intended revision and content-only policy, and then production deployment proceeds from `main`.

The useful security property is simple: the research/writing agent does not receive general repository authority just because it can propose an article.

---

## the owner control room

The project now has a separate owner-only control room rather than treating GitHub itself as the day-to-day editorial UI.

It runs as an isolated Cloudflare Worker behind Cloudflare Access and presents operational views for areas such as:

- publication status and review state
- aggregate site statistics
- privacy-bounded analytics and Search Console data
- media attention queues
- owner actions and operational health

Git remains the source of truth for canonical publication data and final media. D1 is used for bounded operational state, reconciliation, and action history rather than as a second CMS database.

The control room is intentionally not a general browser-to-production editor. Mutating actions are constrained, identity-bound, and represented through narrow repository operations instead of a hidden direct-to-`main` write path.

---

## original editorial media, added separately

Article quality and image state are separate concerns.

A strong article can publish without a hero image. The owner control room can later show the article context and a deterministic article-specific image prompt, then accept an owner-created source image, crop it to the editorial ratio, and create responsive WebP variants for the site.

That keeps the content publisher focused on research and writing while keeping visual direction in an explicit owner workflow. It also avoids turning autonomous publishing into an image-scraping or licensing-research agent.

The current media workflow is intentionally simple: **Needs image** or **Image added**. Final media still lands in Git with the publication record rather than living as an unexplained hotlink.

---

## SEO as an engineering system

The project treats technical SEO as part of the application architecture.

Current pieces include:

- canonical URLs
- filtered sitemap output
- robots handling
- Open Graph metadata
- visible and JSON-LD breadcrumbs
- record-derived structured data
- RSS discovery feed
- clean static route generation
- indexability controls for raw assets and query states
- Search Console reporting
- deterministic SEO diagnostics
- trusted post-publish IndexNow submission
- image/search discovery considerations

The growth side is intentionally measurement-driven: Search Console and aggregate traffic signals can feed private reporting and opportunity review, but those signals do not bypass editorial or publishing gates.

---

## architecture

```text
reviewed / publisher-produced resource data
        ↓
typed content schema + publish gates
        ↓
Astro static generation
        ├── hub pages
        ├── resource detail pages
        ├── search index
        ├── structured data
        ├── sitemap / feeds
        └── responsive media
        ↓
Cloudflare Workers Static Assets
        ↓
freefindshub.com
```

The owner side is deliberately separate:

```text
Cloudflare Access
        ↓
owner control-room Worker
        ├── publications
        ├── statistics / analytics
        ├── media queue
        └── bounded operations / audits
        ↓
GitHub + bounded D1 operational state
```

And publishing stays narrow:

```text
scheduled research + writing
        ↓
bounded structured payload
        ↓
deterministic content-only staging
        ↓
exact-revision policy checks + PR audit trail
        ↓
main
        ↓
normal production build / deploy
```

Keeping those responsibilities separate matters. Search performance can suggest where to look next, Admin can make bounded owner changes, and the publisher can draft new articles, but none of those surfaces silently gains unlimited production authority.

---

## under the hood

| Area | Choice |
| --- | --- |
| Framework | Astro |
| Language | TypeScript |
| Rendering | Static-first |
| Public hosting | Cloudflare Workers + Static Assets |
| Owner control room | Separate Cloudflare Worker behind Cloudflare Access |
| Canonical content | Typed local structured records in Git |
| Operational state | Bounded D1 state for Admin, not canonical content |
| Search | Build-generated static search index |
| SEO | Canonicals, sitemap, JSON-LD, RSS, diagnostics |
| Discovery | Search, filters, related content, saved/recent state |
| Media | Owner-created responsive WebP assets attached through a narrow Admin workflow |
| Automation | GitHub-native, content-only scheduled publishing |
| Measurement | Search Console + Cloudflare Web Analytics + private owner reporting |
| Monetization | Separately gated advertising readiness |

The production source repository is private. This public repository is intentionally documentation-first: it shows the product, architecture, and engineering decisions without publishing operational configuration, private reporting, privileged workflows, or the production content pipeline itself.

---

## monetization without turning the page into a trap

FreeFindsHub is designed to support advertising, but monetization is treated as another gated subsystem rather than permission to cover every page in units.

The product still has to deliver a useful answer, code, claim path, comparison, or tool. Advertising activation is kept separate from content eligibility and depends on external account, policy, consent, and owner readiness.

The page still has to be useful if you mentally remove every ad slot from it.

---

## a few decisions i care about

**a resource must earn its indexable page**  
The schema can represent many verticals, but route support is not permission to publish thin or invented content.

**public copy should sound like a useful article, not an audit log**  
Evidence, automation state, and verification machinery can stay strict internally without leaking that language into the reader experience.

**automation gets a tiny box to work inside**  
Automated publishing is useful precisely because it is constrained. A writing agent should not silently become repository administration.

**admin is an owner tool, not a second source of truth**  
The control room can make bounded changes and show operational state, while canonical publication data stays in Git.

**images are optional for publishing**  
A strong article should not be blocked because original editorial artwork has not been added yet.

**search data suggests; it does not publish**  
Growth reports help prioritize research. They do not bypass human judgment or content policy.

**static-first is a feature**  
Most resource pages do not need a database request just to tell a visitor what is available. Static output keeps the common path fast, cacheable, and crawl-friendly.

**monetization should fail closed**  
Ads are downstream of useful content and policy readiness, not the other way around.

---

## about this repository

This is the **public product + engineering showcase** for FreeFindsHub.

The production application, owner control-room implementation, publishing automation, private growth reports, content registry, deployment configuration, and privileged workflow details remain in a private repository.

This repo exists so I can share the architecture and the parts of the project I find interesting without turning a production publishing business into an open operational blueprint.

For more implementation detail, see **[docs/engineering.md](docs/engineering.md)**.

---

<p align="center">
  <strong>FreeFindsHub</strong><br />
  built by <a href="https://github.com/SenithUmesha">Senith Umesha</a>
</p>

<p align="center">
  <a href="https://freefindshub.com/">freefindshub.com</a>
  ·
  <a href="https://senithumesha.com/">senithumesha.com</a>
</p>
