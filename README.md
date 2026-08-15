# Setlist Builder

A single-page setlist planner for working bands. Reads a song library from a
Google Sheet, lets you drag songs into sets, tracks the running time of each
set, and prints each set as one full 8.5x11 page.

No build step, no dependencies. One HTML file.

## What it does

- Drag songs from the library into a set, or tap to add on a phone
- Live per-set time, including a configurable gap per song for banter and tuning
- Warns when a set will not fit on a single printed page
- Blocks the same song being added twice in one night
- Prints or saves a PDF with the type auto-sized to fill each page
- Saves setlists back to the same spreadsheet, so the whole band can see them

## Setup

### 1. Song library

Any tab in a Google Sheet. Row 1 must be headers.

| Column | Required | Notes |
| --- | --- | --- |
| `Title` | yes | |
| `Length` | yes | `4:12`, `4` (minutes), `252` (seconds), or a duration cell |
| `Notes` | no | Shown under the title on the printed page |
| `Artist` | no | Search only, not printed |
| `Band` | no | Adds a band filter, so one sheet can serve several projects |

Share the sheet as **Anyone with the link -> Viewer**.

### 2. Setlist storage (optional)

`Code.gs` goes in the same spreadsheet under **Extensions -> Apps Script**.
Deploy it as a **Web app**, executing as you, accessible to **Anyone**, then put
the `/exec` url in `CONFIG.API`.

It writes to a tab called `Setlists`, one row per song, so past setlists stay
readable and filterable in the sheet itself. Your library tab is never written
to. The script creates the tab and its header row on first use.

Because the deployment is open to anyone holding the url, `TOKEN` in `Code.gs`
must match the `?token=` on the end of `CONFIG.API`. Change one and you must
change the other.

### 3. Configure

Edit the `CONFIG` block at the top of the `<script>` in `index.html`:

```js
const CONFIG = {
  BAND:       "Your Band",
  SHEET_URL:  "https://docs.google.com/spreadsheets/d/.../edit",
  TAB:        "Master",
  API:        "https://script.google.com/macros/s/.../exec?token=...",
  SETS:       3,
  TARGET_MIN: 45,
  GAP_SEC:    25
};
```

Anything left blank keeps its on-screen control instead.

## More than one band

Copy the file and give each copy a different `BAND`. That value also namespaces
saved work in the browser, so two copies sharing a `BAND` would overwrite each
other.

## Printing

Set margins to **None** and scale to **100%**, and turn off headers and footers.
Each set prints as its own page with the type scaled to fill it, so a 12 song
set prints large and a 20 song set prints smaller. "Save as PDF" in the same
dialog is how you get it onto a phone.

## Notes

- Setlists reference songs by title, so renaming a song in the library flags it
  in older setlists as `Not in sheet` rather than silently dropping it
- Songs are matched by title, not row, so re-sorting the sheet is safe
- Editing `Code.gs` is not enough to change what is live: redeploy with
  **Deploy -> Manage deployments -> edit -> New version**
