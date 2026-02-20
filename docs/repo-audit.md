# Repository Audit: Errors, Risks, and Improvement Opportunities

Date: 2026-02-20

## Blocking / High-impact issues

1. **Layout metadata props are ignored (SEO bug).**
   - `Layout.astro` accepts `title` and `description`, but renders `<MainHead />` without passing these props.
   - Result: pages that set `<Layout title="...">` do not actually emit those values in `<title>`/`<meta name="description">`.

2. **Build currently cannot be installed in this environment due dependency access failure.**
   - `npm ci` fails with `403 Forbidden` for `astro-compress@1.1.32`, so local build validation is blocked here.

3. **Potential runtime error in `404.astro` random post logic.**
   - `Math.floor(Math.random() * 1000) % (allPosts.length - 1)` can produce `NaN` when there are fewer than 2 posts.
   - It also never selects the final post because of `% (length - 1)`.

## Medium issues / code quality

4. **Unused imports and duplicate imports.**
   - `subscribe.astro` imports `Newsletter` and `Subs` from the same file but only uses one.
   - `PostCard.astro` and `PostHeader.astro` import `slugify` (unused).

5. **Dead code: `src/js/nav.js` is not wired into pages/layout.**
   - Active-link logic won’t run because script is never loaded.

6. **Likely Tailwind class typo in `nav.js`:** `color-blue-600` should be `text-blue-600`.

7. **Nested interactive elements in `PostCard.astro` (`<a>` wrapping another `<a>`).**
   - Invalid HTML and accessibility concern.

8. **Placeholder links (`href="#"`) in footer and post metadata.**
   - Poor UX and can hurt credibility/SEO quality.

9. **README is still default Astro starter text and not project-specific.**
   - New contributors won’t get correct setup/deployment/content guidance.

## Typical optimizations for a personal website (prioritized)

### Performance

1. **Modernize Astro stack/dependencies.**
   - Current package versions are old (`astro@^2.0.6`, `@astrojs/image` legacy package). Upgrade to latest Astro and image optimization approach.

2. **Improve image delivery policy.**
   - Ensure all article/hero images have responsive sizes, explicit width/height, and lazy-loading where not above the fold.

3. **Audit JS payload and remove unused scripts.**
   - Remove dead JS (`nav.js`) or properly wire minimal client script only when needed.

4. **Font loading optimization.**
   - Consider self-hosting critical fonts or adding `display=swap` (already present via Google CSS URL) plus local fallback tuning.

### SEO / Content quality

5. **Fix metadata plumbing in layout immediately.**
   - Ensure every page uses meaningful unique title + description.

6. **Add canonical URLs and Open Graph/Twitter tags in `MainHead.astro`.**
   - Improves social previews and indexing quality.

7. **Replace placeholder legal/social links and add real About/Contact context.**
   - Important trust signal for personal sites.

### Accessibility

8. **Fix nested anchors and button-inside-anchor patterns.**
   - Convert subscribe CTA to a single semantic link/button pattern.

9. **Add clearer alt text per post image (not generic “blog post image”).**
   - Better screen reader experience and image SEO.

### Maintainability

10. **Add quality scripts and CI checks.**
    - Add scripts for `astro check`, lint, and formatting; run in CI on pushes.

11. **Document project in README.**
    - Include local setup, content workflow, deployment URL, and design system notes.

## Suggested next implementation order

1. Fix Layout -> MainHead prop pass-through.
2. Fix 404 randomization logic.
3. Remove unused imports/dead JS or wire nav script correctly and fix class typo.
4. Fix nested anchors in PostCard and CTA semantics.
5. Refresh README.
6. Upgrade dependencies and re-run build/lint/check in CI.
