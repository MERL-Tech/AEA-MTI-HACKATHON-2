# AEA/MTI Hackathon 2 — Technical Specification
**Critique • Create • Collab | Virtual Edition**

> This document is the single source of truth for Claude Code.
> Read it fully before writing any code or creating any files.

---

## 1. Project Overview

A fully virtual, low-barrier hackathon for evaluation practitioners exploring AI in evaluation practice. Participants choose one of three engagement paths (Critique, Create, Collab), complete a 10–30 minute activity, submit via form, and appear in a live public gallery.

**The system has four user-facing surfaces:**

| Surface | Purpose |
|---|---|
| Landing page | Front door; explains the event, presents the three path cards |
| Path instruction pages (×3) | Deep-dive per path: instructions, option cards, link to submission form |
| Submission forms (×2) | Collect entries; post to Google Sheets via Apps Script |
| Gallery page | Live, filterable display of all submissions |

---

## 2. Technical Architecture

### Stack
- **Frontend:** Vanilla HTML + CSS + JavaScript (no frameworks, no build tools)
- **Backend / data pipe:** Google Apps Script (deployed as web app) receives POST from forms, writes to Google Sheets
- **Data source for gallery:** Published Google Sheet (CSV endpoint), polled every 60 seconds via JS fetch
- **Hosting:** Pages are embedded into MTI's WordPress site as HTML blocks or custom page templates. GitHub Pages serves as a staging/preview environment via GitHub Actions

### Data Flow
```
User fills form (HTML page)
  → JS fetches POST to Apps Script Web App URL
    → Apps Script appends row to Google Sheet
      → Sheet is published as CSV (public read)
        → Gallery page fetches CSV on load + every 60s
          → JS renders submission cards
```

### No dependencies on paid tools, WordPress plugins, or proprietary APIs.

---

## 3. Repository Structure

```
/
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions → GitHub Pages staging deploy
├── pages/
│   ├── index.html              # Landing page
│   ├── critique.html           # Critique path instructions + option cards
│   ├── create.html             # Create path instructions + option cards
│   ├── collab.html             # Collab path instructions + option cards
│   ├── form-critique-create.html   # Submission form: Critique + Create paths
│   ├── form-collab.html            # Submission form: Collab path
│   └── gallery.html            # Live gallery
├── assets/
│   ├── brand/                  # PLACEHOLDER — brand kit files go here before prompting
│   │   ├── brand.css           # CSS custom properties: colors, fonts, spacing
│   │   └── logo.svg            # MTI logo
│   ├── css/
│   │   └── styles.css          # Shared styles (imports brand.css)
│   └── js/
│       └── gallery.js          # Gallery fetch + render logic (standalone module)
├── appscript/
│   ├── Code.gs                 # Apps Script: handles POST, writes to Sheet, CORS headers
│   └── DEPLOY.md               # Step-by-step instructions for deploying the Apps Script
├── docs/
│   ├── SETUP.md                # Full setup guide: repo → Apps Script → Sheets → WordPress embed
│   ├── WORDPRESS-EMBED.md      # How to embed each HTML page into WordPress
│   └── CONTENT.md              # Editable content: path descriptions, prompt lab challenges, etc.
└── README.md                   # Project overview and quick-start
```

---

## 4. Page Specifications

### 4.1 Landing Page (`pages/index.html`)

**Purpose:** Single entry point. Orients visitors, presents the three path choices.

**Sections (top to bottom):**
1. **Hero** — Event name, tagline ("Critique • Create • Collab"), one-sentence purpose statement, event dates (placeholder: `[EVENT DATES]`)
2. **What is this?** — 2–3 sentence plain-language summary of the hackathon
3. **How it works** — 5-step visual flow: Visit → Pick → Work → Submit → Gallery (matches concept note)
4. **Choose your path** — Three cards side by side (or stacked on mobile), one per path. Each card has: path name, icon, one-line tagline, 3-bullet option list, and a CTA button linking to the path instruction page
5. **Hot Take Wall** — Brief callout block with a link to the Padlet/Slack thread (URL placeholder: `[HOT_TAKE_URL]`)
6. **Footer** — MTI logo, event info, link to gallery, link to Slack channel (URL placeholder: `[SLACK_URL]`)

**Path card colors (use brand accent colors per path):**
- Critique: red accent (matches deck)
- Create: green accent (matches deck)
- Collab: purple accent (matches deck)

