# CrowScout MVP Plan

## Phase 0: Repository and fixtures
- [x] Establish product name and repository.
- [x] Capture initial product requirements and architecture.
- [ ] Create a small local fixture website with listing, pagination and profile pages.
- [ ] Agree first real-world test website and confirm authorised access.

## Phase 1: Extension foundation
- [ ] Scaffold Manifest V3 + TypeScript + React Side Panel.
- [ ] Add strict permission flow for a user-supplied origin.
- [ ] Add URL validation and scan configuration.
- [ ] Implement start/pause/stop state machine and persisted checkpoints.

## Phase 2: Discovery and learning
- [ ] Detect forms and listing cards.
- [ ] Detect profile links and ordinary pagination.
- [ ] Add example-profile selection.
- [ ] Extract candidate field/value pairs.
- [ ] Build field-mapping confirmation UI.
- [ ] Save and reload site templates.

## Phase 3: Extraction and local storage
- [ ] Implement bounded same-tab crawling.
- [ ] Extract selected fields from profiles.
- [ ] Deduplicate profile records.
- [ ] Store source provenance and scan checkpoints in IndexedDB.
- [ ] Add CSV/JSON export.

## Phase 4: Multilingual handling
- [ ] Detect site language controls.
- [ ] Support site-native language switching and verification.
- [ ] Detect language per text field/segment.
- [ ] Preserve original and site-translated text separately.
- [ ] Add optional provider-based translation with explicit disclosure/consent.
- [ ] Search across original and translated renditions.

## Phase 5: Filtering and quality analysis
- [ ] Implement numeric, text, date, list/language, boolean and missing/present operators.
- [ ] Support AND/OR groups.
- [ ] Add any-field text search.
- [ ] Add explainable placeholder, outlier, precision and consistency flags.
- [ ] Add tests for currencies, decimal precision and comparison cohorts.

## MVP completion gate

The MVP is complete when a user can authorise a test origin, inspect a paginated listing, teach CrowScout one profile structure, collect a bounded set of profiles, filter on a field absent from the site's own form, preserve multilingual source text and provenance, inspect quality flags, stop/restart safely and export records.
