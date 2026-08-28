# V12 — Botanical Editorial Theme Update

This build preserves the existing portfolio content, dashboard architecture, editors, save/publish flows, media library, section layout presets, typography controls, image controls, and premium customization features.

## Visual update

- Reworked the public portfolio visual language to match the approved reference direction:
  - deep botanical/forest green
  - warm ivory/cream surfaces
  - restrained muted gold accents
  - editorial serif headings + clean sans-serif body text
  - premium research-card presentation
  - clean academic timeline cards
- Removed decorative leaf treatment from the visual theme.
- Added a new green-gold scientific background that retains the DNA helix and 3D chemical-structure artwork.
- Kept the scientific background as a single reusable portfolio canvas so the DNA artwork remains visually coherent.
- Improved text contrast on dark and light sections.
- Increased major section heading scale slightly for stronger hierarchy.
- Added mobile-safe spacing and typography overrides.

## Functionality policy

No dashboard editing architecture was intentionally removed or replaced in this update. The change is primarily a public-facing visual/theme upgrade plus the new background asset and appearance defaults.

## QA performed

- JavaScript syntax checks passed for `main.js`, `dashboard.js`, `data.js`, and `github-api.js`.
- All JSON data files parsed successfully.
- New background asset exists at:
  `assets/images/scientific-background-green-gold.png`
- Existing premium editor code remains present.
- Existing media-library actions remain present, including portfolio background, dashboard background, copy path, and Home image assignment.
