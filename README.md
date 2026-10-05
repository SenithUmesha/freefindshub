<p align="center">
  <img src="https://freefindshub.com/favicon.svg" width="96" alt="FreeFindsHub icon" />
</p>

<h1 align="center">FreeFindsHub</h1>

<p align="center">
  <strong>Useful free things, without the junk.</strong>
</p>

<p align="center">
  A search-first discovery platform for genuinely useful free digital resources, offers, tools, templates, courses, gaming finds, and downloads.
</p>

<p align="center">
  <a href="https://freefindshub.com/">Live site</a>
  ·
  <a href="docs/engineering.md">Engineering notes</a>
  ·
  <a href="https://senithumesha.com/">Portfolio</a>
</p>

<p align="center">
  <code>Astro</code> · <code>TypeScript</code> · <code>Cloudflare Workers</code> · <code>SEO</code> · <code>GitHub Actions</code> · <code>static-first</code>
</p>

<p align="center">
  <a href="https://github.com/SenithUmesha/freefindshub/actions/workflows/docs-check.yml"><img src="https://github.com/SenithUmesha/freefindshub/actions/workflows/docs-check.yml/badge.svg" alt="Docs integrity" /></a>
</p>

---

## why i built it

The web is full of pages promising something free and then making you fight through expired offers, fake buttons, thin affiliate pages, recycled lists, or ten paragraphs before getting to the actual thing.

FreeFindsHub is my attempt at the opposite:

```text
search for something useful
        ↓
land on a page that explains exactly what it is
        ↓
see source + freshness + eligibility context
        ↓
get the real action/download link
        ↓
discover related useful resources
```

The interesting engineering problem is not just rendering pages. It is building a publishing system where search visibility, content quality, provenance, media rights, freshness, automation, and monetization all have to agree before something is allowed to become a real public page.

---

## what FreeFindsHub covers

The platform supports multiple resource families rather than one giant generic feed:

- deals and free digital offers
- career resources
- templates
- AI resources
- gaming freebies and codes
- fonts and wallpapers
- student resources
- mobile resources
- promotions
- courses
- downloads and owned utilities

The priority is usefulness and search intent, not hitting a page-count target.

Every publishable record carries structured information about its source, destination, freshness, indexability, rights/provenance where relevant, and the content depth needed to justify a standalone page.

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
- timestamps and expiry state
- relationships between resources
- image/media paths
- provenance and licensing/rights metadata
- indexability
- minimum useful content depth

The build discovers reviewed production records deterministically and validates the whole collection before it can ship.

That means an invalid resource is a build problem, not something I hope to notice after Google crawls it.

---

## safe automation instead of generic auto-merge

FreeFindsHub has an autonomous publishing path, but it is deliberately narrow.

The automation can work inside a content-only boundary; it does **not** get a general-purpose path to change application code, workflows, deployment configuration, or repository policy.

The trusted merge side verifies the exact reviewed commit and successful CI result before a content update can move forward. Automation-produced media goes through its own validation/materialization boundary rather than trusting arbitrary remote files.

This was much more interesting to build than a script that simply generates Markdown and pushes it to `main`.

---

## media that can be explained later

Resource imagery is treated as publishable data, not decoration copied from somewhere and forgotten.

The production pipeline tracks provenance for reviewed media and validates expected paths and image constraints before publication. Automated media ingestion is intentionally bounded to approved providers and produces optimized local assets only after validation.

The goal is that a page can answer two separate questions:

1. **Does this image actually match the resource?**
2. **Can I explain where it came from and why it is allowed to be here?**

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

The growth side is intentionally measurement-driven: Search Console and aggregate traffic signals can feed private reporting and opportunity scoring, but those signals do not bypass editorial, provenance, or publishing gates.

---

## architecture

```text
reviewed resource data
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

Around that path are two separate systems:

```text
publishing automation                 growth feedback
        ↓                                  ↓
content-only proposal                 Search Console
        ↓                                  +
validation / CI                       aggregate site signals
        ↓                                  ↓
trusted merge boundary                private diagnostics
        ↓                                  ↓
production build                      human editorial decisions
```

Keeping those responsibilities separate matters. Search performance can suggest where to look next, but it cannot grant itself permission to publish something.

---

## under the hood

| Area | Choice |
| --- | --- |
| Framework | Astro |
| Language | TypeScript |
| Rendering | Static-first |
| Hosting | Cloudflare Workers + Static Assets |
| Content | Typed local structured records |
| Search | Build-generated static search index |
| SEO | Canonicals, sitemap, JSON-LD, RSS, diagnostics |
| Discovery | Search, filters, related content, saved/recent state |
| Media | Reviewed local assets + provenance-aware validation |
| Automation | GitHub Actions with narrow content-only boundaries |
| Growth | Search Console + private consolidated reporting |
| Monetization | Gated, fail-closed advertising readiness |

The production source repository is private. This public repository is intentionally documentation-first: it shows the product, architecture, and engineering decisions without publishing operational configuration, private reporting, privileged workflows, or the production content pipeline itself.

---

## monetization without turning the page into a trap

FreeFindsHub is designed to support advertising, but monetization is treated as another gated subsystem rather than permission to cover every page in units.

The production architecture has explicit content-depth budgets, control/CTA exclusion zones, indexability checks, and owner-controlled activation. Advertising readiness fails closed until the required external account, policy, consent, and production checks are actually satisfied.

The page still has to be useful if you mentally remove every ad slot from it.

---

## a few decisions i care about

**a resource must earn its indexable page**  
The schema can represent many verticals, but route support is not permission to publish thin or invented content.

**source and freshness are product UI**  
People should be able to understand where a claim came from and whether the resource is still current.

**automation gets a tiny box to work inside**  
Automated publishing is useful precisely because it is constrained. Content automation should not silently become repository administration.

**search data suggests; it does not publish**  
Growth reports help prioritize research. They do not bypass human review or source/rights checks.

**static-first is a feature**  
Most resource pages do not need a database request just to tell a visitor what is available. Static output keeps the common path fast, cacheable, and crawl-friendly.

**monetization should fail closed**  
Ads are downstream of useful content and policy readiness, not the other way around.

---

## about this repository

This is the **public product + engineering showcase** for FreeFindsHub.

The production application, publishing automation, private growth reports, content registry, deployment configuration, and privileged workflow details remain in a private repository.

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
