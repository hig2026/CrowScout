# CrowScout Product Specification

## 1. Purpose

CrowScout helps a user inspect an authorised website that presents searchable listings and individual profile pages. It extracts user-selected fields from accessible profiles, stores the results locally, and enables flexible searches over every discovered attribute.

The defining capability is **profile-level search beyond the source site's search form**. If a site's form does not offer an age, language, price or description filter, CrowScout can still filter those fields after collecting the accessible profiles.

CrowScout must clearly state that local filtering applies only to profiles actually collected. It cannot query records the website has not exposed to the user.

## 2. Target website pattern

- A search or category page with form controls.
- Result cards, often with thumbnail images and profile links.
- Numbered pagination, previous/next controls or infinite scroll.
- Individual profile/detail pages with multiple labelled or unlabelled attributes.
- Optional site-native language selection.
- Potentially mixed-language, untranslated or imperfectly translated user-generated content.

## 3. User stories

- As a user, I can paste a URL and explicitly grant access to that website.
- I can ask CrowScout to inspect the page and identify forms, cards, profile links and pagination.
- I can choose a site-provided language such as English or Russian and have CrowScout verify that the page changed.
- I can nominate one profile as an example so the extension can learn its fields.
- I can correct field names and save the site mapping for reuse.
- I can extract selected fields from profiles across a bounded number of result pages.
- I can filter by any discovered field, even when the source search form does not expose it.
- I can search original and translated text without losing the original.
- I can inspect explanations for suspicious or inconsistent values.
- I can stop a scan, delete local records and export selected results.

## 4. Functional requirements

### FR-01: URL and permission
Accept an HTTP(S) starting URL. Validate the URL, identify its origin and request the narrowest practical optional host permission only after user action. Display the origin and permission state.

### FR-02: Discovery
Inspect the rendered page DOM to identify likely forms, inputs, result cards, profile links and pagination controls. Show confidence and allow user correction where discovery is ambiguous.

### FR-03: Site language
Detect common language selectors: links, dropdowns, flag controls and site-specific toggles. Use the site's ordinary UI or documented public URL pattern. Wait for navigation or content changes and verify the result from page language/text. Never assume a click succeeded.

### FR-04: Example profile learning
Allow the user to choose a profile link or open one example profile. Inspect visible DOM content and infer fields from labels, tables, definition lists, headings and nearby values. Present a mapping review step.

### FR-05: Extraction
Traverse only user-authorised, accessible pages. Use bounded page/profile limits and conservative pacing. Extract only selected fields and required provenance, including canonical URL and scan time. Preserve raw values.

### FR-06: Translation
Support a layered model:
1. Website interface language selected by the native language adapter.
2. Site-provided translated text, stored as a distinct rendition.
3. Optional external translation for untranslated or poor-quality segments, only after explicit opt-in.

Keep source text, detected language, provider, target language and translation status. Translation results are assistive and may be imperfect.

### FR-07: Flexible filtering
Provide a simple rule builder:
- Numeric: equals, greater/less than, between.
- Text: contains, excludes, exact match.
- Language/list: contains any, contains all.
- Date: before, after, within interval.
- Boolean: yes/no.
- Presence: is present/is missing.
Support AND/OR grouping and search across all text fields.

### FR-08: Data quality
Flag possible outliers, placeholder patterns, excess decimal precision, repeated defaults and field inconsistencies. Do not classify an individual as fraudulent. Explain each flag and retain the unmodified source value.

### FR-09: Storage and export
Store records, templates, filters and scan state locally. Export CSV and JSON. Provide record-level and whole-scan deletion.

### FR-10: Resilience
Detect navigation errors, changed layouts, blocked pages and rate-limit responses. Pause or stop safely; never attempt to bypass access restrictions.

## 5. Field data model

A profile record should include:
- profileId (internal stable identifier)
- sourceUrl
- canonicalUrl (when reliably determined)
- siteId / templateId
- collectedAt
- rawFields: mapping of field key to original displayed value and source label
- normalisedFields: optional typed representations with parsing metadata
- textRenditions: original, site-provided translation and optional machine translation, each with language and provenance
- qualityFlags: explainable rule results, severity and evidence
- extractionStatus and field-level confidence

Never discard raw text when normalising currency, dates, numbers or languages. A failed parse should remain visible as unnormalised source text.

## 6. Non-functional requirements

- Least privilege and clear consent.
- Local-first processing by default.
- No remote executable code.
- Predictable, visible scan limits and rate pacing.
- Accessible and understandable UI.
- Graceful recovery from navigation and extraction failures.
- Testable adapters and deterministic core logic.
