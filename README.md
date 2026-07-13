# SH Fitness Tools — combined site

This is your uploaded pages combined into one working static site. Here's exactly what changed.

## What was wrong with the uploaded files

Every page (`about.html`, `contact.html`, the two `index (1)/(2).html` files, the 5 articles, etc.) was a complete, self-contained HTML file with its own full copy of the site's ~520-line CSS design system and ~75-line behavior script pasted inline. That's normal for one-off page generation, but as a set it meant:

- The same CSS/JS was duplicated 14 times (~650KB total for 14 pages)
- `index (1).html` and `index (2).html` were actually the **Calculators directory** and **Blog** pages, not your homepage — there was no `/` page at all
- The header's search icon and mobile hamburger menu had buttons and CSS for the closed state, but no code to ever open them — so on every page except the calculator directory, they didn't do anything
- The `manifest.json` referenced `icon-192.png` / `icon-512.png` that didn't exist, and `favicon.svg` didn't exist either

## What this build does

**Deduplicated the design system.** All shared CSS now lives in `/assets/css/base.css` (loaded by every page), with two small additive files — `directory.css` and `blog.css` — for the two page types that need a bit extra. All shared behavior (theme toggle, scroll reveal, accordion, the store promo modal) lives in `/assets/js/main.js`. Page-specific logic (the calculator directory's search/filter, the blog directory's filter, the contact form, the 404 search box, the newsletter signup) is each its own small file. Nothing in the CSS or copy was redesigned — this is your content and styling, just organized so it ships once instead of 14 times.

**Built the missing homepage** (`index.html`), using only classes and colours that already existed in your design system (the hero illustration reuses the `.donut-*` / `.comp-card` / `.float-chip` styles that were defined in the CSS but never actually used anywhere). It links out to your real categories, featured calculators, and the 5 real articles.

**Fixed the two dead buttons** in the header, site-wide:
- The hamburger now opens a real mobile dropdown (new CSS is clearly marked as additive at the bottom of `base.css`, nothing existing was touched)
- The search icon now behaves contextually: on the calculators directory or the 404 page it focuses the search box already on that page; everywhere else it sends the visitor to `/calculators/` to search from there

**Added the missing assets**: `favicon.svg` and `icon-192.png` / `icon-512.png` (generated to match your teal/orange brand mark) so `manifest.json` no longer 404s on those.

**Added the 5 article URLs** to `sitemap.xml` — the original only listed the calculator and top-level pages.

## Folder structure

```
/
├── index.html                 NEW — homepage
├── about.html
├── contact.html
├── disclaimer.html
├── privacy-policy.html
├── terms.html
├── 404.html
├── manifest.json
├── robots.txt
├── sitemap.xml
├── /assets
│   ├── /css
│   │   ├── base.css            Shared by every page
│   │   ├── directory.css       Extra: calculators directory only
│   │   └── blog.css             Extra: blog index + articles only
│   ├── /js
│   │   ├── main.js              Shared by every page (theme, reveal, accordion, modal, nav, search)
│   │   ├── calculator-directory.js   Calculators directory search/filter
│   │   ├── blog-directory.js    Blog index filter
│   │   ├── blog.js              Newsletter signup (blog index + articles)
│   │   ├── contact.js           Contact form → mailto handler
│   │   └── notfound.js          404 page search box
│   └── /icons
│       ├── favicon.svg
│       ├── icon-192.png
│       └── icon-512.png
├── /calculators
│   ├── index.html               Calculator directory (search + filter, 16 tools listed)
│   └── categories.html
└── /blog
    ├── index.html                Blog directory (filter by category)
    ├── how-to-count-macros.html
    ├── best-macro-split-for-your-goal.html
    ├── body-recomposition-explained.html
    ├── tracking-progress-beyond-the-scale.html
    └── natural-muscle-growth-limits.html
```

## Update: all 16 calculators are now built

Every calculator page linked from the directory, categories page, footer and homepage now exists and works:

**Nutrition** — Macro, Protein Intake, Water Intake, Creatine Dosage
**Body composition** — Body Fat % (US Navy method), FFMI, Ideal Weight (Hamwi/Devine/Robinson), Lean Body Mass (Boer formula)
**Strength** — One Rep Max (Epley/Brzycki), Weight Plate, Wilks/DOTS
**Cardio & activity** — Heart Rate Zones, Running Pace (Riegel predictions), VO2 Max (Cooper test), Calories Burned (MET-based), Steps to Distance

Each page follows the same structure: hero → calculator card + live result panel → explanation cards → formula breakdown → FAQ → related calculators → related articles → CTA — all built from the `.calc-layout`, `.result-panel`, `.pie-svg-wrap`, `.range-bar`, `.formula-block` etc. classes that were already in `base.css` but unused. No design-system CSS was changed to build these; two small calculator-only stylesheets already existed for the directory/blog pages, and this batch didn't need any more.

**New shared files:**
- `/assets/js/calc-common.js` — unit conversion, validation, formatting, and result-panel helpers shared by all 16 calculators
- `/assets/js/calc/<slug>.js` — one small file per calculator with its own formulas and DOM wiring

**Every formula was checked**, either by hand-verifying the arithmetic or against multiple independent published sources — this caught one real bug during testing: the DOTS coefficients I used initially were wrong (mixed up polynomial degree), giving scores roughly half of what they should be. That's fixed and verified against known reference values (e.g. 80kg male / 430kg total → 296.5 DOTS, matching published calculators exactly).

All 16 pages were tested end-to-end in a real browser (fill form → submit → verify results render, for every calculator), plus every `getElementById` call in every calculator's JS was cross-checked against its HTML to catch typos. Zero broken internal links and zero missing assets across all 31 pages on the site.

## Local testing

Every page uses root-absolute paths (`/assets/...`, `/calculators/...`), so serve the folder rather than opening files directly:

```bash
npx serve .
# or
python3 -m http.server
```

This works with no changes on GitHub Pages (user/organization site or a custom domain). If you deploy to a GitHub *project* Pages site (`username.github.io/repo-name/`), point a custom domain at it, or the absolute paths will need a `repo-name/` prefix.
