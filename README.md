# Pro Hauls — Dump / Tri-Axle Owner-Operator Map

Interactive recruit map for **Pro Hauls (Mr. Vaughn)** covering dump / haul / excavating owner-operators within ~100 miles of Nashville.

## Files

| File | Purpose |
|------|---------|
| `index.html` | Self-contained map app (Leaflet + MarkerCluster via CDN) |
| `drivers.json` | Filtered, geocoded carrier list (do not invent phones/USDOTs) |
| `notes.json` | **Shared** sales notes keyed by USDOT — team source of truth |
| `build_drivers_json.py` | Rebuilds `drivers.json` from Census/FMCSA CSV extracts |
| `uszips.csv` | ZIP centroids used for geocoding (rebuild only) |

## Quick start

1. Put this folder where the sales team can open it (local disk or synced Drive/Dropbox folder).
2. Open `index.html` in **Chrome or Edge** (best support for the File System Access API).
   - If the map loads but drivers do not (browser blocks `fetch` of local JSON), serve the folder instead:
     ```bash
     cd pro-hauls-drivers-map
     python3 -m http.server 8765
     ```
     Then open http://localhost:8765/
3. Click a pin or list row → call (`tel:`), email (`mailto:`), or open **SAFER**.
4. Enter status, tri-axle (yes/no/unknown), last contact, rep name, and free-text notes.

## Shared notes workflow (critical)

Notes are **shared for the whole sales team**, not trapped in one browser.

1. Keep a single `notes.json` in a **shared Google Drive / Dropbox / OneDrive folder**.
2. In the app, use **Open shared notes file…** and pick that `notes.json`.
3. After editing notes, click **Save notes file…** so everyone sees updates.
4. If your browser lacks File System Access:
   - **Download notes.json** after edits
   - Replace the file in the shared folder
   - Others use **Upload notes.json** (or Open) to load the latest

`localStorage` is only a **cache**. A banner in the app reminds reps that the shared `notes.json` file is the source of truth.

### Suggested team habit

- Morning: Open shared `notes.json`
- During day: update statuses while dialing
- End of session: **Save notes file…**
- Avoid two people saving conflicting copies at once — last save wins

## Filters

- Free-text search (name, owner, city, USDOT, phone, cargo)
- City
- Status: Not called / Called / Interested / Not a fit / Hired / Callback
- Tri-axle: yes / no / unknown
- Has phone

List sorts: City · Status · Miles from Nashville.

## Data rules (how `drivers.json` was built)

- Primary source: `usdot-100mi-oos.csv` (~6k rows, power units 1–3 already)
- Keyword filter (case-insensitive) on legal name, DBA, owner/officer, and enriched cargo text:
  dump, dirt, gravel, sand, rock, haul, excavate*, tri-axle / triaxle, aggregate, topsoil, asphalt, debris, demolition, fill  
  Short words use word boundaries so surnames like *Sanders* are not matched; `haul` / `dump` / `excavat` match prefixes (*hauling*, *dumping*, *excavating*).
- Deduplicated by USDOT
- Geocoded with US ZIP centroids (`uszips.csv` / SimpleMaps)
- Pins farther than **110 miles** from Nashville (36.1627, −86.7816) dropped when coords exist
- Cargo text enriched when available from `usdot-nashville-oos.csv` / `fmcsa_midtn_candidates.csv`

Rebuild:

```bash
python3 build_drivers_json.py
```

## Branding

Navy / gold Pro Hauls styling, mobile-usable list + detail panels for field reps.

## Privacy

Carrier phone/email/USDOT come from public FMCSA / Census extracts already on disk. Do not fabricate contacts. Treat notes as internal sales data.
