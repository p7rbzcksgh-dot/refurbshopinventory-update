# Local verification — Full Inventory + Part Search
Version 2.2.0 · 28 September 2026

## Source-data reconciliation

All nine supplied CSVs were read programmatically using CSV-aware parsing, including quoted/multiline cells. There were 3,777 rows and 2,118 unique normalized part numbers. Every unique source part number appears exactly once in either the retained catalogue (1,692) or the exclusions (426). There is no overlap between the two sets. The 1,659 redundant rows were merged without summing snapshot quantities.

Every retained starting on-hand amount was compared against the selected source quantity: zero differences. The 15 article-versus-inventory stock discrepancies are separately recorded. The eight article exports had no conflicting nonblank metadata values. All 34 original library part numbers remain represented. All 277 items in the 3D Print group remain, along with 187 Fasteners, 31 Insert Assemblies, 15 Small Assemblies and other retained groups.

## Automated checks

**28 of 28 tests passed** using Node.js 22.16.0 and the built-in Node test runner. No external test dependencies are required. Run `npm test` from the extracted package.

The 13 retained starter-record tests cover exact membership/order, unknown values, handwritten notes, import idempotence, duplicate detection, editable fields, open/closed MO synchronization, server authentication and persistence, backup before import and refusal to replace corrupt saved data.

The 15 new inventory tests cover:

- Source coverage, unique counts, exclusions and retained production categories.
- Snapshot quantity precedence without summing duplicates; decimal/zero quantity and metadata mapping.
- Search by code/description/metadata/previous aliases and explicit PR1–PR7 fit parsing.
- Preservation of distinct part numbers, zero padding and OBS suffixes.
- One-time import after edits, renames and deletions; refresh of untouched old seeds; preservation of live edits/counts.
- Existing-code deduplication and protection of excluded inventory still referenced by a job.
- Editing all working inventory fields and additional CSV reference fields; updating linked MO/print/history references on rename.
- Rejection of duplicate or blank codes and invalid quantities.
- Real HTTP API saves of inventory edits and fractional MO/issued quantities, restart persistence, protected data/source paths, unauthenticated access rejection and stale-client conflict responses.

The server, frontend and shared JavaScript modules also passed syntax checks.

## Browser interaction checks

Eighteen checks passed using headless Chromium and the actual app scripts/styles. The checks exercised:

1. Inventory pagination with 40 cards displayed.
2. Multiword description search.
3. Case-insensitive code search with optional punctuation.
4. Saving part-code/name changes, decimal stock, location, notes, revision, barcode and PR7 through the full editor.
5. Cancel without saving.
6. Visible duplicate-code rejection.
7. Creating a refurb MO for a PR7 starter without removing queue records.
8. Search, selection and addition of a fractional-quantity MO part.
9. Decimal stock issue with correct remaining count and issued amount.
10. Exact stock return.
11. Enter-key result selection.
12. Hiding already-added parts to prevent duplicate MO lines.
13. Group-filtered part search.
14. No-results guidance.
15. A 390-pixel-wide MO picker without page-level horizontal overflow.
16. A 390-pixel-wide inventory card without page-level horizontal overflow.
17. A 320-pixel-wide inventory layout without page-level horizontal overflow.
18. No JavaScript page errors during these tested interactions.

Desktop and mobile screenshots were visually inspected. These tests used isolated temporary data, not the packaged catalogue or the user's live data. Renamed test parts and temporary test jobs are not included in the ZIP.

The container browser blocks URL navigation. The actual scripts and styles were therefore loaded into a local test page, and fetch calls were routed through a test-only bridge to the real local Node server. The bridge and test-page wrapper are not included in the app. Real HTTP route behavior was independently exercised by the automated tests. This is not a claim that the deployed website was browser-tested.

## Not covered

This package was not pushed to GitHub or deployed to Railway. No live service or live data was accessed or changed. A physical iPhone/Safari session, every possible CSV format, the entire printing/teardown/testing workflow, external Odoo imports and simultaneous multi-user editing were not tested in this update. The inherited whole-state save/concurrency limitation remains. These tests are regression checks, not a security audit or production-load certification.

The ZIP contains no live data.json, migration backup, temporary test-state file or test-only browser adapter. First-start data is created by the normal one-time import when the application runs.
