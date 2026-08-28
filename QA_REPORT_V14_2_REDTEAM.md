# V14.2 — Red-Team Forensic Audit Report

Per the golden rule: nothing below is marked PASS without an actual
executed test. Where something genuinely couldn't be tested in this
non-browser environment, it's marked as such — not assumed to pass.

## Scope: what was new in this ZIP

This upload carries forward the `renderAboutPage` fix from the V14 audit
(verified present, not duplicated) plus a rewritten background system
(`css/style.css`: 988 → 1,002 rules, 14 new/changed). All JS and HTML files
were byte-identical to the already-audited V14, so this pass focused fresh
effort on (a) adversarial/red-team testing that hadn't been run before, and
(b) the new background CSS specifically.

## Bugs found and fixed this pass

**1. Broken/404 research images showed the browser's native broken-image
icon instead of the "No image" placeholder.** The existing code only
handled the case where `r.image` was empty from the start — not the case
where an image path exists but the file is missing or fails to load (a
distinct test case the prompt explicitly calls out: "C. deleted image,
D. broken image" vs. "B. no image"). Fixed by adding an `onerror` handler
to research card images, hero portrait, and both About page portraits, so
a failed image load now degrades to the same professional placeholder
treatment, and the card never collapses or shows a broken-image glyph.

**2. The new single-image background had no mobile override for
`background-attachment: fixed`.** This is a known WebKit/iOS Safari
limitation — fixed backgrounds render inconsistently on phones. The
codebase already had this exact protection for the *old* background rule
elsewhere in the file; the *new* `.scientific-global-bg` rule was missing
the equivalent. Added `@media(max-width:900px){background-attachment:scroll}`
to match the existing pattern.

## A finding I initially got wrong — corrected before reporting

I first tried to dynamically verify the broken-image fix by firing a
synthetic `error` event on an `<img>` in a jsdom sandbox, and it appeared to
work. On a second, more careful pass, I discovered that was a false
positive: jsdom's `runScripts: 'outside-only'` mode does **not** compile
inline HTML event-handler attributes (`onerror="..."`) into live functions
— I confirmed this with an isolated minimal repro (`typeof img.onerror`
returns `"object"`, not `"function"`, in this mode). My earlier "confirmed
working" result was a bug in my own test script, not a real verification.
I'm flagging this explicitly per the prompt's golden rule rather than
quietly correcting it — the fallback logic is standard HTML that any real
browser executes reliably, but I could not dynamically fire it in this
sandboxed, non-browser environment, so it's marked "not testable here"
below rather than PASS.

## Red-team tests actually executed

- **XSS injection**: built research/publication entries with `<script>`,
  `<img onerror>`, and `<svg onload>` payloads in title/description/category
  fields, rendered them through the real pipeline, and checked
  `document.querySelectorAll('script').length` (stayed at 2 — only the
  legitimate `data.js`/`main.js` tags) and whether any injected flag
  variable executed. **0 for 4 payloads fired.**
- **`javascript:` URL injection**: set a project's `externalUrl`/`pdfUrl` to
  `javascript:alert(...)`. Confirmed `safeUrl()` rejected both and rendered
  `#` instead of the malicious scheme.
- **Sparse/malformed data**: rendered a research project object with only
  `{slug, title}` — no image, category, description, tags, methods, results.
  No crash, no literal "undefined" leaking into the page.
- **Extreme-length data**: a title/description/tag list 40–80x normal
  length. Rendered without throwing; relies on the new CSS wrap rules
  (`word-break`/`overflow-wrap` additions already present in the file) to
  avoid layout breakage — confirmed the rules exist and target the right
  selectors, could not visually confirm pixel-level wrapping without a
  browser.
- **Full page load test**: all 11 public page/route combinations + all 16
  dashboard views executed against real production data with the real
  script load order — 0 runtime errors, 0 stray "undefined" text.

## QA Table

| Feature | Test performed | Result |
|---|---|---|
| About page renders | Load about.html with real data | PASS — real content, 0 errors |
| Research card, has image | Render with real image | PASS |
| Research card, no image | Render with empty `image` field | PASS — shows "No image", stays clickable |
| Research card, broken image path | Render + inspect onerror handler code path | **Code correct, dynamic firing NOT TESTABLE in this sandboxed environment** — standard HTML behavior, needs a real-browser check |
| Research detail, sparse data | Render project with only slug+title | PASS — no crash, no "undefined" |
| Research detail, extreme text | Render 80x-length description/tags | PASS — no crash; visual wrap not confirmable here |
| XSS via research/publication fields | Inject 4 different script/event payloads | PASS — 0 executed |
| `javascript:` URL injection | Malicious externalUrl/pdfUrl | PASS — sanitized to `#` |
| GitHub token exposure | grep entire codebase | PASS — none found; sessionStorage only |
| Dashboard link in public nav | grep all public HTML/JS | PASS — not present |
| All public pages load | 11 real routes, real data | PASS — 0 errors |
| All dashboard views render | 16 views, real data | PASS — 0 errors |
| Asset path case-sensitivity | Cross-check every referenced path vs. filesystem | PASS — 0 mismatches |
| JSON validity | Parse all 17 data files | PASS |
| JS syntax | `node --check` all 4 scripts | PASS |
| i18n completeness | Compare key sets across 5 languages | PASS — 34/34 keys each |
| CV/cover letter files exist | Check all 4 referenced paths | PASS |
| Research layout switching | Cross-check 93 declared options vs. CSS | PASS — 92 implemented, 1 is the unstyled default (not a bug) |
| Background — single continuous image | Read new CSS architecture | PASS — one `body`-level image, all per-section pseudo-layers explicitly disabled, cannot produce a seam by construction |
| Background — mobile fixed-attachment quirk | Check for iOS Safari override | **Found missing, fixed this pass** |
| Live GitHub save round-trip | — | **NOT TESTABLE** — requires your real token/repo |
| Visual/pixel rendering, all breakpoints | — | **NOT TESTABLE** — this environment cannot render pixels or screenshot |
| Graduation cap position, header scroll transition (visual) | — | **NOT TESTABLE** — same reason; CSS rules for both exist and target the right elements, unchanged from V14 |

## Explicit remaining limitations (not silently passed)

- Anything requiring actual pixel rendering (visual seam-check by eye,
  cap alignment, spacing at each breakpoint) cannot be done in this
  text/DOM-only environment. The CSS logic is verified correct; a real
  browser check is the only way to close this out.
- Live GitHub API write/commit flow needs your real token and repo.
- The broken-image `onerror` fallback is standard, widely-supported HTML
  and matches the exact pattern already proven for the "no image" case —
  but I'm not going to claim I dynamically verified it when my only attempt
  to do so turned out to be invalid.

## Deliverable

One ZIP. No content removed, no design direction changed beyond the two
fixes above (both were genuine bugs, not design changes).
