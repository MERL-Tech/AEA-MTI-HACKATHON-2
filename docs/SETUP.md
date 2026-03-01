# Setup Guide — AEA/MTI Hackathon 2

> **Who this is for:** The person who manages the GitHub repo and sets up the event backend. You need a GitHub account and a Google account.

---

## Prerequisites

- A **GitHub account** with access to this repository
- A **Google account** (for Google Sheets and Apps Script)
- A text editor (VS Code, Notepad, etc.) to edit the placeholder constants
- The **brand kit** files (already in `/assets/brand/` — confirm before proceeding)

---

## 1 — Clone the repo

```bash
git clone https://github.com/YOUR-USERNAME/aea-mti-hackathon-2.git
cd aea-mti-hackathon-2
```

---

## 2 — Confirm brand kit files

Open `/assets/brand/` and verify these files are present:

- `brand.css` — CSS custom properties (colors, fonts)
- `logo_merl-tech-4.svg` — Primary MTI logo (used in all pages)
- `AEA letters.png` — AEA co-brand logo (used in all pages)
- `AEA logo big.png` — AEA icon (optional, available for alternate use)
- `AEA.webp` — AEA logo alternate format (available)
- `brand-info.txt` — Raw brand color values (reference only)

If `brand.css` is missing, add it before proceeding — all pages depend on it.

---

## 3 — Set up the Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com) and create a new blank spreadsheet.
2. Name it: **AEA/MTI Hackathon 2 — Submissions**
3. You'll add two tabs (sheets) with the correct headers using the Apps Script `setupSheets()` function in Step 4.

---

## 4 — Deploy the Apps Script

Follow the full instructions in [`appscript/DEPLOY.md`](../appscript/DEPLOY.md). In summary:

1. Open your Google Sheet → **Extensions → Apps Script**
2. Paste `appscript/Code.gs` contents into the editor
3. Run `setupSheets()` to create the two tabs with headers
4. Deploy as a web app (**Execute as: Me / Anyone can access**)
5. Copy the web app URL

---

## 5 — Update `APPS_SCRIPT_URL` in the forms

Open both form files and replace the placeholder constant:

**`pages/form-critique-create.html`** — find in the `<script>` block:
```js
const APPS_SCRIPT_URL = '[APPS_SCRIPT_URL]';
```
Replace `[APPS_SCRIPT_URL]` with your deployed web app URL.

**`pages/form-collab.html`** — same constant, same replacement.

---

## 6 — Publish the Sheet as CSV and update `gallery.js`

1. In your Google Sheet, publish each tab as CSV (see `appscript/DEPLOY.md` Step 6 for details)
2. Open `assets/js/gallery.js` and replace:
   ```js
   const SHEET_CSV_URL_CRITIQUE_CREATE = '[SHEET_CSV_URL_CRITIQUE_CREATE]';
   const SHEET_CSV_URL_COLLAB          = '[SHEET_CSV_URL_COLLAB]';
   ```
   with the two published CSV URLs.

---

## 7 — Update remaining placeholders

Search the repo for any remaining `[PLACEHOLDER]` strings:

```bash
grep -r '\[.*_URL\]\|\[EVENT_DATES\]\|\[STAGING_URL\]' pages/ assets/
```

Replace each one:

| Placeholder | File(s) | What to put there |
|---|---|---|
| `[HOT_TAKE_URL]` | `pages/index.html` | Padlet board URL or Slack thread URL |
| `[SLACK_URL]` | All pages (footer) | Slack channel invite URL |
| `[TEMPLATES_URL]` | `pages/collab.html` | Link to starter templates folder |
| `[EVENT_DATES]` | `pages/index.html` | Real event dates, e.g., "May 5–9, 2025" |
| `[STAGING_URL]` | `README.md` | GitHub Pages URL (set after enabling Pages) |

---

## 8 — Test the full pipeline locally

Open HTML files directly in a browser (no local server needed):

1. Open `pages/index.html` — verify nav, hero, path cards, footer logos
2. Open `pages/critique.html`, `create.html`, `collab.html` — verify option cards and links
3. Open `pages/form-critique-create.html` — fill in test data and submit
   - Check Google Sheet for the new row
4. Open `pages/gallery.html` — verify it fetches and displays your test entry
5. Open `pages/form-collab.html` — submit a test Collab entry, verify in Sheet 2

> **Note:** When testing locally, you may encounter a CORS error when the gallery tries to fetch the CSV, because `file://` URLs sometimes trigger stricter security. Use a simple local server (e.g., `python3 -m http.server 8080` from the repo root) if that happens.

---

## 9 — Enable GitHub Pages (staging)

1. Go to your GitHub repo → **Settings → Pages**
2. Under "Build and deployment", set **Source** to **GitHub Actions**
3. Push any change to `main` — the `deploy.yml` workflow will run automatically
4. After it completes (1–2 min), GitHub will show your Pages URL
5. Add that URL to `README.md` as `[STAGING_URL]` and to your records

> The deploy workflow copies `/pages/*.html` to the site root and `/assets/` alongside. So `gallery.html` is served at `https://YOUR-ORG.github.io/aea-mti-hackathon-2/gallery.html`.

---

## 10 — Going live checklist

Before announcing the event, confirm:

- [ ] Both forms submit successfully (test entries visible in spreadsheet)
- [ ] Gallery loads entries and refreshes (wait 60 seconds after submit)
- [ ] All `[PLACEHOLDER]` strings replaced (`grep` check above returns nothing)
- [ ] GitHub Pages staging URL works and shows the correct content
- [ ] WordPress embed set up (see `docs/WORDPRESS-EMBED.md`)
- [ ] Slack channel URL, Hot Take Wall URL, and Templates URL all pointing to real destinations
- [ ] Event dates correct in hero section
- [ ] AEA co-brand logos visible in header and footer on all pages
- [ ] Mobile layout works (test on phone or use browser DevTools)

---

## Maintenance after launch

This system is intentionally low-maintenance:

- **New submissions:** Appear automatically in the gallery via the 60-second poll
- **Content edits:** See `docs/CONTENT.md` for how to update copy without touching HTML
- **Re-deploying changes:** Commit and push to `main` — GitHub Actions handles the rest
- **Adding new paths or options:** Update the HTML files and the path options list in both forms
