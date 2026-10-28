# PDF Flipbook

Self-hosted page-turn booklet for Squarespace 7.1. Drop a PDF in, get a flipbook with a
spine shadow, arrows, swipe, full screen and download. No monthly fee, no Yumpu/Heyzine.
Repo: <https://github.com/swtldesignco/sq-flipbook> · Version **1.4.0** · Status: **stable: live on swtldesignco staging**

---

# Part 1 — Basic install

## Overview

- Renders each PDF page with PDF.js and flips it with StPageFlip (both loaded for you).
- Desktop: two-page spread with a centre fold shadow. Phones: one page at a time, swipe
  to turn.
- Arrows, keyboard (← →), page count, **Full screen** and **Download** buttons.
- Links inside the PDF stay clickable.
- Type and colour follow Site Styles. Arrow icons are Heroicons outline.
- PDFs stay in Squarespace. Updating the booklet = replacing the file (see Step 3).

Requires the **Core plan or higher** (code blocks with scripts) and a **Code Block**.

## Step 1 — Upload the PDF

1. In any text or button block, add a link → **File** → upload the PDF → **Save**.
2. Copy the file's path. It looks like `/s/Your_File_Name.pdf`.
3. You can delete the temporary link afterwards. The file stays in the site's storage.

Tips: keep file names simple (no spaces), under ~20 MB, and export the PDF as single
pages, not spreads.

## Step 2 — Add the flipbook

1. Add a **Code Block** where the booklet should appear. Give it the full section width.
2. Paste [`snippets/code-block.html`](../snippets/code-block.html):

```html
<div class="sq-flipbook" data-pdf="/s/your-file.pdf"></div>
<script src="https://cdn.jsdelivr.net/gh/swtldesignco/sq-flipbook@1/sq-flipbook.js" defer></script>
```

3. Replace `/s/your-file.pdf` with your path from Step 1. **Paths are case-sensitive.**
4. Save, then check the **live** page (`?noredirect`). The editor disables scripts in code
   blocks, so the booklet only shows on the live site.

## Step 3 — Updating the PDF later

1. Upload the new PDF (Step 1) and copy its `/s/` path.
2. If the path is **exactly** the same as before, you're done: no code change. Hard-refresh
   to see it.
3. If Squarespace gave it a different path (for example a `-1` added to the name), change
   `data-pdf` in the code block to the new path.

Using a dated file name (`Owners_Manual_2026-11.pdf`) makes it obvious which version is
live.

## How PDFs reach the flipbook (the relay)

Browsers block a script from reading Squarespace `/s/` files directly. A small
Cloudflare Worker (`sq-flipbook-relay`) fetches the file and passes it on. It only accepts
`https://…/s/….pdf` URLs from Squarespace sites, including custom domains. You don't
configure anything. Non-Squarespace PDF URLs are tried directly first.

## Customizations (per-booklet attributes)

All go on the `<div class="sq-flipbook">`. Empty or missing = default.

| Attribute | Default | Options | Description |
|---|---|---|---|
| `data-pdf` | — (required) | `/s/file.pdf` or a full URL | The PDF to show |
| `data-max-width` | fills the block | px number | Maximum book width (two-page spread) |
| `data-max-height` | none | px number | Maximum book height; narrows the book to fit |
| `data-align` | `center` | `left` `center` `right` | Book position when narrower than the block |
| `data-mobile-arrows` | `true` | `true` `overlay` `false` | Phone view: arrows under the book / on the page edges / none (swipe only) |
| `data-links` | `true` | `true` `false` | Make links inside the PDF clickable |
| `data-fullscreen` | `true` | `true` `false` | Show the **Full screen** button |
| `data-download` | `true` | `true` `false` | Show the **Download** button |
| `data-accent` | site paragraph colour | any CSS colour | Arrow and control colour |
| `data-shadow` | `0.5` | `0`–`1` | Shadow strength while a page flips |
| `data-speed` | `700` | ms | Flip duration |
| `data-scale` | `1.5` | `1`–`2` | Render sharpness. Higher = crisper text, slower first load |
| `data-relay` | the SWTL relay | URL | Use a different relay (rarely needed) |

## Per-site checklist

