# CLAUDE.md

This file gives guidance to Claude Code (claude.ai/code) when working in this repository.

## What this is

A personal portfolio site for Dhatu Kertayuga (System Development at Pasifik Satelit Nusantara since 04/2025, previously Application Management at BCA Syariah, Jakarta). It has two pages, a home landing page and a Projects page, in a warm, editorial style. A tiny Express.js server serves them. There is no build step, no framework, no database, and no tests.

## Commands

```bash
npm install      # install deps (only runtime dep: express ^4.22.2)
npm start        # node index.js → http://localhost:3000 (override with PORT env var)
```

- There is no dev/watch script. Restart the server after changing `index.js`. Changes to `public/` only need a browser refresh.
- `npm test` is the npm placeholder and always exits with code 1. No test framework or linter is configured.
- The project is developed on Windows (`D:\Kodingan\...`), so use cross-platform paths (`path.join`) in server code.

## Layout

```
index.js           Express server (entire backend)
public/index.html     Home page (hero, about, skills, 3 featured projects, experience, contact)
public/projects.html  Projects page (all projects in a 2-column grid), served at /projects
public/styles.css     Shared stylesheet for both pages
package.json       CommonJS project, "main": "index.js"
README.md          User-facing docs and design principles
```

## Backend (`index.js`)

- Middleware: `express.json()`, `express.urlencoded()`, `express.static('public')`.
- Routes:
  - `GET /` sends `public/index.html`. `express.static` already serves this, so the route is redundant but harmless.
  - `GET /projects` sends `public/projects.html`. Link to `/projects`, not `/projects.html`.
  - `GET /api` returns `{ message: 'Portfolio API', version: '1.0.0', author: 'Dhatu Kertayuga' }`.
  - `GET /health` returns `{ status: 'healthy' }`.
- The catch-all 404 returns **JSON** (`{ error: 'Page not found' }`), not an HTML page.
- The 500 error handler logs `err.stack` and returns JSON.
- `module.exports = app` is exported, but `app.listen()` runs unconditionally at import. If you add supertest-style tests, guard it with `if (require.main === module)`.
- Add new routes **before** the 404 handler. Express middleware order matters.

## Frontend (`public/`)

The pages are plain HTML files. All CSS lives in `public/styles.css`, loaded as `/styles.css` (root-absolute, so it works from any route), and each page keeps its own small inline `<script>`. The only external resource is Google Fonts (IBM Plex Sans + IBM Plex Mono). Don't add other external CSS or JS unless asked. Opening the files straight from disk (`file://`) breaks the `/styles.css` and `/projects` links, so preview through `npm start`.

The design is "Mission Control, Light" (chosen from a redesign preview canvas, Oct 2026): a cool grey ground (`--bg`) with a faint 48px grid, white rounded modules (`.mod`) with hairline borders that turn orange on hover, IBM Plex Sans for text and IBM Plex Mono for labels, tags and the nav, and one orange accent (`--accent`, with the darker `--accent-text` for small text so it passes contrast). The header is sticky and blurred with a `DHATU//SYSDEV` brand. The home hero has a decorative orbit SVG (one satellite slowly rotates) and a `dl.readout` card. Sections are labelled like modules (`01 / About`, `02 / Stack`, `03 / Missions`, `04 / Log`, `05 / Comms open`); the contact block is a full orange `.comms` panel.

