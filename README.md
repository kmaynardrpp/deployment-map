# Globe Sites (CSV version)

This is a tiny static site that renders an interactive 3D globe with clickable pins,
a searchable list of sites on the left, and a popup with details for the selected site.

The globe is powered by **Globe.GL** and everything runs entirely in the browser
(perfect for static hosting such as GitHub Pages).

## Files

- `index.html` — main page with the globe and UI
- `style.css` — layout and styling
- `pins.csv` — site data (`id`, `lat`, `lng`)

## Data format (`pins.csv`)

Site locations are stored in a simple CSV file with UTF‑8 encoding:

```csv
id,lat,lng
HOU8,29.538,-95.447
MCI7,38.825,-94.909
ABE4,40.688,-75.221
```

- `id` — short site code or name
- `lat` — latitude in decimal degrees (`-90` to `90`)
- `lng` — longitude in decimal degrees (`-180` to `180`)

You can add/remove sites by editing `pins.csv` and refreshing the page.

## Company + link rules

The frontend automatically derives the **company** and **dashboard URL** for each site
based on its `id`:

- If the ID is made of English letters/digits (e.g. `ABE4`, `HOU8`, `DFW6`) it is
  treated as an **Amazon** site **by default**.
  - The link format is:

    ```text
    https://amazon{site}.iwarehouseknows-rtls.com/
    ```

    where `{site}` is the lowercase site code, e.g.:

    - `ABE4` → `https://amazonabe4.iwarehouseknows-rtls.com/`
    - `HOU8` → `https://amazonhou8.iwarehouseknows-rtls.com/`

- Two special cases are treated as **Walmart** sites with different links:

  - `GSP1` → `https://walmartGSP1.rpplabs.com`
  - `CHS1` → `https://walmartCHS1.rpplabs.com`

- IDs that contain non‑English characters (for example many of the city names in
  China and Asia) are left without a company or link; they still appear as pins and
  can be clicked, but the popup won’t show a dashboard URL.

## UI behaviour

- **Globe**
  - Drag to rotate, scroll to zoom.
  - Pins are colored by company:
    - Amazon sites — warm red
    - Walmart sites — cool blue
    - Other / unspecified — light grey
  - Pin size smoothly adjusts with zoom so markers feel more elegant up close.

- **Sidebar (left)**
  - Shows every site from `pins.csv`.
  - Includes a **search bar** that filters by site ID or company name.
  - Clicking a site will:
    - Fly the camera to that location on the globe.
    - Open the popup with details.
    - Highlight the selected site in the list.

- **Popup (bottom‑right)**
  - Appears when you click a pin or a site in the list.
  - Shows:
    - Site ID
    - Company (Amazon/Walmart/unspecified)
    - Latitude & longitude
    - A button to **open the dashboard** (if a URL exists)
  - The link opens in a **new browser tab**.

## Running locally

You can open `index.html` directly in a modern browser, but using a simple HTTP
server is recommended so that `fetch('./pins.csv')` works consistently.

From the project directory:

```bash
python3 -m http.server
# then open http://localhost:8000
```

## Deploying

Because everything is static (HTML/CSS/JS and a CSV file), deployment is as simple
as uploading these files to any static hosting provider (GitHub Pages, Netlify,
Cloudflare Pages, etc.).
