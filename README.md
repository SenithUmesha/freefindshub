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

## stable production shape

**Stable pre-AdSense baseline finalized: October 2026.**

FreeFindsHub is live from a private production repository whose stable production branch is `main`. This public repository mirrors the released product and engineering shape without exposing private content, credentials, operational configuration, or privileged workflow implementation.

The production baseline now includes:

- the consolidated five-hub public taxonomy: **Gaming, Freebies, Software, Perks, and Tools**
- reader-first gaming code guides that keep useful current, reported, and expired history visible
- static search, filters, freshness surfaces, saved finds, recently viewed, and related-resource discovery
- typed publishing gates and deterministic build-time validation
- a GitHub-native Daily Publisher with a tightly bounded structured write surface
- **one scheduled GitHub Actions workflow**: the Daily Publisher
- publisher-selected directly relevant editorial image candidates, with final media materialized by a trusted staging layer
- self-hosted responsive 640/960/1440 WebP media instead of public hotlinks
- visible owner/publisher/developer credit and retained source/provenance metadata
- real game imagery preferred for Gaming where suitable instead of generic generated replacements
- media failures that do not block a verified article; image-less pages simply render without a placeholder
- a separate owner control room protected by Cloudflare Access, with Git remaining canonical for publications and final media
- bounded owner media controls including add/replace/delete operations
- privacy-bounded analytics and Search Console reporting
- canonical/sitemap/robots/structured-data foundations, Cloudflare Web Analytics, RSS, and trusted IndexNow notification
- advertising readiness that remains separately gated by Google/account approval and owner configuration

The project is intentionally in a quiet operating phase until AdSense approval. The platform is not being expanded just to keep shipping features.

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

The priority is usefulness and search intent, not hitting a page-count target.

---

## reader-first article surfaces

The public site is deliberately less technical than the system behind it.

Gaming code pages, for example, keep useful code history visible instead of turning the page into an internal verification dashboard. Current, reported, and expired codes can remain readable with redemption steps, troubleshooting, game context, and update information around them.

The same editorial rule applies elsewhere: strict evidence and automation metadata stay behind the scenes, while the public page leads with the thing a reader actually came to get.

---

## publishing with guard rails

Production resources use a typed local content model with publish gates for things like:

- canonical identity and route uniqueness
- source and action URLs
- publication, verification, and expiry state
- relationships between resources
- article structure and useful content depth
- canonical image alt text
- optional media metadata
- indexability

Collection-level validation protects invariants that an individual record cannot check alone.

That means an invalid resource is a build or publishing-policy problem, not something I hope to notice after a crawler finds it.

---

## a bounded Daily Publisher

FreeFindsHub has recurring autonomous publishing, but it is deliberately narrow.

The scheduled publisher researches one to three strong new resources using current web evidence. It returns a structured payload rather than receiving arbitrary repository authority.

For each article it may also select one directly relevant image candidate. The research stage supplies source metadata and credit; a trusted deterministic stage owns the final local media paths and repository writes.

Conceptually:

```text
scheduled research
        ↓
structured article + image-source metadata
        ↓
trusted validation / media materialization
        ↓
article-owned responsive assets + publication record
        ↓
narrow PR / exact revision merge
        ↓
main
        ↓
normal production build
```

The useful security property is simple: a research/writing model does not become a general repository administrator just because it can propose content.

---

## real editorial media without hotlinking

The current media path intentionally separates **image selection** from **trusted materialization**.

When a useful source image is selected:

```text
relevant source page / direct image
        ↓
one-time trusted download
        ↓
decode + validation + editorial crop
        ↓
640 / 960 / 1440 WebP variants
        ↓
self-hosted FreeFindsHub media
```

The original remote image is not used as a public runtime dependency. If the source host later renames or removes the file, the published page does not break.

For Gaming, recognizable real game imagery—gameplay, characters, update/key art, official game/store/news/social imagery, or another clearly relevant game visual—is preferred over generic generated art.

If media import fails, a verified article can still publish. There is no fake placeholder card pretending an image exists.

The owner control room can also add/replace media and delete a managed hero. Deletion leaves the article live without a placeholder.

---

## the owner control room

The project has a separate owner-only control room rather than treating raw GitHub files as the day-to-day editorial UI.

It runs as an isolated Cloudflare Worker behind Cloudflare Access and presents operational views for areas such as:

- publication status
- aggregate site statistics
- privacy-bounded analytics and Search Console data
- media state
- bounded owner operations and health

Git remains the source of truth for canonical publication data and final media. D1 is bounded operational state rather than a second CMS.

---

## discovery is part of the product

FreeFindsHub is not just a pile of SEO landing pages.

The platform includes:

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

## SEO as an engineering system

Technical SEO is part of the application architecture rather than an afterthought.

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

Search and traffic data can guide research, but those signals do not bypass editorial or publishing gates.

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

Owner operations stay separate:

```text
Cloudflare Access
        ↓
owner control-room Worker
        ├── publications
        ├── statistics / analytics
        ├── media
        └── bounded operations / audits
        ↓
GitHub + bounded D1 operational state
```

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
| Media | Relevant publisher/owner-selected sources → trusted local 640/960/1440 WebPs with credit/provenance |
| Automation | GitHub-native Daily Publisher; only scheduled Actions workflow |
| Measurement | Search Console + Cloudflare Web Analytics + private owner reporting |
| Monetization | Repository-ready, externally gated AdSense activation |

The production source repository is private. This public repository is intentionally documentation-first: it shows the product, architecture, and engineering decisions without publishing operational configuration, private reporting, privileged workflow implementation, or the production content registry itself.

---

## monetization without turning the page into a trap

FreeFindsHub is designed to support advertising, but monetization is treated as another gated subsystem rather than permission to cover every page in units.

The product still has to deliver a useful answer, code, claim path, comparison, or tool. Advertising activation is kept separate from content eligibility and depends on external account, policy, consent, and owner readiness.

The stable release is currently waiting for AdSense approval. The initial live plan is deliberately controlled: establish a small manual baseline first, measure it, then test stronger placements one change at a time.

---

## decisions i care about

**a resource must earn its indexable page**  
Route support is not permission to publish thin or invented content.

**public copy should sound like a useful article, not an audit log**  
Evidence and automation state can stay strict internally without leaking into the reader experience.

**automation gets a tiny box to work inside**  
Automated publishing is useful precisely because it is constrained.

**admin is an owner tool, not a second source of truth**  
Canonical publication data stays in Git.

**media should be relevant and durable**  
Use the real subject when practical, self-host trusted variants, keep credit/source metadata, and do not make the public page depend on a third-party image URL.

**a missing image is not a broken page**  
If media is absent or deleted, the page simply renders without it.

**search data suggests; it does not publish**  
Growth reports help prioritize research. They do not bypass content policy.

**static-first is a feature**  
Most resource pages do not need a database request just to tell a visitor what is available.

**monetization should fail closed**  
Ads are downstream of useful content and policy readiness, not the other way around.

---

## about this repository

This is the **public product + engineering showcase** for FreeFindsHub.

The production application, owner control-room implementation, publishing automation, private growth reports, content registry, deployment configuration, and privileged workflow details remain in a private repository.

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
