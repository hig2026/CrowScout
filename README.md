# CrowScout

A local-first, user-authorised Chrome extension for discovering, extracting, filtering and assessing profile listings across multilingual websites.

## Project status

**Stage:** Product specification and architecture  
**Implementation:** Not yet started

CrowScout is designed for websites that present paginated or dynamically loaded listing pages (often thumbnail/profile cards) linking to individual profile/detail pages. It learns a site's structure, collects only user-selected information from pages the user is authorised to access, and lets the user search across any discovered profile field, including fields absent from the website's own search form.

## Core capabilities

1. **User-authorised website access**
   - User supplies a starting URL and explicitly grants access to the relevant site.
   - Request the narrowest practical host permissions; do not request blanket access to all websites.
   - Show the domain, planned scope, page/profile limits and selected fields before scanning.
   - Provide visible progress, pause and stop controls.

2. **Site discovery and reusable templates**
   - Discover search forms, listing cards, profile links and pagination controls.
   - Support numbered pagination, next/previous navigation and detectable infinite scrolling.
   - Learn individual profile fields from labels, tables, headings, definition lists, metadata and visible page text.
   - Allow a user to confirm or correct field mappings and save a reusable site template.
   - Re-check templates when page structure changes.

3. **Native website language first**
   - Detect website language controls and, when available, use the site's own language selector (for example Georgian → English or Russian).
   - Verify that the language actually changed and persists across listing and profile navigation.
   - Do not assume the website translates user-submitted profile descriptions.

4. **Hybrid multilingual content handling**
   - Preserve original text exactly as observed.
   - Detect the language of each relevant text field or segment where feasible.
   - Retain website-provided translations separately from original content.
   - Optionally translate residual or poorly translated content through a user-enabled provider.
   - Store translated text, source language, target language and translation provenance separately.
   - Search original, native-site-translated and optional translated text.
   - Translation must never silently overwrite source text.

5. **Profile extraction and local search**
   - Extract selected profile attributes and source URLs.
   - Support user-defined fields that do not exist in the site's native search form.
   - Filter extracted records locally using a simple rule builder (numeric, text, date, boolean, language and missing/present operators).
   - Clearly distinguish filtering the collected profiles from querying the website's full underlying database.
   - Deduplicate by stable profile identifier or canonical URL where possible.

6. **Data-quality and anomaly signals**
   - Flag possible placeholders, odd numeric precision, implausible outliers, repeated values and conflicting fields.
   - Compare numerical values only within suitable peer groups and account for currency/unit differences.
   - Treat flags as review signals, not proof of fraud or falsity.
   - Preserve raw values and explain each flag; never silently "correct" source data.

7. **Results and export**
   - Searchable local results with source-profile links.
   - CSV and JSON export.
   - Scan history, template management and deletion controls.

## Explicit non-goals and safety boundaries

- No bypassing CAPTCHA, paywalls, authentication, access controls or site restrictions.
- No credential collection, password storage, cookie extraction or hidden account-data access.
- No stealth, proxy rotation or evasion of rate limits.
- No automatic collection of every available field: collect only fields selected by the user and necessary for the stated scan.
- No claims that an anomaly score is a probability of fraud.
- No default upload of profile content to a remote service.
- Follow applicable law, site terms, robots/access guidance where relevant, and reasonable rate limits.

## Proposed user workflow

1. Paste a starting URL.
2. Grant access to that specific website when Chrome requests it.
3. Inspect the current page and identify language controls, search controls, result cards and pagination.
4. Optionally select a preferred website language and verify the change.
5. If necessary, click one example profile so CrowScout can learn the detail-page structure.
6. Review discovered fields and confirm the site template.
7. Choose scan limits and fields to collect.
8. Run the scan with visible progress and stop/pause controls.
9. Build filters over any discovered field, including fields not offered by the site's search form.
10. Review anomaly explanations and export selected results.

## Initial technical direction

- Chrome Extension Manifest V3
- TypeScript
- React-based Chrome Side Panel
- Chrome optional host permissions and runtime permission requests
- Content scripts / DOM inspection for rendered page content
- Background service worker for orchestration and recovery
- IndexedDB for local profile records, scan state and site templates
- chrome.storage.local for non-sensitive preferences
- Deterministic parsers and rules first; optional AI assistance only for ambiguous structure/field mapping
- Optional translation provider behind an explicit opt-in boundary

## Initial architecture

- **Side Panel UI:** URL input, permission status, scan configuration, field mapping, filters, results and exports.
- **Service Worker / Orchestrator:** scan lifecycle, navigation coordination, rate limiting, persistence and recovery.
- **Page Inspector:** forms, cards, links, pagination and profile-field discovery.
- **Native Language Adapter:** identifies and uses site-provided language controls.
- **Content Language Layer:** segment-level language detection, provenance and optional translation.
- **Extraction Engine:** site-template-based extraction with user-confirmed mappings.
- **Query Engine:** local filters across all extracted fields.
- **Data Quality Engine:** explainable anomaly rules and consistency checks.
- **Local Repository:** IndexedDB records, templates and scan history.

## MVP acceptance criteria

- [ ] Accept a user-supplied starting URL.
- [ ] Request narrow, explicit access to that site's origin.
- [ ] Identify a listing page containing profile cards or links.
- [ ] Detect and traverse ordinary pagination within a user-defined limit.
- [ ] Learn a profile page from a user-selected example.
- [ ] Extract at least ten configurable attributes from a representative test site.
- [ ] Filter on a discovered attribute that is absent from the website's own search form.
- [ ] Preserve original text and source URLs.
- [ ] Detect and use a site's own language selector when available, with a verification step.
- [ ] Keep original, site-translated and optional machine-translated text distinct.
- [ ] Flag suspicious numeric values with explanations without changing them.
- [ ] Deduplicate profiles and export results to CSV and JSON.
- [ ] Stop cleanly and resume safely without duplicating records.

## Suggested project layout

```text
CrowScout/
├── README.md
├── docs/
│   ├── PRODUCT_SPEC.md
│   ├── ARCHITECTURE.md
│   ├── SECURITY_AND_PRIVACY.md
│   ├── SITE_ADAPTERS.md
│   └── MVP_PLAN.md
├── src/
│   ├── background/
│   ├── content/
│   ├── sidepanel/
│   ├── core/
│   │   ├── discovery/
│   │   ├── extraction/
│   │   ├── language/
│   │   ├── filtering/
│   │   └── quality/
│   ├── adapters/
│   └── storage/
├── tests/
└── manifest.json
```

## Current decisions

- Product name: **CrowScout**
- Repository: https://github.com/hig2026/CrowScout
- Browser: Chrome, Manifest V3
- Primary model: local-first and user-authorised
- Website language: use native site translation first when available
- Residual profile text: optional segment-level translation, preserving originals
- Search: local filtering across any discovered field
- Extraction: generic engine plus reusable, user-confirmed site templates

## Open questions for implementation

- Which real website will be the first test fixture?
- Which translation provider, if any, should be supported first?
- Should the first MVP support only same-tab navigation, or also controlled background tabs?
- What initial default page/profile limits and delays should be exposed in the UI?
