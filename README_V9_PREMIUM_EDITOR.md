# Fahad Badsha Shamim Portfolio — V9 Premium Editor

## Final editor features
- Section lock → Edit Section → Save Changes workflow.
- GitHub-backed saves for portfolio content and appearance.
- 15 structure presets for Home, About, Research, Publications, Skills, Education, Experience and Contact.
- 30 premium browser-safe fonts.
- Section typography, colors, hover, links, paragraph imagery, background image, opacity, blur, brightness, contrast, saturation, overlay, radius and shadow controls.
- 50 profile-image frames: 30 creative/luxury + 20 formal/academic.
- Portrait position, zoom, brightness, contrast, saturation, rotation and motion controls.
- 20 Home hero-title animation presets.
- 15 header styles + 15 header motion presets.
- Continuous portfolio scientific background to prevent section-to-section image seams.
- Media Library actions for portfolio background, dashboard background, Home image and path copying.
- Separate About page (`about.html`).
- Dashboard remains hidden from public navigation.
- CV Manager and existing research/publication/skill detail workflows retained.
- Responsive/mobile-safe layout and reduced-motion fallback.

## Security model
- GitHub fine-grained token is kept in `sessionStorage` only.
- Repository owner/name/branch are kept separately in `localStorage`.
- Token is never written to portfolio JSON files.
- Dashboard password uses salted PBKDF2 verification.
- Use a token limited to this repository with Contents read/write only, and revoke/expire it when finished.
