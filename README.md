# TCG Refurb Shop

**Version 2.2.0 — full editable inventory and searchable MO parts, 28 September 2026.**

Start with **START_HERE.md** for inventory editing, part-search instructions, the CSV merge rules and the safe update procedure.

This package retains the existing TCG Refurb Shop workflow and branding. It adds a cleaned, editable inventory of **1,692 unique items** from nine supplied CSVs and a type-to-search part picker in manufacturing orders. The previous 86 serialized queue starters and three unnumbered CHS notes are preserved. This is a complete application package, not a separate import file or a replacement interface.

## Run locally

Node.js 18 or newer is required. No dependencies, build step or public UI CDN are needed.

```sh
npm start
# or: node server.js
```

Open `http://localhost:3000`. The default shared shop passcode remains `566218` unless overridden by REFURB_PASSCODE. The gate protects the interface and data API. It is not a personal user-account system.

## Environment settings

| Variable | Default | Purpose |
|---|---|---|
| `PORT` | `3000` | HTTP listening port |
| `DATA_DIR` | App directory | Persistent folder holding data.json and migration backups |
| `REFURB_PASSCODE` | Included default | Shared shop passcode |
| `AUTH_SECRET` | Derived fallback | Authentication-cookie signing secret |

Keep the existing values when updating a deployed service. Use a private shop passcode and a long random AUTH_SECRET. Use HTTPS and a persistent data volume for a shared hosted installation.

Copy the application entry files to the repository root. Start with `npm start`. Keep DATA_DIR pointing to the existing persistent-volume mount path. Do not overwrite live data.json with a starter or test file; none is included in this package. Close old browser tabs before redeployment and refresh afterwards. Preparing this ZIP does not push it to GitHub or deploy it to Railway.

## Existing workflow retained

Receive a full machine or individual SBX/CNV/CHS unit into the Queue, verify its generation and create its refurb MO. Record module inspection and upgrade requirements, add required parts, and choose stock, print request or external supply. MO-specific Refurb Print Requests retain their revision and status workflow. Continue through teardown, cleaning, installation, pre-test, regular testing and shipping.

The app also retains intake/parts/print CSV exports, the existing Odoo repair-order export, stock issue/return history, session undo/redo, and language selection. External Odoo import mappings and live printing integrations were not validated as part of this inventory update. Advanced new interface strings may use the existing English fallback in another selected language.

## New inventory behavior

- All retained categories are searchable and visible by default, including actual 3D-print components, hardware, assemblies, manufactured and off-the-shelf parts, tools and consumables.
- Search inventory or MO parts by code, description, group, supplier, material, location or notes. MO results support group filters and keyboard selection.
- Inventory cards use 40-item pagination without limiting the search scope. MO search displays up to 60 matches and invites a narrower search when needed.
- Edit and save all working inventory fields, including stock counts and part codes. Extra CSV fields remain editable source references, not live MRP calculations.
- PR1 through PR7 are supported. Unspecified fit is labelled and never presented as verified compatibility.
- Fractional stock and MO quantities are retained. Duplicate part numbers are rejected. Renaming keeps part links consistent; deletion of a referenced part is protected.
- Additional CSV imports skip existing codes rather than overwriting counts or edits. Export produces the full current inventory with reference fields.

## Inventory merge and migration

The nine input files contain 3,777 rows and 2,118 unique part numbers. The build merged 1,659 repeated rows and excluded 426 non-production items, leaving 1,692 editable starter items. Actual printed machine parts and legacy/obsolete revisions remain. The reports folder records both included and excluded items.

The supplied inventory CSV takes precedence for starting on-hand counts when it has a quantity; article In stock is the fallback. Overlapping snapshots are never added together. Fifteen quantity differences are recorded in the stock-conflict report. MRP reserved/WIP/available/cost exports remain source reference fields; the application is not automatically synchronized with MRPeasy.

A one-time migration backs up existing saved state, adds missing inventory and refreshes untouched old library seeds. Existing live edits, stock movements, custom parts, machines, MOs and counters are preserved. Excluded records still referenced by jobs are kept and flagged. The saved import marker prevents deleted or renamed seed items from returning after a restart. Older tabs without that marker are blocked from saving stale data over the new inventory.

Data is saved atomically in a JSON file. This remains a shared whole-state system, not a relational database or concurrent per-record editor. Separate devices can still overwrite each other's whole-state changes; avoid simultaneous editing and refresh before important updates. Keep persistent storage and regular backups.

## Included files

| File | Purpose |
|---|---|
| `server.js` | Dependency-free Node server, authentication, JSON API and migration |
| `index.html`, `app.js`, `styles.css` | Application shell, workflow UI and responsive styling |
| `inventory-utils.js` | Inventory normalization, search, edit validation and one-time import |
| `inventory-batch.json` | Private server-side starter catalogue and exclusion references |
| `seed-parts.json` | Original 34-part baseline for recognizing untouched legacy records; not a reset button |
| `starter-units.json` | Original 86 serials and three unnumbered CHS notes |
| `starter-records.js` | Starter import, serial matching/sorting and Queue edits |
| `i18n.js` | Interface strings and language fallback |
| `vendor-preact-htm.js` | Local Preact/HTM browser runtime |
| `logo.png` | Existing Refurb Shop branding |
| `inventory.test.js`, `starter-records.test.js` | 28 automated regression tests |
| `reports/` | Cleaned/excluded inventories, duplicates, quantity differences and import summary |
| `START_HERE.md` | Editing, MO part search and safe deployment/update steps |
| `TEST_RESULTS.md` | Verification results and limits |
| `THIRD-PARTY-NOTICES.md` | Runtime attribution and licences |

The server only exposes an explicit list of browser assets. Data, seed JSON, reports, backups and server source are not served as public static files. Reports remain accessible in the downloaded ZIP.

## Tests

```sh
npm test
```

All 28 automated tests passed locally on Node.js 22.16.0. Browser interaction checks exercised the actual scripts/styles with a test-only adapter to a local Node API; see TEST_RESULTS.md for the exact scope. Tests did not access the live service. Test data, screenshots and browser adapters are not part of the runtime package.
