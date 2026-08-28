# Portfolio v6 — Dashboard & Background Fixes

This build fixes two reported issues:

1. Dashboard mutation controls now work under the dashboard CSP. Inline event handlers are permitted only through `script-src-attr`; executable scripts remain restricted to same-origin files.
2. Dashboard sections use an explicit **Edit Section → Save Changes** workflow. Existing section-specific save actions remain available and the section-level Save Changes button triggers them sequentially.
3. Media Library actions are restored: Copy path, Set portfolio background, Set dashboard background, and Use for Home image.
4. The scientific DNA + 3D chemical-structure artwork is rendered as one fixed, continuous portfolio canvas rather than a separate background image per section. This removes visible seams between sections while preserving the same supplied artwork.
5. The graduation-cap mark is positioned immediately above the `i` dot in the header brand treatment.

The GitHub token is still kept in sessionStorage only; no token is written into repository files.
