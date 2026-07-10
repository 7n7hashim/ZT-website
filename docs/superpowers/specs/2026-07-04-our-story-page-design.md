# Our Story page — design spec

**Date:** 2026-07-04
**Status:** Approved by user, proceeding to implementation
**Note:** This project has no git repository, so this spec is saved to disk but not committed.

## Goal

A new, standalone `our-story.html` — a cinematic, editorial scroll narrative for Zaras
Travel's origin story. Not a traditional "About" page: each section assembles itself
as the visitor scrolls. Built as a brand new page; `index.html` and `hero-v2.html` are
not modified.

## Origin

The user supplied a 21st.dev implementation prompt built for React/Next.js/shadcn/
TypeScript/Framer Motion (a `VerticalCutReveal` word-mask text effect and a
`TimelineContent` shared-ref staggered reveal wrapper). This codebase is plain
HTML/CSS/JS with a Python static server — no npm, bundler, or git. Decision (user
confirmed): port the *visual behavior* of both techniques to vanilla GSAP +
ScrollTrigger rather than introduce a React toolchain for one page.

## Architecture

- **File:** `our-story.html`, project root. Self-contained like `hero-v2.html`:
  inline Tailwind CDN config, inline `<style>`, GSAP + ScrollTrigger via CDN
  (`gsap@3.12.5`, matching the version already used in `hero-v2.html`).
- **Design tokens:** reuses `hero-v2.html`'s system exactly — Cormorant Garamond
  (display) + Inter (body); `--cream #f5f0e8`, `--paper #fcfaf6`, `--umber #2e1a0e`,
  `--amber #e87722`, `--maroon #7a1a1a`.
- **Nav:** fixed, light-themed (paper background, umber text, hairline border) —
  this page has no dark hero photo to float a transparent nav over. Same link set
  and styling language as `hero-v2.html`'s nav; "Our Story" marked current. Wordmark
  links back to `hero-v2.html` so the page isn't a dead end.
- **Footer:** the dark charcoal (`#191009`) footer already built for `hero-v2.html`,
  reused verbatim.
- **No React/Next.js/TypeScript/shadcn/Framer Motion.** GSAP + ScrollTrigger only.

## Reusable animation primitives

1. **`revealOnScroll(container, opts)`** — one shared JS helper (replaces
   `TimelineContent`). Fades + lifts (+ optional blur) each matched child in
   sequence with a stagger, fired once via `ScrollTrigger` (`start: 'top 80%'`)
   when the container enters the viewport. Every section calls this instead of
   bespoke per-section reveal code.
2. **Vertical cut reveal** (replaces `VerticalCutReveal`) — headings are wrapped
   word-by-word in `overflow:hidden` spans; GSAP animates each from `y:100%` to
   `0` with a small stagger, refined spring-like easing, fired once on scroll-in.
   Subtle only — no dramatic overshoot.
3. **"Arrival Reveal"** (signature device, replaces the reference's SVG
   clip-path blob) — every photo on the page enters at 102% scale, softly
   blurred, and inset-clipped by a few percent; eases to full scale / full clarity
   / full frame as it scrolls into view. One consistent treatment used everywhere
   an image appears, rather than a different one-off trick per section.
4. **Itinerary spine** — a thin amber vertical line down the left margin with a
   waypoint dot per section, drawn in proportionally to overall page scroll
   progress (not pinned). Ties the whole page together as "one journey," visually
   distinct from Services' branching horizontal routes.
5. **Count-up** — plain GSAP tween of a numeric proxy value, `onUpdate` writes
   integer-snapped text. Runs once, only while the "14+ Years" section is active.

## Interaction approach (Hybrid — approved)

- Sections 1 (Introduction), 2 (The Beginning), 4 (Our Mission): normal document
  flow, calm one-shot reveal-on-scroll (fade + rise + slight scale 98%→100%). Never
  pinned — this is where the reading happens, so nothing should feel rushed.
- Section 3 (14+ Years): short pinned moment (mirrors the Services section's pin
  trick, used sparingly/once) — page holds briefly while a giant serif numeral
  counts 0→14, a trailing "+" fades in, a thin rule draws under it, caption settles
  in, then the pin releases and normal scrolling resumes.

## Content

### Section 1 — Introduction
Heading "Our Story" (vertical cut reveal). Centered supporting copy:

> Zaras Travel was built on a simple belief: that travel planning should feel as
> good as the trip itself. What began as one woman's expertise in aviation and
> hospitality has grown into a trusted travel partner for individuals, families,
> and companies across Kenya and beyond — grounded in experience, built on trust,
> and shaped around every client we serve.

### Section 2 — The Beginning
Two-column: `brand assets/ourstoryphoto.jpeg` (Arrival Reveal treatment; plane-window
view of a tropical island — no founder portrait exists, user confirmed staying
photo-less/editorial) paired with founder copy. Eyebrow "The Beginning":

> Before Zaras Travel existed, its founder, Fatma Mohamed, spent years working
> within Kenya Airways — learning the realities of aviation, the discipline of
> international logistics, and what it truly takes to make a traveler feel taken
> care of from departure to arrival.
>
> That experience shaped a clear conviction: travel planning is at its best when
> it's personal — not a call center, not a form, but a relationship with someone
> who understands the details that matter.
>
> Zaras Travel was founded to bring that same standard of care to every client,
> whether it's a family safari, a corporate itinerary, or a once-in-a-lifetime
> holiday.

### Section 3 — 14+ Years (pinned moment)
Full-height, centered. "14+" counts up from 0, thin rule draws under it, caption
settles beneath: "YEARS OF EXPERIENCE" — *"Over a decade of dependable itineraries
and clients who return every time they travel."*

### Section 4 — Our Mission
Heading "Our Mission" + short intro line, then four pillars (a genuine enumerated
list of distinct commitments, so numbering is meaningful here):

1. **Effortless by Design** — planning that removes friction instead of adding steps.
2. **Personal, Down to the Detail** — every itinerary shaped around the traveler,
   not a template.
3. **Corporate & Leisure, One Standard** — the same rigor for a boardroom trip as
   a family holiday.
4. **Relationships Over Transactions** — success measured by clients who call for
   the next trip too.

## Responsive behavior

- Below ~1024px: the itinerary spine and any pin/scrub behavior are disabled;
  Section 3's counter still animates once via a plain (non-pinned) ScrollTrigger
  fire, matching the reduced-complexity pattern already used for Services' routes.
- Two-column sections (Section 2) stack to a single column.
- Mobile-first fluid type via `clamp()`, consistent with the rest of the site.

## Performance & accessibility

- Animations restricted to `transform`/`opacity` (+ `filter: blur` on the Arrival
  Reveal, which is compositor-friendly) to hold 60fps and avoid layout shift.
- Images `loading="lazy"` except the first-viewport heading area (no hero photo
  on this page, so nothing needs eager-loading priority beyond normal flow).
- `prefers-reduced-motion: reduce` → everything renders in its final state, no
  animation.
- `?static` query param → same fully-rendered final state, used for our own
  screenshot verification (matches `hero-v2.html`'s existing convention).
- Semantic heading order, visible `:focus-visible` states on all links, meaningful
  alt text on the photo.

## Verification plan

- Desktop (~1440px) and mobile (375px) screenshots in `?static` mode for layout.
- Animated-mode scroll simulation via `preview_eval` to confirm: reveal-on-scroll
  fires once per section, the Section 3 pin holds and releases, counter animates
  0→14 exactly once.
- Console check for errors.
- Restore the user's standalone `python3 -m http.server 3000` afterward (this
  project's established convention, since the preview tool needs the port
  temporarily).
