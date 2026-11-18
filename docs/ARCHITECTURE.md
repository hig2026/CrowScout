# CrowScout Architecture

## 1. Architectural principles

1. Local-first: profile records remain in the browser unless the user explicitly invokes an external service or export.
2. Least privilege: request optional access only to the site the user selected.
3. Deterministic first: DOM inspection, field mapping and filtering should be explainable. Optional AI can assist ambiguous mapping but must not be required.
4. Provenance: retain source URL, original label/value, collection time and translation provenance.
5. Bounded crawling: explicit page/profile limits, pacing, visible controls and safe stop/resume.
6. Reusable adapters: keep a generic engine while allowing per-site templates for selectors and navigation.

## 2. Runtime components

### Side Panel
React UI that remains available while the user browses. Screens:
- Start / URL and permissions
- Discovery preview
- Language selection
- Example profile and field mapping
- Scan configuration and progress
- Filters and results
- Template and data management

### Background service worker
Owns scan orchestration and durable state transitions. It should not assume it remains alive continuously. Persist checkpoints in IndexedDB and reconstruct state after worker suspension.

### Content scripts
Run only on origins for which access has been granted. Inspect rendered DOM, identify controls and extract visible page content. Keep injected code packaged with the extension.

### Site discovery engine
Classifies the current page as search/listing/detail/unknown. Detects forms, result cards, profile links, pagination and likely language controls. Use confidence-ranked heuristics and let the user confirm ambiguous elements.

### Native language adapter
A generic detector for language links, select controls, flags and URL-language patterns. The adapter should use the site's ordinary navigation and verify that the resulting content changed. Site-specific rules may be stored in a user-approved template.

### Profile schema learner
Extract candidate field/value pairs from semantic HTML and rendered text. Present mappings for user confirmation. Do not infer sensitive or hidden information from images or unrelated signals.

### Translation pipeline
Keep each rendition distinct:
- original
- website-provided translation
- optional external translation

Translation provider calls must be explicit, limited to selected text and disclosed before content leaves the browser. Cache translated segments locally with provider and language metadata. Avoid translating the same segment repeatedly.

### Extraction engine
Uses confirmed templates plus generic fallback heuristics. Each extracted field includes raw label, raw value, source element context where practical, and extraction confidence.

### Query engine
Runs local predicates against typed and raw fields. Text search should support original and translated renditions. Make clear which text rendition is being searched.

### Quality engine
Runs transparent rules over numeric fields, duplicate text, placeholder patterns and internal inconsistencies. Flags include rule identifier, evidence, severity and explanation. No opaque fraud probability.

### Storage
IndexedDB stores profiles, text renditions, templates, scan jobs, checkpoints and saved filters. chrome.storage.local stores non-sensitive preferences and settings. Avoid storing credentials, cookies or session tokens.

## 3. Suggested data flow

1. User enters URL and triggers permission request.
2. Extension validates the URL and obtains optional host access.
3. Content inspection classifies page and discovers controls.
4. User optionally selects site language; adapter navigates and verifies.
5. User confirms example profile and field mapping.
6. Orchestrator traverses bounded listing pages and profile links.
7. Extracted values are normalised without discarding raw source values.
8. Language detection determines which segments need optional translation.
9. Query and quality engines index local records.
10. User filters, reviews flags and exports.

## 4. Manifest V3 considerations

- Use a service worker background.
- Use optional_host_permissions rather than blanket all-site access.
- Request permissions through user gestures and runtime permission APIs.
- Use activeTab for temporary access where appropriate.
- Package all executable extension code locally.
- Do not load remote scripts or evaluate remote code.
- Use alarms or persisted checkpoints only where needed; do not rely on long-lived service-worker memory.

## 5. Navigation strategy

Start with same-tab navigation for the smallest reliable MVP. Preserve the user's original URL and restore it after a scan if navigation is controlled by the extension. Background-tab concurrency can be considered later, subject to clear consent and site behaviour.

For each page:
- Record URL and page classification.
- Wait for navigation completion and a stable DOM condition.
- Detect duplicate URLs and pagination loops.
- Stop on access-denied, CAPTCHA, paywall, unexpected authentication or clear rate-limit signals.
- Persist progress before moving to the next page.

## 6. Test strategy

Build fixture pages for:
- A static listing with numbered pagination.
- A listing using next/previous links.
- Infinite scroll.
- A JavaScript-rendered listing.
- A profile with table-based fields.
- A profile with label/value divs.
- A mixed-language profile.
- A native language selector that changes URL.
- A selector that changes content without navigation.
- Malformed prices and duplicate/default values.
- A layout that changes after the template is saved.

Test parsers and filters with deterministic unit tests; use Playwright for extension integration tests where feasible.
