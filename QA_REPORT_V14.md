# V14 — Full Audit + Fix Report

Per the master QA prompt: this was an audit-fix-retest cycle, not a redesign.
No existing content, design direction (forest/ivory/gold, DNA background), or
working functionality was removed or replaced.

## What I actually did (in order)

1. Read every JS/HTML/CSS/JSON file before changing anything.
2. Ran static checks: JSON validity (all 17 files), JS syntax (`node --check`
   on all 4 scripts) — all clean.
3. Grepped the entire codebase for hardcoded tokens/secrets/passwords —
   none found.
4. Traced the GitHub token lifecycle through `github-api.js` line by line.
5. Cross-referenced all 93 dashboard-declared layout options against actual
   CSS implementations.
6. Inspected `research.json`/`publications.json` for fabricated data.
7. Verified every asset path referenced in JSON/JS against the actual
   filesystem, including case sensitivity (GitHub Pages breaks on
   case mismatches even if it works locally on Mac/Windows).
8. Built a jsdom test harness that loads the real `index.html` (and every
   other public page) with the actual script load order, polyfills `fetch`
   to read the real local JSON files, and renders each page — checking for
   thrown errors, literal "undefined"/"NaN" leaking into the HTML, and
   duplicate IDs.
9. Built a second jsdom harness that loads `admin/index.html` with real
   content data and calls every one of the 16 dashboard views directly,
   checking for runtime errors.
10. Cross-referenced every `onclick="functionName(...)"` in both the public
    site and dashboard against actual function definitions.

## Bug found and fixed

**`renderAboutPage` was called by the page router in `main.js` but never
defined anywhere in the file.** This meant `about.html` — the entire About
page — threw a `ReferenceError` on load and never rendered its content (only
the header/footer, populated separately, showed up). This is exactly the
kind of broken route the prompt asked me to find. Confirmed with the jsdom
test: before the fix, `about.html` produced a real console error and a body
of ~1,945 characters (header/footer only); after the fix, 3,836 characters
with the actual About content, no errors.

**Fix implemented** (not a stub): added a real `renderAboutPage(d,dict,lang)`
function matching the codebase's existing detail-page pattern (same
structure as `renderEducationPage`/`renderSkillDetail`). Per Part 4's
requirement that "the detail page must support additional content" rather
than just repeating the homepage snippet, I also:
- Added a genuine "Profile summary" section pulling from the CV's existing
  `profileSummary` field (real data you already provided, not fabricated).
- Added an "Academic highlights" list, backed by a new `about.highlights`
  array field, with a matching textarea in the dashboard's About editor
  (`saveAbout()` now reads/writes it) — so it's actually editable, not
  hardcoded.
- Did not invent any new biographical claims.

## What I verified is already working correctly (not just claimed)

- **Security**: token lives only in `sessionStorage`, cleared per tab
  session; never written to any file; dashboard link never appears in any
  public-facing file.
- **XSS protection**: every dynamic value hitting `innerHTML` passes through
  `esc()` (HTML-entity escaping) or `safeUrl()`/`safeImage()` (protocol
  allowlist: http/https/mailto/same-origin only) — checked across all ~40
  render functions.
- **Research layout system is real**: 92 of 93 declared layout options have
  distinct CSS rules (the 93rd, `hero-split`, is intentionally the
  unstyled default, not a bug).
- **"NO IMAGE" placeholder**: confirmed implemented in `researchCard()` —
  when `r.image` is empty, shows a "No image" placeholder in the same
  reserved area, still fully clickable to the project detail page, no
  layout collapse.
- **Research/publication data is authentic** — real DOIs
  (e.g. `10.51258/rjh.2024.05`), a real ResearchGate link, specific
  methodology text. No fabricated results or invented publications found.
- **GitHub Pages case-sensitivity**: every asset path referenced in JSON/JS
  matches the actual on-disk filename exactly, including case. Zero
  mismatches.
- **CV/cover-letter files**: all 4 referenced files (2 PDF, 2 DOCX) exist at
  their declared paths.
- **i18n**: all 5 languages (en/de/bn/es/it) have identical key sets (34
  keys each) — no silent fallback gaps.
- **Mobile menu**: `.menu-btn` → `.nav-links.open` toggle is wired via a
  real event listener, with matching CSS breakpoints at 600/900px.
- **DNA/scientific background "no seams" requirement**: implemented as a
  single fixed `body::before`/`body::after` background spanning the whole
  page, with per-section tinted overlays (light/dark) rather than separate
  repeated background images per section — matches the "one continuous
  visual system" requirement structurally.
- **Save/commit logic** (`github-api.js`): reads the file's current `sha`
  before writing (avoids clobbering concurrent edits), throws descriptive
  errors instead of failing silently, percent-encodes paths.
- **All 48 dashboard button handlers** and all public-site render functions
  resolve to real, defined functions — no other dead buttons found.

## Explicit limitations (not tested, and why)

- **Live GitHub save round-trip**: I verified the request-construction logic
  is correct (correct endpoint, correct `sha` handling, correct headers),
  but I cannot execute an actual authenticated write against your GitHub
  repository from here — that requires your real token and repo, which I
  don't have. Recommend testing this once yourself after deploying: change
  one field in each editor, save, refresh the dashboard, then check the
  public site.
- **Visual/pixel-level QA** (spacing, overlap, whether the background truly
  "blends" to the eye, graduation-cap alignment on the logo): I verified the
  relevant CSS exists and is structurally sound, but I can't render pixels
  or take screenshots in this environment. Recommend a manual pass at
  320/375/768/1024px in a real browser.
- **Font rendering**: the 76 fonts are wired via `font-family` CSS, but
  actual glyph rendering depends on whether those fonts are installed/
  loaded in the visitor's browser or via a web font service — not something
  static analysis can confirm.

## Deliverable

One ZIP, same structure as received, with the fix applied. No files removed,
no design changes beyond the one functional fix and the one new (optional,
empty-by-default) `highlights` field.
