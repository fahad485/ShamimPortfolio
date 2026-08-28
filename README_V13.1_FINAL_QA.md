# V13.1 Final QA / Polish

## Visual fixes
- Forest-green / warm-ivory / muted-gold visual system preserved.
- Scientific DNA + molecular artwork remains the portfolio-wide background.
- Global background mode no longer paints a second copy of the background inside every section, preventing visible seams between sections.
- Scrolled header remains a translucent forest-glass header with high-contrast text instead of switching to a disconnected white bar.
- Research cards always reserve a consistent image area. Missing images display a clickable `No image` placeholder that opens the project detail page.

## Structure fixes
- Dedicated Research, Publications, Skills and Experience pages now expose their section IDs so dashboard layout presets apply to the public pages as well as the homepage.
- Dedicated Education page added. Homepage education cards show only concise degree information and link to full academic detail pages.
- Education dashboard editor now supports short description, detailed academic overview, thesis/research focus, academic highlights, academic areas/coursework and institution website.

## Dashboard/save fixes
- Existing Edit Section -> Save Changes workflow preserved.
- Skills top-level Save Changes now also captures directly edited category names.
- Existing project/publication/education/experience modal saves continue to write to the corresponding JSON data file.
- Portfolio appearance, section layouts, item presentation, buttons and background controls continue to use the JSON-driven system.

## Automated checks performed in this build
- All JavaScript files pass Node syntax checks.
- All JSON data files parse successfully.
- All local HTML/CSS/JS/image/file references found in HTML were checked; no missing local references were found.
- Dashboard inline `onclick` handlers were checked against defined functions; no undefined handler names were found.
- Research image placeholder/link behavior was verified by code inspection.
- GitHub API write/read/delete code paths were inspected; live GitHub publishing cannot be exercised offline without the user's repository token/session.