---

### 4.2 Path Instruction Pages (`pages/critique.html`, `create.html`, `collab.html`)

**Purpose:** Give participants everything they need to do their chosen activity and submit.

**Shared structure for all three:**
1. **Path header** — Path name, icon, tagline, estimated time (10–30 min)
2. **Overview** — 2–3 sentences on what this path involves
3. **Choose your option** — Three option cards (one per activity within the path). Each card contains:
   - Option name
   - Full plain-language instructions (step-by-step)
   - What to include in your submission
   - Estimated time
4. **Submission checklist** — Reminder of what every submission needs (one-line need/claim, evidence of work, ethics check, 140-char reflection)
5. **Submit button** — Links to the relevant submission form
6. **Back to all paths** — Link back to landing page

**Path-specific option content:**

**Critique path options:**
- Red-Team a Claim
- AEA Competencies Remix
- Case Snap: Failure or Success

**Create path options:**
- Prompt Lab (include all 5 placeholder challenges — see Section 7)
- Custom Chatbot: Build a Tiny Helper
- Vibe-Coding Studio

**Collab path options:**
- Single option: Craft a Remix (start from shared template, improve, publish, submit change-log)
- Include a prominent "Browse starter templates" link (placeholder: `[TEMPLATES_URL]`)

---

### 4.3 Submission Forms

#### Form A: Critique + Create (`pages/form-critique-create.html`)

**Fields:**

| Field | Type | Required | Notes |
|---|---|---|---|
| Your name | Text | Yes | |
| Email address | Email | Yes | Not displayed publicly |
| Path | Select | Yes | Options: Critique, Create |
| Option chosen | Select | Yes | Dynamically filtered by path selection |
| One-line need or claim | Text | Yes | Max 140 characters |
| Evidence of work | Textarea | Yes | Label changes based on path/option selected |
| Link (if applicable) | URL | No | For Create path outputs |
| Ethics check | Checkbox | Yes | "I confirm this submission contains no PII or real client data" |
| 140-character reflection | Textarea | Yes | Counter shown; hard limit enforced |

**Behavior:**
- "Option chosen" dropdown dynamically updates options when "Path" changes (JS)
- "Evidence of work" label dynamically updates to match selected option (JS)
- Character counter on reflection field (JS)
- On submit: JS collects fields, POSTs JSON to Apps Script URL (stored as a JS constant: `APPS_SCRIPT_URL`)
- Show loading state on submit button
- On success: show thank-you message with link to gallery
- On error: show friendly error message, do not clear form

#### Form B: Collab (`pages/form-collab.html`)

**Fields:**

| Field | Type | Required | Notes |
|---|---|---|---|
| Your name | Text | Yes | |
| Email address | Email | Yes | Not displayed publicly |
| One-line need or claim | Text | Yes | Max 140 characters |
| Based-on template link | URL | Yes | The template they started from |
| What you changed (change-log) | Textarea | Yes | 1–3 bullets describing changes and why |
| Your remix link | URL | Yes | Published remix |
| Ethics check | Checkbox | Yes | Same as Form A |
| 140-character reflection | Textarea | Yes | Same counter behavior as Form A |

---

### 4.4 Gallery Page (`pages/gallery.html`)

**Purpose:** Live, filterable display of all submissions. Updates every 60 seconds without page reload.

**Layout:**
1. **Header** — "Open Gallery" title, submission count, last-updated timestamp
2. **Filter bar** — Filter buttons: All | Critique | Create | Collab. Plus sub-filter for option (e.g., "Red-Team", "Prompt Lab", etc.) — secondary filter row, shown as chips
3. **Card grid** — Responsive grid (3 col desktop, 2 col tablet, 1 col mobile)
4. **Footer** — Link to submit your own entry

**Submission card anatomy:**

All paths:
- Path badge (color-coded: Critique=red, Create=green, Collab=purple)
- Option label
- Submitter name
- One-line need/claim (bold)
- Reflection (140 chars, italicized)
- Submission timestamp

Create path additions:
- "View output →" link button (if link provided)

Collab path differences:
- Replace reflection area with: "Based on: [link]" + change-log bullets (1–3) + "Open Remix →" button
- Show version lineage tag if available