- [ ] Core plan or higher.
- [ ] PDF uploaded; `data-pdf` path copied exactly (case-sensitive).
- [ ] Code block holds the div + script. One script tag per page is enough for several booklets.
- [ ] Tested on the live site (`?noredirect`), not in the editor.
- [ ] Desktop: two-page spread, fold shadow, arrows work. Keyboard ← → works.
- [ ] Phone: one page, swipe works, arrows as chosen.
- [ ] Full screen and Download behave as the client wants (turn off Download if needed).
- [ ] Links in the PDF go to the right places.
- [ ] Handoff note: how to replace the PDF (Step 3).

---

# Part 2 — Styling

Add to **Design → Custom CSS** as its own numbered section
([`snippets/custom-css-examples.css`](../snippets/custom-css-examples.css)).

| Variable | Default | Description |
|---|---|---|
| `--sqfb-accent` | `var(--paragraphMediumColor)` | Arrows and controls (`data-accent` wins) |
| `--sqfb-arrow-size` | `44px` | Side arrow circles |
| `--sqfb-arrow-stroke` | `1.5` | Arrow line weight |
| `--sqfb-controls-size` | `.9rem` | Page count, Full screen, Download text |
| `--sqfb-controls-gap` | `18px` | Space between the controls |
| `--sqfb-spine` | `.2` | Centre fold shadow, `0`–`1` |
| `--sqfb-link-hover` | `rgba(255,220,0,.18)` | Highlight over PDF links on hover |
| `--sqfb-fullscreen-bg` | `var(--siteBackgroundColor)` | Background in full screen |
| `--sqfb-fullscreen-size` | `88` | How much of the screen the book fills in full screen (%) |

Targeting: `.sq-flipbook`, `.sq-flipbook__stage`, `.sq-flipbook__book`,
`.sq-flipbook__spine`, `.sq-flipbook__arrow`, `.sq-flipbook__arrow--prev`,
`.sq-flipbook__arrow--next`, `.sq-flipbook__controls`, `.sq-flipbook__tool`,
`.sq-flipbook__link`. State classes: `.sq-flipbook--landscape` (two-page),
`.sq-flipbook--portrait` (one page), `.sq-flipbook--flipping`.

---

# Part 3 — For developers

```
div.sq-flipbook[data-pdf][data-ready]   ← your div; tabindex=0 for keyboard
  div.sq-flipbook__stage
    div.sq-flipbook__book                ← StPageFlip mounts here
      div.sq-flipbook__page > img        ← each PDF page rendered to an image
        a.sq-flipbook__link              ← PDF link areas (when data-links is on)
    div.sq-flipbook__spine               ← fold shadow (two-page mode)
    button.sq-flipbook__arrow--prev / --next
  div.sq-flipbook__controls              ← page count, Full screen, Download
```

| Name | Description |
|---|---|
| `SqFlipbook.init()` | Builds any new `.sq-flipbook[data-pdf]` not built yet (e.g. after AJAX loads) |

Dependencies (loaded on demand, pinned): `pdfjs-dist@3.11.174`, `page-flip@2.0.7`.

---

# Part 4 — Support

## Troubleshooting

The booklet shows a plain message when it can't load. What each one means:

| Message | Fix |
|---|---|
| **PDF not found. Check the data-pdf path (case-sensitive).** | The `/s/` path is wrong. Re-copy it from the file link. |
| **The server returned an error for the PDF** | The file was removed or replaced under a different name. |
| **The file is not a valid PDF.** | Re-export the PDF; the upload may be corrupt. |
| **The browser blocked the PDF request.** | The relay couldn't be reached. Check `sq-flipbook-relay` in Cloudflare. |

- **Nothing shows in the editor:** expected. Check the live page.
- **Stuck on "Loading document" in a background tab:** browsers pause drawing in hidden
  tabs. Bring the tab to the front.
- **Blurry text:** raise `data-scale` to `2`.

## Deployed on

- swtldesignco.squarespace.com/pdf-booklet-test (v1.4.0).
- Planned: paragusit.com/owners-manual (replacing the Yumpu embed).

## Moved

Previously `github.com/zsawtelle-lgtm/sq-flipbook` (moved Oct 2026). CDN links should use
`swtldesignco`.
