# VATGLASSES-Visualiation-editor-
Made by me and Claude 



# ◈ VATSCOPE — VATSIM Airspace Viewer

A single-file, browser-based airspace visualiser for VATSIM sector JSON files. No install, no server, no dependencies to manage — just open the HTML file and load your data.

---

## Getting Started

1. Download `vatscope.html`
2. Open it in any modern browser (Chrome, Firefox, Edge)
3. Click **↑ LOAD FILE** and select one or more VATSIM sector `.json` files
4. Sectors will render on the map and the FL slider will auto-set to the lowest defined floor

Multiple files can be loaded simultaneously. Duplicate filenames replace the previous version of that file.

---

## Input File Format

VATSCOPE expects VATSIM-format JSON with an `airspace` array. A minimal example:

```json
{
  "positions": {
    "KAT": {
      "type": "TWR",
      "callsign": "DTKA_TWR",
      "frequency": "118.100",
      "colours": [{ "hex": "#f0b429" }]
    }
  },
  "airspace": [
    {
      "id": "Tabarka",
      "comment": "DTKA_TWR",
      "group": "TWR",
      "owner": ["KAT", "KAA", "TCNL", "TC"],
      "sectors": [
        {
          "min": 0,
          "max": 14,
          "points": [
            ["365727", "0090307"],
            ["365610", "0090243"]
          ]
        }
      ]
    }
  ],
  "airports": {}
}
```

Points are encoded as DMS strings: `DDMMSS` for latitude, `DDDMMSS` for longitude. Negative values are prefixed with `-`.

Supported position types: `CTR`, `APP`, `DEP`, `TWR`, `GND`, `DEL`, `FSS`.

---

## Interface Overview

```
┌─────────────────────────────────────────────────────────┐
│  ◈ VATSCOPE  [LOAD FILE]  filename.json  FL [slider] 050│  ← Topbar
│              [+ SECTOR]   [ALL ON]  [POSITIONS ▶]       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│                    MAP                                  │
│                                                         │
│         ┌──────────────────────────────┐               │
│         │  VERTEX EDIT  N pts  [COPY]  │  ← Edit bar   │
│         └──────────────────────────────┘               │
├─────────────────────────────────────────────────────────┤
│  VISIBLE: 12   POSITIONS: 8   FL: 050   51.5°N  0.1°W  │  ← Status bar
└─────────────────────────────────────────────────────────┘
```

---

## Features

### FL Slider

The **FL** slider in the top bar filters which sectors are visible based on their defined `min`/`max` flight level range. Only sectors whose range includes the current FL value are drawn. The slider minimum is automatically set to the lowest floor found across all loaded files.

### Positions Panel

Click **POSITIONS ▶** to open the ATC positions side panel. Positions are grouped by type (CTR, APP, TWR, etc.) and listed per file.

- **Toggle a position on/off** — click any row to activate it. Active positions colour their owned sectors on the map.
- **Change position colour** — click the coloured dot on any row to open a colour picker.
- **ALL ON** — toggles all positions across all files on or off at once.
- **Remove a file** — click the ✕ next to the filename in the panel header.

### Clicking a Sector

Click any rendered sector polygon to open its info popup, which shows the sector name, group badge, FL range, and owner chips. The popup provides four actions:

| Button | Action |
|---|---|
| **✦ EDIT VERTICES** | Enter vertex edit mode for this sector |
| **⎘ DUPLICATE** | Create an identical copy of the sector on the map |
| **⎘ COPY JSON** | Copy the full sector JSON to clipboard |
| **◑ COLOUR** | Override the sector's fill/stroke colour |

### Vertex Editor

Activate via **✦ EDIT VERTICES** in a sector's popup. A floating bar appears at the bottom of the screen showing the point count and controls.

**Controls:**

| Action | How |
|---|---|
| Move a vertex | Click and drag a blue dot — the sector morphs live |
| Delete a vertex | Right-click a blue dot (minimum 3 points enforced; flashes red if at limit) |
| Insert a vertex | Double-click a faint midpoint marker between two vertices |
| Copy coordinates | Click **⎘ COPY JSON** in the edit bar — outputs DMS-encoded point array |
| Exit | Click **✕ DONE** |

Changes to vertices are reflected immediately in the polygon. Coordinates are re-encoded to DMS when copied or exported.

### Copy Sector JSON

Clicking **⎘ COPY JSON** on any sector popup copies the complete region definition in VATSIM JSON format — including all FL ranges for that region, all owner and group metadata, and all points re-encoded to DMS. If you have moved vertices in the editor, the copied output reflects your edits.

Example output:

```json
{
  "id": "Tabarka",
  "comment": "DTKA_TWR",
  "group": "TWR",
  "owner": ["KAT", "KAA", "TCNL", "TC"],
  "sectors": [
    {
      "min": 0,
      "max": 14,
      "points": [
        ["365727", "0090307"],
        ["365610", "0090243"]
      ]
    }
  ]
}
```

### Duplicate Sector

**⎘ DUPLICATE** in the popup creates a full copy of the region (all FL ranges, all points) and adds it to the map immediately. The duplicate is assigned an auto-incremented ID (e.g. `Tabarka_copy`, then `Tabarka_copy2`) and inherits any colour override. You can then click it and use **✦ EDIT VERTICES** to reshape it independently.

### Create a New Sector

Click **+ SECTOR** in the top bar to open the sector builder panel on the left.

1. Fill in **ID** (required), **COMMENT**, **GROUP**, and **OWNERS** (comma-separated)
2. Pick a **COLOUR** for the preview
3. Under **FL RANGES**, set the min/max FL for each range and click **✦ DRAW POINTS** to enter drawing mode
4. Click on the map to place points. Right-click to undo the last point.
5. Add more FL ranges with **+ ADD FL RANGE** if the sector has layered airspace
6. The **JSON OUTPUT** panel updates live as you draw — click **⎘ COPY ALL** at any time
7. Click **✔ ADD TO MAP** to commit the sector

**Drawing mode controls:**

| Action | How |
|---|---|
| Place a point | Left-click on the map |
| Undo last point | Right-click, or **↩ UNDO** in the toolbar |
| Clear all points | **✕ CLEAR** in the toolbar |
| Copy current JSON | **⎘ COPY JSON** in the toolbar |
| Stop drawing | **✔ DONE** in the toolbar |

---

## Coordinate Format (DMS)

All points in the JSON format use zero-padded DMS strings:

- **Latitude:** `DDMMSS` — 6 digits, e.g. `365727` = 36°57'27"N
- **Longitude:** `DDDMMSS` — 7 digits, e.g. `0090307` = 9°03'07"E
- **Southern/Western values** are prefixed with a minus sign, e.g. `-365727`

VATSCOPE parses both 6- and 7-digit strings automatically.

---

## Technical Notes

- **Self-contained** — the entire application is a single `.html` file. No build step, no server required.
- **Dependencies loaded from CDN** — requires an internet connection to load [Leaflet 1.9.4](https://leafletjs.com/) on first open.
- **Map tiles** — served by [CartoDB Dark Matter](https://carto.com/basemaps/).
- **Multiple files** — files are layered; each has its own position list and colour assignments.
- **No data is sent anywhere** — all processing happens locally in the browser.

---

## Browser Compatibility

| Browser | Support |
|---|---|
| Chrome / Edge (Chromium) | ✅ Full |
| Firefox | ✅ Full |
| Safari | ✅ Full |
| Mobile browsers | ⚠️ Functional, but vertex drag requires touch support |