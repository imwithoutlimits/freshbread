FreshBread Publishing — Website

The marketing website for FreshBread Publishing: a static site showcasing the company's WordPress plugin suite, publishing work, and consulting services.

Live site: add your GitHub Pages / custom domain URL here once deployed

What this is

A hand-built, dependency-free static website — plain HTML, CSS, and vanilla JavaScript. No framework, no build step, no npm install. You can open index.html directly in a browser and the whole site works.

It's intentionally static (not WordPress, not a database-backed app) because this site's content changes rarely and doesn't need user accounts, logins, or a CMS. Static hosting is free, fast, and close to impossible to hack, since there's no server-side code running. The company's actual products (the plugins) are WordPress plugins — this site just markets them.

Site structure
.
├── index.html              Home page
├── products.html            Full plugin suite grid
├── about.html                Company/about page
├── contact.html              Contact page (Netlify Forms — see below)
├── thank-you.html            Contact form success page
├── privacy.html               Privacy Policy
├── terms.html                  Terms of Service
├── refund.html                 Refund Policy
├── plugins/
│   ├── vaultmind.html
│   ├── linkhealer.html
│   ├── knosearch.html
│   ├── eventmind.html
│   ├── documind.html
│   ├── recipemind.html
│   ├── affiliatemind.html
│   ├── tablemind.html
│   ├── trustmind.html
│   ├── couponmind.html
│   └── visiontag.html
└── assets/
    ├── style.css            All site styling — design tokens are at the top of the file
    └── main.js               Mobile nav toggle + the node-network canvas animation in the hero

Every page shares the same nav, footer, and assets/style.css / assets/main.js, so a change to those two files affects the whole site at once.

Design system

Defined as CSS custom properties at the top of assets/style.css:

Token	Value	Use
--ink	
#0A0A0A	Primary text, dark sections
--paper	
#FFFFFF	Primary background
--fog	
#F0F0EC	Secondary band background
--signal	
#FF4B2B	Primary accent (buttons, links, highlights)
--live	
#2FBE73	Status/"active" indicator dots

Type: Space Grotesk (headings), Inter (body text), IBM Plex Mono (nav links, tags, prices, eyebrows) — all loaded from Google Fonts.

The contact form

contact.html uses Netlify Forms as its backend, even when the site itself isn't hosted on Netlify (e.g. GitHub Pages).

How it works:

A Netlify deployment of this same site has the <form data-netlify="true"> in its HTML, which is how Netlify's build bot registers the form's fields.
When a visitor submits the form (from wherever the site is actually hosted), JavaScript in contact.html sends the form data via fetch() to that Netlify deployment's URL in the background.
The visitor is then redirected to thank-you.html on the domain they're already on — they never see the Netlify domain.
Submissions appear in the Netlify site's dashboard under Forms.

Important: if you fork/rename/redeploy the Netlify site under a different URL, update NETLIFY_FORM_ENDPOINT in contact.html (search for it near the bottom of the file) and the form's action attribute to match.

Updating content

There's no CMS — edit the HTML files directly.

Add a new plugin: copy an existing file in /plugins/ as a template, update its content, then add a matching card to the grid in index.html and products.html, and a link in the footer's Products list (which is repeated at the bottom of every page).
Change site-wide styling: edit assets/style.css. Colors, fonts, and spacing are all driven by the CSS variables at the top.
Change the hero animation: assets/main.js, look for the nodeField canvas logic.

If you're comfortable with basic scripting, this site was originally generated from a small Python script that assembled all pages from shared templates — regenerating it that way is far less error-prone than hand-editing 20 HTML files when adding several plugins at once.

Deployment

This repo is deployed in two places simultaneously:

GitHub Pages — the live, public-facing site.
Netlify — exists solely to power the contact form backend (see above). It can also serve as a second live mirror of the site if you want a Netlify URL as a backup.

Both are just this same folder, deployed as static files — no build command, no environment variables, no server config needed on either platform.

See the deployment guide provided alongside this repo for the full step-by-step (zip → GitHub → live → custom domain → Netlify), or:

GitHub Pages docs: https://docs.github.com/en/pages
Netlify Forms docs: https://docs.netlify.com/forms/setup/
License

Private/proprietary — © FreshBread Publishing. Not licensed for reuse.
