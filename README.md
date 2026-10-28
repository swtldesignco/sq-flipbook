# sq-flipbook

Self-hosted PDF flipbook for **Squarespace 7.1**. Drop a PDF in, get a page-turn
booklet: two-page spread with a fold shadow on desktop, swipe on phones, full screen,
download and clickable PDF links. Replaces Yumpu/Heyzine embeds with no monthly fee.

- PDFs stay in Squarespace (`/s/` files). Updating = replacing the file.
- One Code Block per booklet; options as `data-*` attributes
- Type and colour follow Site Styles; Heroicons arrows

See **[docs/INSTALL.md](docs/INSTALL.md)**.

## Drop-in
```html
<div class="sq-flipbook" data-pdf="/s/your-file.pdf"></div>
<script src="https://cdn.jsdelivr.net/gh/swtldesignco/sq-flipbook@1/sq-flipbook.js" defer></script>
```
`@1` follows the latest `1.x` tag. Never use `@latest`. Full option list:
[`snippets/code-block.html`](snippets/code-block.html).
