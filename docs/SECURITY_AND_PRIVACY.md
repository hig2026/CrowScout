# CrowScout Security and Privacy

## Security goals

CrowScout must be transparent, user-authorised and least-privilege. The extension should operate only on the website origin the user explicitly selected and only within the scope disclosed before scanning.

## Permission design

- Use Manifest V3 optional host permissions for site access.
- Request a specific origin after the user supplies a URL and presses an action such as Inspect or Start.
- Validate scheme and origin; reject browser-internal URLs and unsupported schemes.
- Use activeTab where temporary, user-gesture-based access is sufficient.
- Do not request <all_urls> as a default.
- Display the granted origin and provide a way to revoke access.

## Data minimisation

- Collect only fields selected by the user or necessary to operate the scan.
- Do not collect passwords, authentication tokens, cookies, hidden form secrets or unrelated account data.
- Prefer visible, rendered profile information.
- Store source URLs and collection timestamps for provenance.
- Provide per-record and whole-scan deletion.
- Provide clear export controls.

## Local-first model

- Store profile data in IndexedDB.
- Keep preferences in chrome.storage.local.
- Do not upload profile contents by default.
- Any optional translation/AI provider must be separately enabled, with a clear disclosure of what text leaves the browser and to which provider.
- Do not send whole pages when only a selected text segment is required.
- Avoid logging profile contents or personal data to the developer console.

## Translation privacy

Before external translation:
1. Tell the user that selected profile text will leave the browser.
2. Identify the provider and target language.
3. Allow translation to be disabled.
4. Keep original text unchanged.
5. Store translation provenance and allow deletion.

Prefer site-native translation or local translation where suitable.

## Safe crawling

- Respect access restrictions, site terms and reasonable request pacing.
- Do not bypass CAPTCHA, paywalls, login restrictions, anti-bot systems or access controls.
- Stop on explicit denial, challenge pages, rate-limit responses or unexpected authentication.
- No proxy rotation, stealth fingerprints or evasion.
- Use bounded page and profile limits.
- Show progress and provide pause/stop controls.

## Extension code security

- Manifest V3.
- No remotely hosted executable scripts.
- Avoid eval and unsafe dynamic code generation.
- Treat website content as untrusted input.
- Render extracted text as text, not HTML.
- Sanitize any exported spreadsheet content to mitigate formula injection.
- Restrict message passing between content scripts, service worker and side panel; validate message shapes and sender origin.
- Keep secrets out of source control. If a translation provider needs an API key, explain that a key stored in an extension is not equivalent to server-side secret storage.

## Data integrity

- Preserve raw source values.
- Record normalisation separately.
- Explain anomaly flags and never label someone fraudulent solely based on an unusual value.
- Store field-level confidence and provenance where possible.
