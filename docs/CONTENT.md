# Content Reference — AEA/MTI Hackathon 2

> **Who this is for:** Non-technical staff who need to update event content (descriptions, dates, challenge text, path copy) without editing HTML directly.
>
> Each section below identifies **what the content is**, **where it lives in the HTML**, and **how to edit it safely**.

---

## How to edit content

All content lives in the `pages/` folder. Each file is a plain HTML file you can open in any text editor. Look for the text between HTML tags — for example:

```html
<p>This is the text you'd edit.</p>
```

You do not need to understand HTML to make text edits. Just be careful not to delete any `<` or `>` characters that surround the text.

**Recommended workflow:**
1. Open the file in a text editor (VS Code, TextEdit, Notepad)
2. Use **Find** (`Cmd/Ctrl + F`) to locate the text you want to change
3. Edit only the visible text, not the surrounding tags
4. Save the file
5. Commit and push to GitHub — the site will auto-update within 2 minutes

---

## Event Dates

**File:** `pages/index.html`
**Search for:** `[EVENT_DATES]`

Replace `[EVENT_DATES]` in the hero eyebrow section with real dates. Example:
```
May 5–16, 2025 · Virtual
```

---

## Landing Page — Hero

**File:** `pages/index.html`
**Section:** Inside `<section class="hero">`

Editable items:
- **Event subtitle / tagline** — the `<p class="hero__description">` paragraph
- **CTA button labels** — the `.btn` elements inside `.hero__actions`

---

## Landing Page — "What is this?" section

**File:** `pages/index.html`
**Section:** Inside `<section class="prose-section">`

This is the plain-text explanation of the event. Edit the `<p>` paragraphs freely.

---

## Landing Page — How It Works (5 steps)

**File:** `pages/index.html`
**Section:** Inside `<section class="how-it-works">`

Each step has a `.step__label` (bold name) and `.step__desc` (description). Edit those two elements per step.

---

## Landing Page — Path Card Descriptions

**File:** `pages/index.html`
**Section:** Inside `<section class="path-cards-section">`

Each path card (`<article class="path-card">`) contains:
- `.path-card__name` — path name (Critique / Create / Collab)
- `.path-card__tagline` — one-line tagline
- `.path-card__options li` — three bullet options
- `.path-card__time` — estimated time

---

## Landing Page — Hot Take Wall

**File:** `pages/index.html`
**Search for:** `[HOT_TAKE_URL]`

Replace the `[HOT_TAKE_URL]` placeholder in the `href` attribute with the real Padlet or Slack URL. Also edit the `.hot-take-wall__desc` paragraph text as needed.

---

## Critique Path — Option Descriptions

**File:** `pages/critique.html`
**Section:** Inside `<section class="options-section">`

There are three option cards. Each `<article class="option-card">` contains:
- `.option-card__name` — option name
- `.option-card__time` — estimated time badge
- `.option-card__desc` — 2–3 sentence description
- `.option-card__steps li` — numbered steps
- `.option-card__include li` — what to include checklist

Edit any of these elements to update the instructions.

---

## Create Path — Overview and Option Descriptions

**File:** `pages/create.html`

Same structure as Critique. Three option cards, plus the Prompt Lab challenge section below.

---

## Create Path — Prompt Lab Challenges

**File:** `pages/create.html`
**Section:** Inside `<section class="prompt-lab-section">`
**Search for:** `class="challenge-card"`

There are five challenge cards. Each has:
- `.challenge-card__name` — challenge title (e.g., "Challenge 1: The Plain-Language Translator")
- `.challenge-card__time` — estimated time
- `.challenge-card__desc` — 2–3 sentence description of the task
- `.challenge-card__intent` — the intent paragraph (what a good prompt should accomplish)

To add a challenge, copy an entire `<article class="challenge-card">` block and paste it after the last one. Update the content inside.

To remove a challenge, delete the entire `<article class="challenge-card">` block (from `<article` to the closing `</article>`).

---

## Create Path — Prompt Lab Challenges (full text for editing reference)

### Challenge 1: The Plain-Language Translator
**Time:** ~10 min
**Description:** Take a dense, jargon-heavy evaluation finding (from a real report or one you compose) and craft a prompt that gets an AI to rewrite it for a non-specialist community audience. The challenge is to instruct the AI to preserve the core finding while removing barriers to understanding — without dumbing it down or introducing errors.
**Intent:** A good prompt will specify the target audience clearly, set a reading-level expectation, ask the AI to flag any hedging it removes, and produce plain prose — not bullet points.

