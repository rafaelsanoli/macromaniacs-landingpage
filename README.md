# MacroManiacs — landing page

Static implementation of the Figma design
[“MM LANDING PAGE”](https://www.figma.com/design/X6U6jCz8eAsBnY9fgCxLVE/Menufy---Mobile-app-landing-page--Community-?node-id=0-1757)
(node `0:1757`).

No build step — plain HTML + CSS. Open `index.html`, or serve the folder:

```bash
npx -y serve -l 4321 .
```

## Structure

```
index.html      markup for all five sections
styles.css      design tokens + layout
assets/         favicons (32, 192, apple-touch)
assets/img/     phone renders, wordmark, store badges, quote mark
assets/icons/   the six feature icons
assets/source/  original full-size brand art (not served)
```

## Brand assets

The wordmark and favicon come from the two 6000x6000 source PNGs in
`assets/source/`, which are not referenced by the page — the served files are
optimized derivatives, regenerated with `sharp`:

| Output | From | Result |
| --- | --- | --- |
| `img/logo-macromaniacs.png` | `Logo tipog.png` | trimmed 6000x6000 → 5367x695 of actual art, scaled to 1000x129 — 1.2 MB → 15 KB |
| `favicon-32.png`, `favicon-192.png`, `apple-touch-icon.png` | `Logo.png` | 2.9 MB → 1.7 / 20.5 / 18.8 KB |

The wordmark's ratio is 7.75:1 (the previous Figma logo was 5.05:1), so it sits
lower than the old mark at the same width. Widths were kept at the design's
values (222px in the navbar and testimonial cards, 343px in the footer) to
preserve the horizontal rhythm; the footer is ~24px shorter as a result.

## Sections

| Section | Figma node | Top offset | Height |
| --- | --- | --- | --- |
| Hero 03 | `0:1758` | 0 | 866 |
| Features 12 | `0:1786` | 866 | 952 |
| Testimonial 06 | `0:1812` | 1818 | 892 |
| CTA 06 | `1:1638` | 2710 | 558 |
| Footer 06 | `1:1714` | 3268 | 293 |

Total 3561px on the 1440px canvas — matches the design exactly.

## Notes on the implementation

- **Decorative geometry is CSS, not images.** The hero's slanted purple slab is a
  `clip-path` polygon and the five diagonal bands are rotated rectangles
  (slope `52.517 / 420` → `-7.1248deg`, 64px vertical thickness). This keeps them
  crisp and lets them bleed past the viewport on screens wider than 1440px,
  which a fixed-width SVG export could not do. Each band is anchored on
  whichever edge is visible inside the canvas and runs off-screen the other way;
  `--band-r1` has both ends inside the canvas so it keeps its exact 420px width.
- **Two layers from the design are intentionally omitted** because they render
  nothing: the hero's turquoise/purple circles sit behind an opaque `#6F2EB7`
  fill, and the Testimonial section's background layer is positioned at
  `y=892` inside an 892px-tall clipped frame.
- **The logo SVG is exported with `preserveAspectRatio="none"`**, so it stretches
  to whatever box it is given. Every usage pins `aspect-ratio: 222 / 44` and
  lets height follow width.
- **Phone renders** are the frame PNG with the app screenshot layered on top.
  The screen inset is the same ratio for every device in the file
  (`4.0623% / 1.6258% / 91.8063% / 96.7194%`), so one `.device__screen` rule
  covers the hero and both CTA phones.
- **`--screen-chin` is the one deliberate deviation from the design.** The app
  screenshots were exported cropped tight at the bottom: the nav bar sits flush
  against the image edge with no safe-area strip, so the phone reads as cut off.
  Figma renders it the same way — a pixel diff against Figma's own export of
  node `103:8` showed only symmetric edge antialiasing. `--screen-chin` trims
  0.75% off the screen height so a little more bezel shows. Set it to `0%` for
  geometry identical to Figma, or drop in an uncropped screenshot and reset it.
- **Assets are committed locally.** Figma's asset URLs expire about 7 days after
  export, so every image was downloaded rather than hot-linked.

## Responsive

The design is a 1440px desktop canvas; below that the fixed compositions unwind:

- **≤1280px** — tighter gutters, smaller hero phone.
- **≤1024px** — hero stacks (copy, then phone), diagonal bands are dropped and
  the slab becomes a flat fill, the absolutely-positioned testimonial cards
  become a single column, the CTA stacks, features drop to 2 columns.
- **≤700px** — nav links hidden, one feature per row, type scales down.
