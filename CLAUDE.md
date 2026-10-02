# CLAUDE.md

This file gives guidance to Claude Code (claude.ai/code) when working in this repository.

## What this is

A personal portfolio site for "Kiki". It has two pages, a home landing page and a Projects page, in a warm, editorial style. A tiny Express.js server serves them. There is no build step, no framework, no database, and no tests.

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
  - `GET /api` returns `{ message: 'Portfolio API', version: '1.0.0', author: 'Kiki' }`.
  - `GET /health` returns `{ status: 'healthy' }`.
- The catch-all 404 returns **JSON** (`{ error: 'Page not found' }`), not an HTML page.
- The 500 error handler logs `err.stack` and returns JSON.
- `module.exports = app` is exported, but `app.listen()` runs unconditionally at import. If you add supertest-style tests, guard it with `if (require.main === module)`.
- Add new routes **before** the 404 handler. Express middleware order matters.

## Frontend (`public/`)

The pages are plain HTML files. All CSS lives in `public/styles.css`, loaded as `/styles.css` (root-absolute, so it works from any route), and each page keeps its own small inline `<script>`. The only external resource is Google Fonts (Space Grotesk + Work Sans + JetBrains Mono). Don't add other external CSS or JS unless asked. Opening the files straight from disk (`file://`) breaks the `/styles.css` and `/projects` links, so preview through `npm start`.

The design started from the "Portfolio Landing Page" Claude Design canvas and was re-themed to a white/blue tech style. It has a white and blue-tinted background with a faint blueprint grid, Space Grotesk headings over a Work Sans body with JetBrains Mono for labels and chips, a single blue accent (plus a cyan secondary), a sticky blurred nav with a `~/Kiki` brand and blinking cursor, a terminal card in the home hero, two-column sections (a small mono `//` label on the left, content on the right), white cards with hairline blue borders, and a dark navy contact band and footer.

- **Home sections**, in order: `top` (hero), `about`, `skills`, `projects`, `experience`, `contact`. The nav links (`.nav-link`) point at `#about` through `#experience`. Contact is the `.btn-primary` button in the nav. The projects section shows 3 featured cards and a `.more-link` ("View more projects →") to `/projects`.
- **Projects page**: `.page-head` header (back link, eyebrow, h1, lead), then `.projects-grid` (2 columns, 1 below 720px) of `.project-card`s, then the contact band and footer. Its nav uses `/#about`-style links back to home, and Projects carries `.active` + `aria-current="page"`. Cards marked `.project-placeholder` (dashed border) are bracketed slots waiting for real projects.
- **Adding a project**: add a card to `projects.html`. If it should be featured, also add it to the home list, and keep the home list at about 3 cards.
- **Shared markup**: the header nav, contact band and footer are duplicated in both pages. Change them in both places.
- **Design tokens** are CSS custom properties on `:root`. Colours are `--bg`, `--surface`, `--chip`, `--fg`, `--fg-2`, `--fg-3`, `--muted`, `--line`, `--line-strong`, `--accent`, `--accent-dark`, `--accent-soft`, `--accent-2` (cyan), `--on-accent`, the effect tokens `--glow`, `--glow-strong` and `--grid-line`, and the dark-band tokens `--dark*` (including `--dark-accent` and `--dark-grid`). Layout uses `--max-width` (1080px) and `--pad-x`. Fonts are `--font-sans`, `--font-display` and `--font-mono`. To re-theme, change `--accent`, `--accent-dark` and `--accent-soft` together (and the `--glow*`/`--grid-line` tints to match). The contact band and terminal use a lighter accent, `--dark-accent`, so they stay readable on the dark background.
- **Reusable classes**: `.wrap` (centred container), `.section` + `.two-col` + `.section-label`, `.card`, `.chip`/`.chips`, `.btn` + `.btn-primary`/`.btn-secondary`/`.btn-sm`, `.project-card`, `.exp-row`, `.page-head`, `.projects-grid`, `.more-link`, `.back-link`.
- **Breakpoints**: `900px` (skills grid drops to 2 columns) and `720px` (single column, `--pad-x: 24px`, smaller hero).
- **JS behaviour** (home page; the projects page only runs the year and reveal parts):
  1. The footer year is set from `new Date()`.
  2. An IntersectionObserver adds `.active` to the `.nav-link` for the section in view. Sections without a nav link are skipped.
  3. An IntersectionObserver adds `.visible` to `.reveal` sections for the fade/slide-in.
- **Progressive enhancement**: a head script adds `class="js"` to `<html>`. Sections are hidden only under `.js .reveal`, so the page stays readable if JS fails. `prefers-reduced-motion` turns off the animations.
- **Adding a home section**: use `<section id="x" class="section wrap reveal"><div class="two-col"><h2 class="section-label">…</h2>…</div></section>`, and add a `.nav-link` if it should appear in the nav.

## Gotchas / known issues

- The content is **placeholder**: the companies (TechCorp Solutions, etc.), project links (`href="#"`), the email `kiki@example.com`, the `555` phone number and the LinkedIn/GitHub handles. Replace them with real data only when the user provides it. Don't invent personal details.
- Smooth scrolling is pure CSS (`scroll-behavior` + `scroll-padding-top: 80px` for the sticky header). If the header's height changes, adjust `scroll-padding-top`.
- `node_modules/` is committed to the repo even though `.gitignore` lists Node exclusions. The committed copy holds express 5.x, while `package.json` asks for `^4.22.2`. Run `npm install` locally rather than relying on the committed folder, and don't stage `node_modules/` changes unless asked.

## Conventions

- CommonJS (`require`), 2-space indentation, single quotes in JS.
- Keep the backend minimal. The site is primarily static content, and README lists deployment to any Node host via `npm start`.
- Update README.md when you add routes, scripts, or sections, because it documents all three.
- New UI should reuse the existing tokens and classes rather than hard-coded colours.
