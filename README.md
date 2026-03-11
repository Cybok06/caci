# CACI-USA Static Website

This project is now a full static site.

## Run locally

From the project root:

```bash
python -m http.server 8000
```

Then open:

- `http://localhost:8000/index.html`

## Structure

- Pages: `index.html`, `aboutus.html`, `ministry.html`, `leadership.html`, `blog.html`, `events.html`, `gallery.html`, `contact.html`
- Shared styles: `assets/css/styles.css`
- Shared logic: `assets/js/common.js`
- Page logic: `assets/js/*.js`
- Content source: `data/*.json`

## Notes

- Backend/admin features were removed.
- Content is read-only and rendered from exported JSON files.
