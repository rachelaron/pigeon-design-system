# pigeon-design-system

Shared "Pigeon" design tokens, base CSS, and Astro components. Originated
on rachel-aron.com (see that site's `/style-guide/` for the full visual
reference); extracted here so every Astro site under `~/Sites/` can build
on the same base instead of re-implementing it.

This is a local package, not published to npm. Each consuming site depends
on it via a `file:` reference, so edits here take effect the next time that
site runs `npm install` (or immediately in dev, since npm symlinks `file:`
deps into `node_modules`).

## Use in a site

```json
// package.json
"dependencies": {
  "pigeon-design-system": "file:../pigeon-design-system"
}
```

(Adjust the relative path to wherever the site actually lives relative to
this package — e.g. `file:../../pigeon-design-system` if the site's
`package.json` is nested one level deeper, like `astro-site/package.json`.)

Then `npm install`, and import from the package's subpaths:

```astro
---
import 'pigeon-design-system/tokens.css';
import 'pigeon-design-system/global.css';
import PillTag from 'pigeon-design-system/components/PillTag.astro';
---
```

## What's here

- **`tokens.css`** — color, type scale, radius, spacing, shadow, motion.
  Every component below is built on these `var(--token-name)` values, not
  hardcoded ones.
- **`global.css`** — reset + the shared `.pg-btn` / `.pg-card` / `.pg-tag`
  base rules and the two shared responsive breakpoints (`.pg-stack`,
  `.pg-hero-h1`, `.pg-page-h1`).
- **`layouts/Layout.astro`** — the page shell: fonts (Space Mono + Work
  Sans), `<Nav>` + `<Footer>`, `.pg-shell` padding. Takes `title`,
  `siteName`, `navItems`, and optional `footerOwner` / `footerLinks` /
  `homeHref`. Most sites should wrap this in their own thin
  `src/layouts/Layout.astro` that hardcodes that site's nav/footer/brand,
  so pages can keep calling `<Layout title="…">` — see
  `portfolio20260901/astro-site/src/layouts/Layout.astro` for the pattern.
- **`components/Nav.astro`**, **`components/Footer.astro`** — used by
  `Layout.astro`; take `siteName`/`items` and `owner`/`links` respectively.
  Only used directly if you're not using the shared `Layout`.
- **`components/PillTag.astro`** — `variant: 'ink' | 'accent' | 'outline'`.
- **`components/MetaRow.astro`** — `items: { label, value }[]`, rendered as
  a row of stat tiles.
- **`components/PigeonFootprints.astro`** — decorative footprint-trail SVG
  divider, the Pigeon brand motif. `id` required (unique per instance),
  `width`/`margin` optional.
- **`components/TableOfContents.astro`** — jump-nav built from an Astro
  content-collection `headings` array (`{ depth, slug, text }[]`).
- **`components/CaseStudyCard.astro`** — the two-tone "sticker" preview
  card (thick accent border + flat lilac shadow). Generic despite the
  name — works for any linked content card with a headline/impact/image/
  tags shape.
- **`components/ContactForm.astro`** — `action: string` (required) is the
  only prop; every site's form backend differs, so this never assumes one.

## What's deliberately not here

Anything that's really page *content* wearing a component's clothes —
e.g. rachel-aron.com's `SkillTabs.astro`, which hardcodes Rae's own
design/dev/biz bio copy. That stays local to the site it belongs to.
When adding a new shared component, ask: would a second site actually
reuse this with different props, or is it one site's content in disguise?

## Adding a new consuming site

1. Add the `file:` dependency to that site's `package.json`, `npm install`.
2. Add the Google Fonts link + `Layout.astro` wrapper pattern (copy
   `portfolio20260901/astro-site/src/layouts/Layout.astro` as a starting
   point, swap in that site's own nav items / site name / footer links).
3. Pull in whichever `components/*` fit; skip the ones that don't apply.
4. If a page needs something not covered here, build it locally first —
   only promote it into this package once a second site actually wants it
   too, so the shared surface stays real reuse, not speculative API.
