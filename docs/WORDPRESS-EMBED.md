# WordPress Embedding Guide — AEA/MTI Hackathon 2

> **Who this is for:** The WordPress site administrator embedding the hackathon pages into MTI's site.

---

## Overview

There are two main approaches to embed the hackathon pages into WordPress:

| Approach | Pros | Cons |
|---|---|---|
| **A — iframe from GitHub Pages** | Easiest to maintain; updates automatically on every push to `main` | Requires managing iframe height; some WordPress themes may restrict iframes |
| **B — Paste HTML directly into WordPress** | No dependency on GitHub Pages; content lives in WordPress | Must manually re-paste whenever pages are updated |

**Recommended: Approach A (iframe).** It keeps the source of truth in one place (this Git repo) and updates automatically.

---

## Approach A — iframe from GitHub Pages (recommended)

### Step 1: Get your GitHub Pages staging URL

Your staging URL looks like:
```
https://YOUR-ORG.github.io/aea-mti-hackathon-2/
```

Individual pages are at:
- `https://YOUR-ORG.github.io/aea-mti-hackathon-2/index.html`
- `https://YOUR-ORG.github.io/aea-mti-hackathon-2/gallery.html`
- etc.

### Step 2: Create a WordPress page for each hackathon page

For each page you want to embed:

1. In WordPress, create a new **Page** (not a post).
2. Give it an appropriate title, e.g., "Hackathon 2 — Landing Page".
3. In the block editor, add a **Custom HTML** block.
4. Paste the iframe code below, substituting the correct GitHub Pages URL.

### iframe code template

```html
<style>
  .hackathon-embed {
    width: 100%;
    border: none;
    min-height: 800px;
    display: block;
  }
</style>
<iframe
  class="hackathon-embed"
  src="https://YOUR-ORG.github.io/aea-mti-hackathon-2/INDEX-PAGE.html"
  title="AEA/MTI Hackathon 2"
  loading="lazy"
  scrolling="no"
  id="hackathon-iframe-1"
></iframe>
<script>
  // Auto-resize iframe to fit content height (avoids double scrollbars)
  (function() {
    var iframe = document.getElementById('hackathon-iframe-1');
    function resize() {
      try {
        iframe.style.height = iframe.contentWindow.document.body.scrollHeight + 'px';
      } catch(e) {
        // Cross-origin: fall back to a fixed generous height
        iframe.style.height = '1200px';
      }
    }
    iframe.addEventListener('load', resize);
    window.addEventListener('resize', resize);
  })();
</script>
```

> **Cross-origin note:** If your GitHub Pages domain differs from your WordPress domain, the auto-resize script will fall back to a fixed height (1200px). Adjust this value to suit your longest page.

### Recommended iframe heights by page

| Page | Suggested fallback height |
|---|---|
| `index.html` (landing) | `1800px` |
| `critique.html` | `1600px` |
| `create.html` | `2200px` (longer — includes Prompt Lab challenges) |
| `collab.html` | `1700px` |
| `form-critique-create.html` | `1200px` |
| `form-collab.html` | `1100px` |
| `gallery.html` | `1400px` |

---

## Approach B — Paste HTML directly

Use this only if you can't use iframes.

1. Open the GitHub Pages URL for the page you want.
2. View source (`Cmd/Ctrl + U`) and copy all the HTML.
3. In WordPress, create a page and add a **Custom HTML** block.
4. Paste the full HTML.

**Important caveats:**
- The HTML references assets via relative paths (`../assets/...`). These will break when copied into WordPress unless you either:
  - Upload the `assets/` folder to your WordPress media library and update all `href` and `src` paths, **or**
  - Change all relative paths to absolute GitHub Pages URLs before pasting.
- You will need to re-paste the HTML every time the source changes.

---

## Known WordPress gotchas

### 1. iframe height / double scrollbars
WordPress themes often set `max-width` or `overflow` on content areas that can clip iframes. If you see double scrollbars:
- Add `overflow: hidden` to the iframe's parent element in WordPress CSS.
- Or set a generous fixed height instead of auto-resize.

### 2. Cookie consent banners
If your WordPress site has a cookie consent plugin, it may render over the iframe. Adjust the consent banner's `z-index` to be lower than the iframe, or use a consent mode that doesn't interfere with embedded content.

### 3. WordPress stripping HTML
Some WordPress versions or plugins strip `<script>` tags from Custom HTML blocks. If the iframe auto-resize script is removed:
- Use a plugin like **WPCode** or **Code Snippets** to add the script to the page header instead.
- Or simply set a fixed iframe height.

### 4. HTTPS required
GitHub Pages serves over HTTPS. Your WordPress site must also be HTTPS, or browsers will block the iframe as "mixed content."

### 5. Gallery auto-refresh inside iframes
The gallery's 60-second auto-refresh works inside iframes — no extra setup needed. However, if the WordPress page itself caches aggressively (e.g., WP Super Cache), the *WordPress page* may be cached but the *gallery's fetch requests* are made directly from the visitor's browser to Google Sheets, so gallery data stays live regardless.

---

## Recommended WordPress page structure

| WordPress Page | Source URL | Notes |
|---|---|---|
| Hackathon 2 — Home | `.../index.html` | Main landing page; link from site nav |
| Critique Path | `.../critique.html` | Or link directly from the Home page |
| Create Path | `.../create.html` | |
| Collab Path | `.../collab.html` | |
| Submit (Critique/Create) | `.../form-critique-create.html` | |
| Submit (Collab) | `.../form-collab.html` | |
| Open Gallery | `.../gallery.html` | Highlight in site nav — this is the "live" page |

You don't need to create a WordPress page for every path page. You can link directly to the GitHub Pages URLs from within WordPress content instead.
