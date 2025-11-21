# Globe Sites (CSV + URL field)

This project renders an interactive 3D globe with clickable pins,
a searchable list of sites on the left, and a popup with details for the
selected site. Everything runs entirely in the browser.

## Files

- `index.html` — main page with the globe and UI
- `style.css` — layout and styling
- `pins.csv` — site data (`id`, `lat`, `lng`, `url`)

## Data format (`pins.csv`)

The site locations are stored in a CSV file with UTF‑8 encoding:

```csv
id,lat,lng,url
HOU8,29.538,-95.447,https://amazonhou8.iwarehouseknows-rtls.com/
福州,26.0745,119.2965,
GSP1,34.8526,-82.394,https://walmartGSP1.rpplabs.com
```

- `id`  — short site code or name
- `lat` — latitude in decimal degrees (`-90` to `90`)
- `lng` — longitude in decimal degrees (`-180` to `180`)
- `url` — optional dashboard URL for that site (may be blank)

The frontend will **use the URL from the CSV when present**. If it is
blank, it will fall back to deriving a URL for Amazon / Walmart sites
using the rules below.

## Company + link rules

The frontend derives the **company** and (when needed) the **dashboard URL**
from the `id`:

- If `id` matches an Amazon-style code (`2‑4` capital letters + `1‑2` digits),
  e.g. `ABE4`, `HOU8`, `DFW6`, then it is treated as an **Amazon** site
  (except for the explicit Walmart overrides listed below).

  - If `url` in the CSV is blank, it will be generated as:

    ```text
    https://amazon{site}.iwarehouseknows-rtls.com/
    ```

    where `{site}` is the lowercase site code, e.g.:

    - `ABE4` → `https://amazonabe4.iwarehouseknows-rtls.com/`
    - `HOU8` → `https://amazonhou8.iwarehouseknows-rtls.com/`

- Two IDs are treated as **Walmart** sites with specific links:

  - `GSP1` → `https://walmartGSP1.rpplabs.com`
  - `CHS1` → `https://walmartCHS1.rpplabs.com`

  These URLs are already written into `pins.csv` and will be used directly.

- IDs that do **not** match the Amazon pattern (for example the city names
  in Chinese) are left without a company and without a URL unless you add
  one explicitly in the CSV. They still appear as pins and can be clicked,
  but the popup won’t show a dashboard button.

## UI behaviour

- **Globe**
  - Drag to rotate, scroll to zoom.
  - Pins are colored by company:
    - Amazon sites — red
    - Walmart sites — blue
    - Other / unspecified — amber (no pins are rendered in white)
  - Pin size is tuned so that:
    - When you are zoomed **out**, they stay large enough to click easily.
    - When you zoom **in**, they shrink a bit so they look more elegant.

- **Sidebar (left)**
  - Lists every site from `pins.csv`.
  - Has a **search bar** that filters by site ID or company name.
  - Clicking a site:
    - Flies the camera to that location on the globe.
    - Highlights the site in the list.
    - Opens the popup with details.

- **Popup (bottom‑right)**
  - Opens when you click a pin or a site in the list.
  - Shows:
    - Site ID
    - Company (Amazon/Walmart/unspecified)
    - Latitude & longitude
    - A button to **open the dashboard** (if a URL exists)
  - The link opens in a **new browser tab**.

## Running locally

Use a simple HTTP server so that `fetch('./pins.csv')` works reliably.

From the project directory:

```bash
python3 -m http.server
# open http://localhost:8000
```

## Deploying

Because everything is static (HTML/CSS/JS and a CSV file), deployment is as
simple as uploading these files to any static hosting provider (GitHub
Pages, Netlify, Cloudflare Pages, etc.).
