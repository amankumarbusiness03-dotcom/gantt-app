# Phase 1 RASIC Gantt Chart — Deployment Guide

A live Gantt chart that reads directly from your Google Sheet.
When your team updates the sheet, the chart refreshes automatically every 5 minutes.

---

## Step 1 — Set up your Google Sheet

1. Open your RASIC Tracker in **Google Sheets**
   (File → Import if you're starting from the Excel file)

2. Make sure the sheet has these column headers (exact names matter):

   | Column | Required |
   |--------|----------|
   | Phase | ✓ |
   | Function | ✓ |
   | Activity | ✓ |
   | Start Date | ✓ |
   | End Date | ✓ |
   | Responsible (R - Name) | ✓ |
   | Status | ✓ |

3. **Publish the sheet as CSV:**
   - Go to **File → Share → Publish to web**
   - Under "Link", select your sheet tab name (e.g. "Phase 1 - RASIC Tracker")
   - Change format from "Web page" to **"Comma-separated values (.csv)"**
   - Click **Publish** → copy the URL

   The URL will look like:
   ```
   https://docs.google.com/spreadsheets/d/XXXX/pub?gid=0&single=true&output=csv
   ```

---

## Step 2 — Add your Sheet URL to the app

Open `index.html` and find this line near the top of the `<script>`:

```js
const SHEET_CSV_URL = window.SHEET_CSV_URL || 'YOUR_GOOGLE_SHEET_CSV_URL_HERE';
```

Replace `YOUR_GOOGLE_SHEET_CSV_URL_HERE` with your published CSV URL:

```js
const SHEET_CSV_URL = window.SHEET_CSV_URL || 'https://docs.google.com/spreadsheets/d/XXXX/pub?gid=0&single=true&output=csv';
```

Save the file.

---

## Step 3 — Deploy to Vercel (free, takes 2 minutes)

### Option A — Drag & Drop (no account needed initially)

1. Go to **https://vercel.com**
2. Sign up free (GitHub login recommended)
3. Click **"Add New Project"**
4. Drag your `gantt-app` folder onto the page
5. Click **Deploy** — you'll get a URL like `https://your-project.vercel.app`

### Option B — GitHub + Vercel (best for ongoing updates)

1. Push this folder to a GitHub repo:
   ```bash
   cd gantt-app
   git init
   git add .
   git commit -m "initial gantt chart"
   gh repo create gantt-app --public --push
   ```
2. Go to **https://vercel.com → New Project → Import Git Repository**
3. Select your repo → Deploy
4. From now on: `git push` → Vercel auto-redeploys in ~30 seconds

### Option C — Netlify Drop (simplest)

1. Go to **https://app.netlify.com/drop**
2. Drag your `gantt-app` folder onto the page
3. Done — you get a live URL instantly

---

## Step 4 — Set your Sheet URL via environment variable (optional, more secure)

Instead of hardcoding the URL in `index.html`, you can set it in Vercel:

1. In Vercel dashboard → your project → **Settings → Environment Variables**
2. Add: `VITE_SHEET_URL` = your CSV URL

Then in `index.html`, update the config line to read from a meta tag injected at build time,
or simply keep it hardcoded — the Google Sheet CSV URL is not a secret.

---

## How live updates work

- The chart auto-refreshes **every 5 minutes** by polling your published Google Sheet
- Your team edits the Google Sheet → changes appear in the chart within 5 minutes
- Users can also click the **↻ refresh** button to force an immediate reload
- No backend, no server, no database required

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| "could not load sheet" error | Make sure you published as CSV (not Web page) in Google Sheets |
| Chart shows demo data | Your Sheet URL hasn't been added yet — see Step 2 |
| CORS error in console | The sheet must be published publicly (File → Publish to web) |
| Dates not showing | Ensure Start Date / End Date cells are formatted as dates in Google Sheets |
| Missing tasks | Check that Activity, Start Date, End Date columns are all filled |

---

## Customizing

- **Colors**: Edit the CSS variables at the top of `index.html` (`:root { ... }`)
- **Auto-refresh interval**: Change `5 * 60 * 1000` (milliseconds) in the script
- **Today's date**: The red "today" line uses `new Date('2026-04-13')` — change to `new Date()` for real-time today
- **Add more filters**: Copy the `ownerFilter` pattern in the HTML/JS

---

## File structure

```
gantt-app/
├── index.html    ← the entire app (single file, no build step needed)
└── README.md     ← this file
```
