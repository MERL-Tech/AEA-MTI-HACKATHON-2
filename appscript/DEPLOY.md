# Apps Script Deployment Guide

> **Who this is for:** The person setting up the backend for the hackathon. You need a Google account with access to a Google Sheet and Google Apps Script.

---

## Overview

The submission forms POST JSON to a Google Apps Script web app, which appends rows to a Google Sheet. The gallery reads from that same sheet via a published CSV URL.

You will:
1. Create the Google Sheet
2. Paste `Code.gs` into Apps Script
3. Run `setupSheets()` to create the tabs and headers
4. Deploy as a web app and copy the URL into the HTML forms
5. Publish the sheet as CSV and copy those URLs into `gallery.js`

---

## Step 1 — Create the Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com) and create a new blank spreadsheet.
2. Name it something like **AEA/MTI Hackathon 2 — Submissions**.
3. Keep this tab open — you'll need the spreadsheet URL later.

---

## Step 2 — Open Apps Script

1. In your Google Sheet, click **Extensions → Apps Script**.
2. A new Apps Script editor opens. You'll see a default `myFunction()` — **delete it entirely**.
3. Paste the full contents of `Code.gs` into the editor.
4. Click the floppy-disk icon (or press `Cmd/Ctrl + S`) to save.
5. Give the project a name when prompted — e.g., **Hackathon2-Forms**.

---

## Step 3 — Run `setupSheets` to create tabs and headers

1. In the Apps Script editor, use the function dropdown at the top to select **`setupSheets`**.
2. Click **Run** (the play button ▶).
3. The first time, Google will ask you to authorize the script. Click **Review permissions → Allow**.
4. After it runs, go back to your spreadsheet. You should now see two tabs:
   - `Critique-Create` with 9 header columns
   - `Collab` with 8 header columns

---

## Step 4 — Deploy as a Web App

1. In the Apps Script editor, click **Deploy → New deployment**.
2. Click the gear icon next to "Type" and select **Web app**.
3. Configure the deployment:
   - **Description:** `v1` (or any label)
   - **Execute as:** `Me` (your Google account)
   - **Who has access:** `Anyone`
4. Click **Deploy**.
5. Google will ask you to authorize again — click **Authorize access → Allow**.
6. After deployment, you'll see a **Web app URL** — it looks like:
   ```
   https://script.google.com/macros/s/AKfycb.../exec
   ```
7. **Copy this URL.** You will paste it into both HTML forms in the next step.

---

## Step 5 — Add the Apps Script URL to the forms

Open each of the following files in your code editor and replace `[APPS_SCRIPT_URL]` with the URL you just copied:

- `pages/form-critique-create.html` — line near the top of the `<script>` block:
  ```js
  const APPS_SCRIPT_URL = '[APPS_SCRIPT_URL]';
  // becomes:
  const APPS_SCRIPT_URL = 'https://script.google.com/macros/s/YOUR_ID/exec';
  ```

- `pages/form-collab.html` — same constant, same replacement.

**Test it:** Open `form-critique-create.html` in a browser, fill in the form, and submit. Check your spreadsheet — a new row should appear in the `Critique-Create` tab within a few seconds.

---

## Step 6 — Publish the Sheet as CSV (for the gallery)

The gallery fetches a published CSV from the spreadsheet. You need two URLs — one per tab.

### For the Critique-Create tab:

1. In your Google Sheet, click on the **Critique-Create** tab to select it.
2. Click **File → Share → Publish to web**.
3. Under "Link" tab, set:
   - **Which sheet:** `Critique-Create`
   - **Format:** `Comma-separated values (.csv)`
4. Click **Publish** and confirm.
5. Copy the URL. It looks like:
   ```
   https://docs.google.com/spreadsheets/d/SPREADSHEET_ID/pub?gid=0&single=true&output=csv
   ```

### For the Collab tab:

1. In the same **Publish to web** dialog (or re-open it), change the sheet to **Collab**.
2. Set format to CSV and publish.
3. Copy the URL.

### Paste both URLs into `gallery.js`:

Open `assets/js/gallery.js` and replace the two constants near the top:

```js
const SHEET_CSV_URL_CRITIQUE_CREATE = '[SHEET_CSV_URL_CRITIQUE_CREATE]';
// becomes:
const SHEET_CSV_URL_CRITIQUE_CREATE = 'https://docs.google.com/spreadsheets/d/YOUR_ID/pub?gid=0&single=true&output=csv';

const SHEET_CSV_URL_COLLAB = '[SHEET_CSV_URL_COLLAB]';
// becomes:
const SHEET_CSV_URL_COLLAB = 'https://docs.google.com/spreadsheets/d/YOUR_ID/pub?gid=123456789&single=true&output=csv';
```

> **Tip:** The `gid` parameter is the sheet tab's ID. It appears in the publish URL automatically when you select a specific sheet.

---

## Step 7 — Verify the end-to-end pipeline

1. Submit a test entry through `form-critique-create.html`.
2. Check the spreadsheet — a row should appear in `Critique-Create`.
3. Wait up to 30 seconds for the published CSV to update (Google caches it briefly).
4. Open `gallery.html` in a browser. Your test entry should appear.

If the gallery shows "Could not load submissions," the CSV URL is likely incorrect or the sheet isn't published yet.

---

## Re-deploying after code changes

If you edit `Code.gs` and need to push the changes:

1. Click **Deploy → Manage deployments**.
2. Select your existing deployment and click the **pencil (edit) icon**.
3. Change the **version** to "New version".
4. Click **Deploy**.

> ⚠️ The web app URL **does not change** when you redeploy — you don't need to update the forms.

---

## Troubleshooting

| Problem | Likely cause | Fix |
|---|---|---|
| Form shows "Something went wrong" | Apps Script URL is wrong or not deployed | Double-check the URL; re-deploy if needed |
| Sheet tab not found error | Tabs weren't created / wrong name | Run `setupSheets()` again; check tab names match `SHEET_MAIN` and `SHEET_COLLAB` in `Code.gs` |
| Gallery shows no entries | CSV URL wrong or sheet not published | Re-publish the sheet tabs; check the `gid` parameter |
| Gallery shows stale data | Google CDN caching | Wait 1–2 minutes; the 60s poll will catch new data once it propagates |
| Authorization error on deploy | Permissions dialog was dismissed | Go to **Deploy → Manage deployments**, delete and redo |