**Gallery JS behavior (`assets/js/gallery.js`):**
- On load: fetch published Sheet CSV, parse, render all cards
- Poll every 60 seconds, re-render only changed/new cards (diff by timestamp or row count)
- Filter buttons update visible cards without re-fetching
- Show "Loading…" skeleton cards on initial load
- Show "No submissions yet — be the first!" if sheet is empty
- Sheet CSV URL stored as a constant: `SHEET_CSV_URL`

---

## 5. Google Apps Script (`appscript/Code.gs`)

**Handles:** POST requests from both submission forms.

**Sheet structure — Sheet 1 (Critique/Create submissions):**

| Column | Field |
|---|---|
| A | Timestamp |
| B | Name |
| C | Path |
| D | Option |
| E | One-line need/claim |
| F | Evidence of work |
| G | Link |
| H | Ethics check confirmed |
| I | Reflection |

**Sheet structure — Sheet 2 (Collab submissions):**

| Column | Field |
|---|---|
| A | Timestamp |
| B | Name |
| C | One-line need/claim |
| D | Based-on link |
| E | Change-log |
| F | Remix link |
| G | Ethics check confirmed |
| H | Reflection |

**Script behavior:**
- Accept POST with JSON body
- Detect which sheet to write to based on a `formType` field in the JSON (`"critique-create"` or `"collab"`)
- Append row with server-side timestamp
- Return JSON response: `{ "status": "success" }` or `{ "status": "error", "message": "..." }`
- Set CORS headers to allow requests from any origin (required for HTML form → Apps Script)
- Include a `doGet()` function that returns a simple status message (useful for testing deployment)

**`appscript/DEPLOY.md` must include:**
1. How to open Apps Script in Google Drive
2. How to paste `Code.gs`
3. How to deploy as a web app (Execute as: Me, Who has access: Anyone)
4. How to copy the deployment URL into the HTML forms (`APPS_SCRIPT_URL` constant)
5. How to publish the Sheet as CSV and copy the URL into the gallery (`SHEET_CSV_URL` constant)

---

## 6. GitHub Actions (`/.github/workflows/deploy.yml`)

**Purpose:** On every push to `main`, deploy the contents of `/pages` and `/assets` to GitHub Pages for staging preview.

**Behavior:**
- Trigger: push to `main`
- Deploy source: root of repo (Pages serves from `/pages` as root, or use a flat copy step)
- The staging URL should be noted in `README.md` as `[STAGING_URL]`
- No build step required — pure static files

---

## 7. Placeholder Content

Claude Code must generate placeholder content for the following. All placeholders should be clearly marked with `[PLACEHOLDER]` comments in the HTML so they are easy to find and replace.

### 7.1 Five Prompt Lab Challenges

Generate five short, evaluation-relevant prompt engineering challenges. Each should:
- Have a name (e.g., "Challenge 1: The Plain-Language Translator")
- Have a 2–3 sentence description of the challenge
- Specify the intent (what a good prompt should accomplish)
- Be completable in 10–15 minutes with a free AI tool

Suggested challenge themes (draft these out fully):
1. Plain-language translation of an evaluation finding for a non-technical audience
2. Generating a stakeholder interview question set from a theory of change
3. Summarizing a long qualitative dataset excerpt without losing nuance
4. Red-teaming an AI-generated evaluation recommendation
5. Drafting an ethical use statement for an AI-assisted evaluation

### 7.2 Collab Starter Templates

Create three placeholder Claude Artifact template descriptions (these will later be replaced with real artifact links):
1. A chatbot system-prompt template for a survey QA helper
2. A red-team checklist template
3. An automation note template for basic data cleaning

### 7.3 URL Placeholders

The following strings should appear as constants or clearly marked placeholders throughout the code:

| Placeholder | Where used |
|---|---|
| `[APPS_SCRIPT_URL]` | Both submission forms (JS constant) |
| `[SHEET_CSV_URL]` | Gallery page (JS constant) |
| `[SLACK_URL]` | Landing page footer, path pages |
| `[HOT_TAKE_URL]` | Landing page Hot Take Wall section |
| `[TEMPLATES_URL]` | Collab path page |
| `[EVENT_DATES]` | Landing page hero |
| `[STAGING_URL]` | README |

---

## 8. Brand & Visual Design

### 8.1 Brand Kit

**The brand kit will be added to `/assets/brand/` before Claude Code is run.** It will include:
- `brand.css` — CSS custom properties for colors, fonts, spacing
- `logo.svg` — MTI logo