- **Home sections**, in order: `top` (hero), `about`, `skills` (labelled Stack), `projects` (labelled Missions), `experience` (labelled Log), `contact`. The ids stay the same as before so old links work. The nav links (`.nav-link`) point at `#about` through `#experience`. Contact is the `.btn-primary` button in the nav. The projects section shows 3 featured cards and a `.more-link` ("View all 17 missions →") to `/projects`.
- **Projects page**: `.page-head` header (back link, eyebrow, h1, lead), then `.projects-grid` (2 columns, 1 below 720px) of `.project-card`s, then the contact band and footer. Its nav uses `/#about`-style links back to home, and Projects carries `.active` + `aria-current="page"`. It holds 17 project cards (5 from Pasifik Satelit Nusantara first, then BCA Syariah and academic work). The `.project-placeholder` class (dashed border) is still in the CSS for bracketed empty slots.
- **Adding a project**: add a card to `projects.html`. If it should be featured, also add it to the home list, and keep the home list at about 3 cards. Project cards are `article.mod.project-card` elements (no links yet): an `h3`, the organisation or context in a `span.label` inside `.project-head`, a `p`, and a `.tags` row of `.tag` spans for the tech stack. On the Projects page an odd last card spans both grid columns automatically.
- **Shared markup**: the header nav, contact band and footer are duplicated in both pages. Change them in both places.
- **Design tokens** are CSS custom properties on `:root`. Colours are `--bg`, `--grid-line`, `--surface`, `--chip`, `--fg`, `--fg-2`, `--muted`, `--muted-2`, `--line`, `--line-card`, `--line-strong`, `--orbit`, `--accent`, `--accent-text`, `--on-accent` and `--shadow`. Layout uses `--max-width` (1120px), `--pad-x`, `--radius` and `--radius-lg`. Fonts are `--font-sans` and `--font-mono`. To re-theme, change `--accent` and `--accent-text` together (keep `--accent-text` dark enough for 4.5:1 on white). White text on `--accent` is only used at large sizes in `.comms`.
- **Reusable classes**: `.wrap` (centred container), `.section` + `.section-label` + `.section-body`, `.label` (+ `.label-accent`), `.mod` (white module/card), `.tag`/`.tags`, `.btn` + `.btn-primary`/`.btn-secondary`/`.btn-sm`, `.stack-card`, `.project-card`, `.log` (experience timeline), `.comms`, `.readout`, `.page-head`, `.projects-grid`, `.more-link`, `.back-link`.
- **Breakpoints**: one at `720px` (`--pad-x: 20px`, smaller hero and orbit, single-column Projects grid, larger `scroll-padding-top` because the nav wraps). Everything else reflows with flex-wrap.
- **JS behaviour** (home page; the projects page only runs the year and reveal parts):
  1. The footer year is set from `new Date()`.
  2. An IntersectionObserver adds `.active` to the `.nav-link` for the section in view. Sections without a nav link are skipped.
  3. An IntersectionObserver adds `.visible` to `.reveal` sections for the fade/slide-in.
- **Progressive enhancement**: a head script adds `class="js"` to `<html>`. Sections are hidden only under `.js .reveal`, so the page stays readable if JS fails. `prefers-reduced-motion` turns off the animations.
- **Adding a home section**: use `<section id="x" class="section wrap reveal"><h2 class="section-label">0N / Name</h2><div class="section-body">…</div></section>`, and add a `.nav-link` if it should appear in the nav.

## Gotchas / known issues

- The content comes from Dhatu's CV (name, role, experience, skills, email, phone, LinkedIn, projects) and from details Dhatu gave in chat about the Pasifik Satelit Nusantara projects. Project descriptions are summarised from the CV, so check them. There is no GitHub link yet because the CV doesn't give one. Don't invent personal details, project links or screenshots.
- Smooth scrolling is pure CSS (`scroll-behavior` + `scroll-padding-top: 80px`, 130px under 720px, for the sticky header). If the header's height changes, adjust `scroll-padding-top`.
- `node_modules/` is committed to the repo even though `.gitignore` lists Node exclusions. The committed copy holds express 5.x, while `package.json` asks for `^4.22.2`. Run `npm install` locally rather than relying on the committed folder, and don't stage `node_modules/` changes unless asked.

## Conventions

- CommonJS (`require`), 2-space indentation, single quotes in JS.
- Keep the backend minimal. The site is primarily static content, and README lists deployment to any Node host via `npm start`.
- Update README.md when you add routes, scripts, or sections, because it documents all three.
- New UI should reuse the existing tokens and classes rather than hard-coded colours.
