# V7 Continuous Scientific Background Fix

This build changes the portfolio background architecture to use one page-level scientific
background layer instead of repainting the DNA/chemical-structure image independently on
each section.

Key fixes:
- Continuous portfolio-wide background layer.
- No section-level background image copies.
- Default `fixed + no-repeat + cover` rendering.
- Dashboard controls for scope, attachment, repeat, fit, position, desktop/mobile scale,
  image opacity/overlay, and gradient.
- Media Library "Set portfolio background" now selects the continuous global mode.
- Light/dark sections use translucent surfaces so the scientific background remains visible
  without a visible seam.
- Responsive mobile background handling.
- Header keeps the dark top state and transitions to a translucent light state on scroll.
- Graduation-cap mark is positioned directly over the `i` dot area in the brand.

The dashboard still stores the GitHub token in sessionStorage only; portfolio files do not
contain the token.