**All pages must import `brand.css` first, then `styles.css`.** Do not hardcode any color or font values directly in HTML or page-level CSS — use CSS custom properties from `brand.css` exclusively.

### 8.2 Fallback (if brand kit not yet present)

If `brand.css` is not yet in the repo, Claude Code should generate a placeholder `brand.css` using the color palette visible in the stakeholder deck:

```css
:root {
  --color-navy:   #1B2A4A;   /* primary dark */
  --color-teal:   #1A8FA0;   /* primary accent */
  --color-gold:   #E8A020;   /* secondary accent */
  --color-white:  #FFFFFF;
  --color-light:  #F5F7FA;   /* page background */
  --color-text:   #2C2C2C;
  --color-border: #DDEAF0;

  /* Path accent colors */
  --color-critique: #C0392B;
  --color-create:   #27AE60;
  --color-collab:   #7D3C98;

  --font-heading: 'Merriweather', Georgia, serif;
  --font-body:    'Open Sans', Arial, sans-serif;

  --radius:  8px;
  --shadow:  0 2px 8px rgba(0,0,0,0.08);
  --max-width: 1100px;
}
```

### 8.3 Responsive Design

All pages must be fully responsive:
- Mobile-first CSS
- Breakpoints: 480px (mobile), 768px (tablet), 1024px (desktop)
- Path cards: 1 col mobile → 3 col desktop
- Gallery cards: 1 col mobile → 2 col tablet → 3 col desktop
- Navigation/header: hamburger menu on mobile

### 8.4 Accessibility

- All images have `alt` attributes
- Color contrast meets WCAG AA minimum
- Form fields have associated `<label>` elements
- Focus states are visible
- Semantic HTML throughout (`<main>`, `<nav>`, `<section>`, `<article>`, etc.)

---

## 9. Handoff Documentation (`docs/`)

### `docs/SETUP.md` must cover:
1. Prerequisites (Google account, GitHub account)
2. Cloning the repo
3. Adding brand kit files
4. Setting up Google Sheet (column headers, two sheets)
5. Deploying Apps Script and getting the web app URL
6. Publishing the Sheet as CSV and getting the URL
7. Updating `APPS_SCRIPT_URL` and `SHEET_CSV_URL` constants in the HTML files
8. Testing the full pipeline locally (open HTML directly in browser)
9. Embedding pages into WordPress (see WORDPRESS-EMBED.md)
10. Going live checklist

### `docs/WORDPRESS-EMBED.md` must cover:
- Two embedding approaches: (A) HTML block with `<iframe>` pointing to GitHub Pages URL, (B) copying HTML into a WordPress Custom HTML block
- Recommended approach: iframe from GitHub Pages staging URL (keeps code in one place, updates automatically on push to main)
- Known WordPress gotchas (iframe height, cookie consent banners, etc.)

### `docs/CONTENT.md`:
- All editable text content extracted into one file (path descriptions, prompt lab challenges, option instructions) so non-technical staff can update content without touching HTML

---

## 10. Quality Checklist

Before considering the build complete, Claude Code must verify:

- [ ] All 7 HTML pages exist and are valid HTML5
- [ ] All pages share `brand.css` + `styles.css` via consistent `<link>` tags
- [ ] All `[PLACEHOLDER]` strings are present and findable via grep
- [ ] Both forms POST correctly to a placeholder `APPS_SCRIPT_URL`
- [ ] Gallery page fetches and renders from a placeholder `SHEET_CSV_URL`
- [ ] Gallery auto-refresh is implemented (60s interval)
- [ ] Filter buttons work without page reload
- [ ] All pages are mobile-responsive
- [ ] GitHub Actions workflow file is valid YAML
- [ ] `appscript/Code.gs` handles both form types and returns correct JSON
- [ ] `appscript/DEPLOY.md` contains complete deployment steps
- [ ] `docs/SETUP.md` covers the full pipeline end-to-end
- [ ] README.md is complete with project overview and quick-start

---

## 11. Out of Scope

The following are explicitly **not** part of this build:

- Authentication or login
- Email notifications on submission
- Admin dashboard or submission moderation UI
- Payment or registration system
- Any WordPress plugin installation
- Real-time WebSocket updates (polling is sufficient)
- Automated badge generation (digital badge is a manual/external process)
