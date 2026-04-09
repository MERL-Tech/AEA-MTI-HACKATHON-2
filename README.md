# AEA/MTI Hackathon 2: Critique • Create • Collab

A fully virtual, low-barrier hackathon for evaluation practitioners exploring AI in evaluation practice. Co-hosted by the **American Evaluation Association (AEA)** and **MTI/MERL Tech**.

<!-- PLACEHOLDER: replace [STAGING_URL] with your GitHub Pages URL after enabling Pages in repo Settings -->
**Staging URL:** [https://merl-tech.github.io/AEA-MTI-HACKATHON-2/]

---

## What this is

Participants choose one of three paths (Critique, Create, or Collab), complete a 10–30 minute activity using a free AI tool, submit via a form, and appear in a live public gallery. No coding required. No prizes. Just focused exploration and community learning.

## System overview

| Surface | File | Purpose |
|---|---|---|
| Landing page | `pages/index.html` | Front door: event info, path cards, how-it-works |
| Critique path | `pages/critique.html` | Instructions + 3 option cards |
| Create path | `pages/create.html` | Instructions + 3 options + 5 Prompt Lab challenges |
| Collab path | `pages/collab.html` | Instructions + starter templates + remix activity |
| Critique/Create form | `pages/form-critique-create.html` | Dynamic submission form (path + option filtering) |
| Collab form | `pages/form-collab.html` | Remix submission with change-log |
| Gallery | `pages/gallery.html` | Live, filterable gallery — auto-refreshes every 60s |
| Apps Script | `appscript/Code.gs` | Receives form POSTs, writes to Google Sheet |

## Tech stack

- **Frontend:** Vanilla HTML + CSS + JavaScript — no frameworks, no npm, no build step
- **Backend:** Google Apps Script (deployed as web app)
- **Data:** Google Sheets (published as CSV, polled by gallery)
- **Hosting:** GitHub Pages (via GitHub Actions), embedded in WordPress via iframe
- **Brand:** Space Grotesk font, 4-color palette (coral, yellow, green, purple), co-branded AEA + MERL Tech logos

## Quick start

### 1. Clone and open

```bash
git clone https://github.com/YOUR-USERNAME/aea-mti-hackathon-2.git
cd aea-mti-hackathon-2
# Open pages/index.html in a browser to preview
```

### 2. Deploy the backend

Follow [`appscript/DEPLOY.md`](appscript/DEPLOY.md):
1. Create a Google Sheet
2. Paste `Code.gs` into Apps Script
3. Run `setupSheets()` to create tabs + headers
4. Deploy as web app → copy the URL into both form files
5. Publish sheet tabs as CSV → copy URLs into `gallery.js`

### 3. Replace placeholder values

Search for and replace all `[PLACEHOLDER]` strings:

| Placeholder | File(s) | Replace with |
|---|---|---|
| `[APPS_SCRIPT_URL]` | Both form files | Deployed Apps Script URL |
| `[SHEET_CSV_URL_CRITIQUE_CREATE]` | `assets/js/gallery.js` | Published CSV URL for Sheet 1 |
| `[SHEET_CSV_URL_COLLAB]` | `assets/js/gallery.js` | Published CSV URL for Sheet 2 |
| `[SLACK_URL]` | All page footers | Slack channel invite URL |
| `[HOT_TAKE_URL]` | `pages/index.html` | Padlet or Slack thread URL |
| `[TEMPLATES_URL]` | `pages/collab.html` | Starter templates folder URL |
| `[EVENT_DATES]` | `pages/index.html` | Real event dates |
| `[STAGING_URL]` | This README | GitHub Pages URL |

### 4. Enable GitHub Pages

Go to **Settings → Pages → Source: GitHub Actions**. Push to `main` — the site deploys automatically.

### 5. Embed in WordPress

See [`docs/WORDPRESS-EMBED.md`](docs/WORDPRESS-EMBED.md). Recommended: iframe from GitHub Pages URL.

## Repository structure

```
/
├── .github/workflows/deploy.yml    # GitHub Actions → GitHub Pages
├── pages/
│   ├── index.html                  # Landing page
│   ├── critique.html               # Critique path
│   ├── create.html                 # Create path (incl. Prompt Lab)
│   ├── collab.html                 # Collab path + templates
│   ├── form-critique-create.html   # Submission form: Critique + Create
│   ├── form-collab.html            # Submission form: Collab
│   └── gallery.html                # Live gallery
├── assets/
│   ├── brand/                      # Brand kit (brand.css, logos)
│   ├── css/styles.css              # Shared styles (imports brand.css)
│   └── js/gallery.js               # Gallery fetch + render logic
├── appscript/
│   ├── Code.gs                     # Apps Script handler
│   └── DEPLOY.md                   # Deployment instructions
├── docs/
│   ├── SETUP.md                    # Full setup guide
│   ├── WORDPRESS-EMBED.md          # WordPress embedding instructions
│   └── CONTENT.md                  # Editable content reference
└── README.md
```

## Design system

All colors and fonts come from `assets/brand/brand.css` — never hardcoded in page CSS or HTML.

| Token | Value | Used for |
|---|---|---|
| `--color-coral` | `#FC624F` | Primary accent, Critique path |
| `--color-yellow` | `#F7CF46` | Secondary accent, callouts |
| `--color-green` | `#73DC8A` | Create path |
| `--color-purple` | `#7B31F6` | Collab path |
| `--font-heading` / `--font-body` | Space Grotesk | All text |

## Documentation

- **Full setup:** [`docs/SETUP.md`](docs/SETUP.md)
- **WordPress embedding:** [`docs/WORDPRESS-EMBED.md`](docs/WORDPRESS-EMBED.md)
- **Editing content:** [`docs/CONTENT.md`](docs/CONTENT.md)
- **Apps Script deployment:** [`appscript/DEPLOY.md`](appscript/DEPLOY.md)
