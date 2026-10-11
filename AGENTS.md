# AGENTS — Maximo Security website

Checklist (what I'll do next)

- Read repository and confirm there was no existing AGENTS.md (done).
- Add a concise, minimal agent guide targeted to this static site.
- Call out exact files and conventions to preserve project consistency.

---

Project overview

- Type: Small static website (HTML, CSS, vanilla JS). No build system, no package.json, no server-side code in the repo.
- Key files:
  - `index.html`, `about.html`, `business.html`, `residential.html`, `services.html`, `contact.html` — pages.
  - `style.css` — single stylesheet.
  - `app.js` — single JS entry that contains UI behaviour, icons and the `services` data.
  - `assets/` — images and icons (webp/png/svg).

Agent goals (what changes are typically needed)

- Content updates (copy, images).
- Add or modify a service (title, image, details) — update only `app.js` and `assets/` (see example below).
- Small UI fixes (menu, dialogs, accessibility attributes) — edit `app.js` or the HTML page(s) that contain the markup.
- Enable server-side features (if requested): add an API and change form handling in `app.js` and `contact.html`.

How the site is structured / important patterns to preserve

- Single-source UI behaviour: `app.js` contains the interactive logic for all pages. Keep changes consolidated there where appropriate rather than scattering behaviour across multiple files.
  - The `icons` map (top of `app.js`) provides inline SVG fragments referenced by name. When adding or changing icons, update the `icons` object.
  - The `services` array in `app.js` defines the services displayed on `services.html`. Add/remove/modify entries here to change the service cards and dialog content.
  - Example: to add a new service, add an object to the `services` array with `title`, `image` (relative path in `assets/`), `alt`, `description`, and `details` (array of strings). The `service-grid` is populated automatically by `app.js`.

- Accessibility and small JS-driven UX:
  - Navigation: the header contains a `button.menu-toggle` and `<nav id="navigation">`. `app.js` toggles `nav.open` and manages `aria-expanded` / `aria-label` — preserve this attribute behaviour when changing markup.
  - Dialogs: pages use `<dialog>` elements with `.dialog-close` buttons. `app.js` wires click/overlay behaviour and `showModal()` usage. Keep the existing close semantics (close button, overlay click, Escape key).
  - Form: `contact.html` contains `#enquiry-form`. Currently the site does not send data; submitting prepares a downloadable request and shows `#request-dialog`. If you implement a server-side endpoint, modify `app.js` to POST the `FormData` (and update the privacy text accordingly).

Cache-busting / versioning conventions

- HTML pages reference `style.css` and `app.js` with explicit query strings (e.g. `style.css?v=9`, `app.js?v=9`). When you change static assets you must bump the `v=` number on every HTML page that references them (search for `?v=` across the repo). This is the project’s cache-busting approach — keep it consistent.

Image and asset conventions

- Images in `assets/` are referenced with width/height attributes in the markup (see `index.html` logo). Maintain width/height where practical for layout stability.
- Use `loading="lazy"` on secondary images and `fetchpriority="high"` on hero/critical images (follow existing examples).
- New images: add optimized `.webp` where possible and include fallbacks only when needed.

Editing patterns and examples (concrete steps)

1) Add a new service (minimal, exact steps)
  - Add the image to `assets/` (prefer `*.webp`).
  - Edit `app.js` and append a new object to the `services` array, for example:

    {title: 'New Service', image: 'assets/service-new.webp', alt: 'Alt text', description: 'Short summary', details: ['Point one','Point two']}

  - Update the `?v=` query on HTML pages referencing `app.js` (if you changed `app.js`) so browsers pick up the change. Example: change `app.js?v=9` → `app.js?v=10` on pages that include it.
  - No other HTML edits are required; the `service-grid` is rendered dynamically.

2) Edit site CSS or JS (versioning & verification)
  - Modify `style.css` or `app.js` as needed.
  - Increment the `?v=` value for the modified asset on every HTML file that references it.
  - Open the site in a browser (see run locally commands) and verify:
    - navigation toggle opens/closes
    - dialogs open and close (click close, overlay click, Escape)
    - service cards render and `LEARN MORE` opens the service dialog with correct content
    - contact form prepare/download flow still works

3) Enable server-side contact submission (high-level notes)
  - This is not present in the repo; adding it will change the site's scope. If implementing, do the following:
    - Add a minimal API (e.g., a Netlify Function, Vercel Serverless function, or a small Express app) hosted separately from this static site.
    - Change the form handling in `app.js` from local `FormData` -> fetch POST to the API, with client-side validation preserved.
    - Update `contact.html` privacy copy to reflect the new backend and data retention.

Local development / testing

- No build tools required — serve the folder and open in a browser.
- Quick suggestions:
  - Python 3: `python3 -m http.server 8000` (from repo root) and open http://localhost:8000
  - Node (if you prefer): `npx serve -s . 8000` or `npx http-server -c-1`
- Use browser developer tools for JS and layout debugging. Console errors will point to `app.js` lines.

Search patterns useful to agents

- Find where version strings must be updated: search for `\?v=` in HTML files.
- Find services definition: search for `const services` in `app.js`.
- Find icons: search for `const icons` in `app.js`.
- Find dialogs: search for `<dialog` in HTML files.

Conventions to preserve (do not change without explicit reason)

- Keep the single `app.js` authoring model for UI behaviour — prefer enhancing it over sprinkling new scripts across pages.
- Preserve `aria-*` attributes placed in HTML (menu toggle, nav, dialog close buttons) to maintain accessibility.
- Maintain the simple cache-busting `?v=` approach and update every page referencing the modified asset.

Notes / TODOs discovered by reading the repo (actionable items)

- Contact details placeholders: `[PHONE NUMBER]`, `[WHATSAPP NUMBER]`, `[EMAIL ADDRESS]`, `[SERVICE AREA]` appear across pages. Replace consistently when the real details are available (search for `\[PHONE NUMBER\]` etc.).
- `app.js` currently prepares but does not send enquiries. If the product requires live submissions, implement a backend and update `app.js` accordingly.
- OpenStreetMap iframe is embedded on `contact.html` and uses `referrerpolicy="no-referrer"` and `loading="lazy"`. If placing a pin, update the iframe src.

What I changed in this repo

- Added `AGENTS.md` at project root because none existed. This file contains targeted guidance for AI coding agents working on this static site: run steps, editing patterns, versioning rules, and concrete examples for common edits.

If you want, next I can:
- Add a short script to automatically bump `v=` cache-bust numbers across all HTML files when `app.js` or `style.css` change.
- Implement a minimal CI check that ensures the `?v=` values are consistent across pages.

---

End of AGENTS.md