### Challenge 2: Interview Question Architect
**Time:** ~12 min
**Description:** You're given a two-sentence theory of change for a youth workforce program (or write your own). Craft a prompt to generate a stakeholder interview question set — one set for program staff and one for participants. The AI should produce open-ended, culturally aware questions directly tied to the theory of change's key assumptions.
**Intent:** A strong prompt will specify the stakeholder roles, the number of questions, the methodological grounding (semi-structured interview), and ask the AI to annotate which assumption each question probes.

### Challenge 3: The Nuance Keeper
**Time:** ~10 min
**Description:** Paste a short excerpt of qualitative data — five interview snippets on a contentious topic — into an AI tool and craft a prompt that gets it to summarize the data without flattening divergent perspectives or over-generalizing from one or two voices. You can use the sample data provided below, or bring your own.
**Intent:** A good prompt will instruct the AI to surface both dominant themes and outliers, preserve participant voice through direct quotes, and explicitly flag where it is uncertain or where data is thin.

### Challenge 4: The AI Skeptic
**Time:** ~15 min
**Description:** An AI tool has produced a program recommendation based on evaluation data (sample provided below). Your task: craft a prompt that gets the same AI to critique its own recommendation — identifying weak assumptions, missing contextual factors, and at least two potential unintended consequences of acting on it.
**Intent:** A strong prompt will assign the AI a specific skeptical role, set a minimum number of risks to identify, and ask it to rank them by severity. Bonus: prompt the AI to suggest what additional evidence would change its assessment.

### Challenge 5: Ethical Use Statement Drafter
**Time:** ~12 min
**Description:** Your organization is beginning to use AI tools in an evaluation engagement for a government client. Craft a prompt that helps you draft a brief ethical use statement — one you could actually share with the client — covering data privacy, informed consent, attribution, and known AI limitations relevant to evaluation contexts.
**Intent:** A good prompt will produce a statement that is honest about AI's role in the work, accessible to a non-technical audience, grounded in specific evaluation ethics (e.g., AEA ethics guidelines), and avoids both over-promising and reflexive disclaimers that say nothing.

---

## Collab Path — Starter Templates

**File:** `pages/collab.html`
**Section:** Inside `<section class="options-section">` (the first one, with the template links)

There are three template links. Each `<a class="template-link">` has:
- `.template-link__name` — template name
- `.template-link__desc` — one-line description
- The `href="[TEMPLATES_URL]"` — replace with the real link to each template

To update a template link:
1. Find the `<a href="[TEMPLATES_URL]"` for the template you want to update
2. Replace `[TEMPLATES_URL]` with the actual URL
3. Update the `.template-link__name` and `.template-link__desc` text as needed

---

## Collab Path — Collab Starter Template Descriptions (full text for reference)

### Template 1: Survey QA Helper
A chatbot system prompt that reviews survey questions for clarity, leading language, and response-scale issues.

### Template 2: AI Red-Team Checklist
A structured checklist template for critiquing AI-generated evaluation outputs, organized by type of error.

### Template 3: Data Cleaning Automation Note
A step-by-step guide and prompt template for using AI to assist with basic data cleaning tasks in evaluation datasets.

---

## Footer — Slack URL

**File:** All pages (footer section of each)
**Search for:** `[SLACK_URL]`

All footers have a link to the Slack channel. Replace `[SLACK_URL]` in each file's footer with the real Slack invite URL. There are six occurrences total (one per page).

> **Tip:** Use Find & Replace across the whole `pages/` folder in VS Code (`Cmd + Shift + H`) to replace all at once.

---

## Submission Forms — Confirmation Messages

**File:** `pages/form-critique-create.html` and `pages/form-collab.html`
**Search for:** `id="form-success"`

The success message shown after a successful submission is inside the `<div id="form-success">` element. Edit the text in the `<p>` tags inside it.

---

## Adding a new path option

To add a new option to an existing path:

1. Copy an existing `<article class="option-card">` block in the relevant path HTML file
2. Paste it after the last option card inside the `<div class="option-cards">` container
3. Update all the text inside (name, time, description, steps, includes)
4. In `pages/form-critique-create.html`, find the `PATH_OPTIONS` object in the `<script>` block and add the new option name to the appropriate array

---

## Replacing placeholder text before launch

Run this search in your code editor to find all remaining placeholders:

```
[APPS_SCRIPT_URL]
[SHEET_CSV_URL_CRITIQUE_CREATE]
[SHEET_CSV_URL_COLLAB]
[SLACK_URL]
[HOT_TAKE_URL]
[TEMPLATES_URL]
[EVENT_DATES]
[STAGING_URL]
```

Each one appears only in specific files (see `docs/SETUP.md` for the full table). None should remain at launch.
