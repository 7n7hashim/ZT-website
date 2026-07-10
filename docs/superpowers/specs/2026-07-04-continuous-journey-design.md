# Continuous luxury journey — design spec

**Date:** 2026-07-04 · **Status:** Approved ("one day of travel" treatment) · No git repo; saved to disk only.

## Concept

hero-v2.html becomes one uninterrupted scroll film. The page traces a single day:
golden-hour hero → daylight paper (Services, Our Story) → late-afternoon sand
(Featured Destinations) → dusk umber (Why Choose, Testimonials) → night charcoal
(Partners, CTA, Footer #191009). Chapter transitions are long gradient bridges and
overlapping elements — light changes, sections never visibly switch.

## Global depth system (GSAP + ScrollTrigger only)

- `liftIn(els)` — 3D card entrance: rise + rotateX(6°→0) with perspective, settles
  flat, power3.out, once per element. No bounce.
- `depthParallax` — elements tagged `data-depth="±px"` drift slowly while their
  chapter transits the viewport (scrubbed, max ±40px). Disabled ≤1024px.
- In-card image drift: destination card images render oversized and drift slower
  than their frames.
- Existing helpers reused: verticalCutReveal (data-vcr, now global), revealOnScroll,
  Arrival Reveal, stat pin. Story re-dress: pillars use liftIn; beginning image
  frame gets ±10px depth drift (frame-level, so it can't fight Arrival Reveal's
  img-level transforms); spine kept.

## New chapters (after Our Story)

1. **Featured Destinations** `#destinations` — sand gradient (paper → #e9d9bd).
   Split chapter header. Three tall staggered cards: Masai Mara & the Safari
   Circuit (safari-landscape.jpeg), The Coast & Indian Ocean (holidaypackage.jpeg),
   Worldwide Escapes (plane-hero.jpeg). Per CLAUDE.md image guardrails: bottom
   gradient overlay + amber mix-blend-multiply treatment. liftIn + image drift.
2. **Why Choose** `#why-us` — dusk. Four glass panels (backdrop-blur, hairline
   border — glass used here ONLY): 14+ years expertise / 1:1 personal planner /
   one corporate+leisure standard / 24-7 human support. Different data-depth rates.
3. **Testimonials** — dusk/dark, typography-led (no glass). One leading quote, two
   supporting on offset depth planes. Placeholder quotes (user approved).
4. **Travel Partners** — night. Typographic wordmarks, cream at low opacity,
   progressive stagger reveal: Kenya Airways, Emirates, Qatar Airways, Turkish
   Airlines, Serena Hotels, Amadeus (user vets names before live).
5. **CTA** `#contact` — bookend: hero-savannah-extended.jpg under a night grade,
   soft mask-fade edges into the dusk gradient; big VCR line "Your Journey Begins
   with a Conversation" + existing pill styles.
6. **Footer** — unchanged design; dusk gradient bottoms out at exactly #191009 so
   the footer is the natural end of the night. Dev proto-note moves BELOW the
   footer as a slim dark strip.

## Wiring

Nav + footer links become in-page: Packages→#destinations, Services→#services (id
added), Our Story→#our-story, Why Us→#why-us, Contact→#contact. Same guards as
before: prefers-reduced-motion, ?static, CDN-failure → fully rendered static page.
