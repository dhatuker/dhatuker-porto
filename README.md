# Self-Project

A white-and-blue, tech-styled portfolio website (home page + projects page) served by Express.js.

## Design

"Mission Control, Light", a satellite-themed take on a clean light layout:
- Cool grey background (`#E9ECF1`) with a faint 48px grid, white rounded modules and one orange accent (`#FF5B2E`)
- IBM Plex Sans for text, IBM Plex Mono for labels, tags and navigation (Google Fonts)
- Sticky, blurred navigation with a `DHATU//SYSDEV` brand
- Hero with a decorative orbit graphic and a role/org/since/base readout card
- Sections labelled like modules: 01 About, 02 Stack, 03 Missions, 04 Log, 05 Comms
- Experience shown as a timeline log; contact as a full orange panel
- Responsive, works without JavaScript, respects `prefers-reduced-motion`

## Project Structure

```
self-project/
├── index.js           # Express.js server
├── package.json       # Project configuration
├── package-lock.json  # Dependency lock file
├── .gitignore         # Node.js exclusions
├── README.md          # Documentation
├── CLAUDE.md          # Notes for Claude Code
└── public/
    ├── index.html     # Home page
    ├── projects.html  # Projects page (/projects)
    └── styles.css     # Shared styles
```

## Features

### Sections
1. **Hero** - Name, role and call-to-action buttons
2. **About** - Personal introduction
3. **Skills** - Grouped into Frontend, Backend and Tools cards
4. **Projects** - Three featured project cards with a "View more projects" link to the Projects page
5. **Experience** - Timeline rows with period, role and company
6. **Contact** - Email, phone, location and social links

### Technical Features
- Express.js server serving static files
- API endpoints: `/api` and `/health`
- Smooth scrolling navigation
- Active section highlighting
- IntersectionObserver-based animations
- Mobile-responsive navigation
- Works without JavaScript (animations are progressive enhancement)
- Respects `prefers-reduced-motion`
- Optimized for performance

## Installation

```bash
cd self-project
npm install
```

## Available Scripts

- `npm start` - Start the server
- Visit `http://localhost:3000` to view the portfolio

## API Endpoints

- `GET /` - Serves the home page
- `GET /projects` - Serves the projects page
- `GET /api` - Returns API information
- `GET /health` - Health check endpoint

## Customization

To personalize the portfolio:
1. Edit the content in `public/index.html` and `public/projects.html`
2. Update skills, experience, projects, and contact information
3. Modify colors in the CSS variables on `:root` in `public/styles.css` (change `--accent`, `--accent-dark` and `--accent-soft` together)
4. Add your own projects and experiences

## Deployment

The Express.js server can be deployed to any Node.js hosting platform:
- Heroku
- Vercel (with Node.js server)
- AWS Elastic Beanstalk
- DigitalOcean App Platform
- Traditional VPS with Node.js

Simply run `npm start` to serve the portfolio on port 3000 (or PORT environment variable).