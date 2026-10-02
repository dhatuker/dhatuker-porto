# Self-Project

A white-and-blue, tech-styled portfolio website (home page + projects page) served by Express.js.

## Design

The layout comes from the "Portfolio Landing Page" design, re-themed with a white/blue tech look:
- White and blue-tinted neutral background with a blue accent (`#1F6FEB`) and a cyan secondary (`#00A8CC`)
- Faint blueprint grid and soft blue glow behind the page
- Space Grotesk headings, Work Sans body, JetBrains Mono for labels, chips and code-style details (Google Fonts)
- Sticky, blurred navigation bar with a `~/Dhatu` brand and blinking cursor
- Terminal-style card in the home hero
- Two-column sections: a small mono `//` label on the left, content on the right
- White cards with hairline blue borders and a glowing hover state
- Dark navy contact band and footer with a grid pattern
- Responsive, with breakpoints at 900px and 720px

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