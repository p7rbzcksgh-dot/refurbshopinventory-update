# TCG Refurb Shop — Full Inventory + Part Search
Version 2.2.0 · 28 September 2026

## What is in this update

| Item | Count |
|---|---:|
| CSV files processed | 9 |
| Source rows read | 3,777 |
| Repeated part-number rows merged | 1,659 |
| Unique part numbers before exclusions | 2,118 |
| Non-production items excluded | 426 |
| Editable inventory items included | **1,692** |

The existing **31 SBX, 26 CNV and 29 CHS serial-numbered starter records** are still included. The three separate, unnumbered CHS notes — No S/N, Blank Box 3DP and Missing Box — remain editable as well. No jobs, completed work or stock consumption have been invented for those starter records.

Office furniture, office supplies, clothing, filament, printer-maintenance supplies and clearly unrelated items have been left out of the starter inventory. Actual 3D-printed machine components, assemblies, fasteners, electronic components, refurb consumables and useful tools remain. Obsolete parts and distinct revisions remain available because they can matter when refurbishing older machines.

The complete exclusion list is in `reports/excluded-inventory.csv`. The original uploaded files have not been changed. The CSV report lets you review or re-add an excluded item rather than losing its identity.

## Find and edit inventory

Open **Parts & Inventory**. Type a part number or a few words into the search box. Search also checks group, supplier, material, location and notes. Use the group/category/status filters to narrow the list. The screen displays 40 cards at a time; search covers the entire inventory, not just the current page.

Select **Edit** on a part. Change the part number, description, category, group, stock count, minimum stock, unit of measure, location, generation fit, status, weight, quantity per machine, supplier, supplier part number, revision, barcode, material, finish, URL or notes. Select **Save changes**, or **Cancel** to leave it unchanged.

Expand **Additional CSV reference fields** inside the editor for the other imported source columns, including the exported cost, WIP and MRP availability fields. These fields are editable reference values from the CSV snapshot: editing them does not post a transaction back to MRPeasy or automatically recalculate the app's working stock fields. Edit **On hand** to change the app's stock quantity.

Use the stock-count box for quick quantity corrections. Decimal quantities are supported. A blank or duplicate part number and invalid negative quantities are rejected. Renaming a part updates its linked MO, print-request and inventory-history references. A part already referenced by a job cannot be deleted directly; this avoids breaking that job's parts list. Its details remain editable.

Use **Add a part or import CSV** to add something missing. Future CSV imports skip existing part numbers rather than overwriting your edits or adding duplicate quantities. The non-production filter is on by default and can be turned off to restore an item deliberately. Importing a CSV is not an automatic stock reconciliation: edit an existing item's quantity when making a verified correction. **Export CSV** exports the full current inventory, including editable reference fields, regardless of the current display filter.

## Search for parts on a manufacturing order

Open **Refurb Jobs**, open the MO, and expand **Required parts & supply source**.

1. Type a code or description in **Search for a part** — for example `MFG0015`, `hinge` or `motor`.
2. Use **Group** to narrow the matching results. Each result shows the part number, description, group and available refurb quantity/unit.
3. Select a result, set its module and quantity, then select **Add**. Arrow keys and Enter also select results.

The list searches all retained inventory types, not only 3D-print parts. Parts already on that MO are hidden from the picker so they cannot be added twice. A no-results message explains when to clear the search or filters. The first 60 matches are displayed; narrow the search to find a specific item quickly.

The generation filter is off initially, so unspecified generation data does not hide the inventory. Generations PR1–PR7 are supported. When the generation filter is turned on, parts without a specified fit remain visible and are explicitly labelled **Fit not specified**. Verify physical compatibility before using them; a search result is not a compatibility approval.

After adding a part, keep using the existing supply-source workflow: **Refurb inventory**, **3D print request**, or **External / other**. Stock issue/return supports decimal amounts and records the actual amount moved. Importing inventory alone does not consume stock or generate print requests.

## How duplicates and quantities were handled

The nine files consist of eight article exports inside STOCKCSVS.zip plus `inventory_20260928.csv`. Repeated rows were merged by part number, ignoring case and whitespace. Quantities from overlapping exports were **not added together**. Different part numbers, zero padding and `-OBS` suffixes were not collapsed just because descriptions looked alike.

Where supplied, `inventory_20260928.csv` **Quantity** is the starting on-hand amount. Otherwise, the article export's **In stock** value is used. There were **15 stock-count differences** between the exports. All are listed in `reports/stock-conflicts.csv`; the selected quantity and source are recorded. Main article metadata is supplemented by the smaller article exports and nonblank inventory fields. Existing library weight is retained only where the CSV weight was blank.

These are stock snapshots dated **28 September 2026**, not a live connection to MRPeasy. The app's **Reserved** and **Available** amounts are calculated from refurb MOs only. They do not include reservations made outside this app. The source MRP values remain reference fields; verify physical availability before issuing parts.

## Run or update the app

The app entry files are at the ZIP root. Keep the included `reports` folder with your download. There is no enclosing app folder, build step or dependency installation.

For a local test, extract the ZIP, open a terminal in the extracted folder and run:

```sh
node server.js
```

Node.js 18 or newer is required. Open `http://localhost:3000`. The default passcode is unchanged: `566218`, unless `REFURB_PASSCODE` is set.

For the existing deployment, replace the repository's app files with the contents of this ZIP and redeploy. **Keep the existing persistent volume, DATA_DIR, REFURB_PASSCODE and AUTH_SECRET settings. Do not delete or replace the live data.json file.** Close old app tabs before updating, then reopen or refresh them. This ZIP does not itself update the live website.

### One-time import; your edits stay saved

On a fresh installation the server loads the 1,692 retained inventory items and the original editable queue starters.

On an existing installation, it first backs up the existing data file inside `DATA_DIR/.refurb-backups/`, then adds missing inventory items. Untouched records from the old built-in 34-part library are updated from the CSV. Already-edited live records keep their details and stock counts, with missing metadata and an imported quantity snapshot added for reference. Existing custom inventory and jobs are not replaced. Consequently, the live inventory total may differ from 1,692 if it contains custom records or excluded parts still referenced by jobs. Referenced excluded parts are retained with a note rather than breaking job history.

The data file stores an import marker. Renamed parts stay renamed, edited quantities stay edited and deleted parts stay deleted after refresh or restart. Editing `inventory-batch.json` after the first import does not overwrite saved records; make later corrections in the app. Old tabs that try to save without the new inventory marker are rejected with a refresh message.

Without DATA_DIR, the app saves data.json beside server.js. On a hosted service, use the same persistent DATA_DIR across redeployments. Deleting the live data file starts a new installation and reimports the starters.

The inherited app saves shared whole-state snapshots, not simultaneous per-record transactions. Avoid concurrent edits from multiple devices and refresh before important changes. Shop access uses a shared passcode, not individual user accounts.

## Reports and checks

| File | Contents |
|---|---|
| `reports/cleaned-inventory.csv` | All 1,692 retained starter items, quantities and source names |
| `reports/excluded-inventory.csv` | 426 excluded items and the reason for each |
| `reports/duplicate-parts.csv` | Repeated part numbers and their source files |
| `reports/stock-conflicts.csv` | 15 differences and the selected quantity |
| `reports/import-summary.json` | Counts, source-file coverage, policies and group totals |
| `reports/article-field-conflicts.csv` | Metadata conflict report; header only because no article-field conflicts were found |

Run **npm test** for the 28 automated tests. See **TEST_RESULTS.md** for the test scope. No live data.json, temporary browser-test records or test-only browser bridge are included in this ZIP.
