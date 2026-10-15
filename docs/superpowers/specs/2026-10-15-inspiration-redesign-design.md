# Zaras Travel — Inspiration Redesign

**Date:** 2026-10-15
**Branch:** `redesign-inspiration`
**Inspiration:** https://realestate-web-five.vercel.app/

## Goal

Rebuild the Zaras Travel one-pager so it looks and behaves like the inspiration
site — corporate-premium, navy + accent, Montserrat light/bold headline pairs,
pinned scroll stories, card carousel, rich footer — using Zaras Travel's own
brand colours, photos and copy. Lead capture stays WhatsApp-based.

## Decisions (from brainstorming)

- **Full rebuild.** The current cinematic page (savannah hero, window-seat story,
  dusk arc) is replaced, not restyled.
- **Fonts:** Montserrat (headlines, eyebrows, buttons) + Inter (body), both
  self-hosted in `fonts/`. Cormorant Garamond is removed. Never link
  `fonts.googleapis.com` (blackholed on the user's network; hangs WebKit).
- **Real content only.** Package prices are included (user will supply them).
  Team, numbers band and a packages listing grid are left out. Testimonials and
  Partners (unvetted placeholders) are removed.
- **Prices:** until the user sends the list, cards show `From KES 000,000` so the
  missing values are unmistakable; every placeholder price is listed in the
  hand-off.

## Stack

- Single `index.html`, inline `<style>`, inline `<script>`.
- ~~GSAP 3.12.5 + ScrollTrigger~~ **Changed during build:** pinned sections use
  CSS `position: sticky` + a small vanilla scroll-progress script, reveals use
  IntersectionObserver. No third-party scripts at all, so nothing external can
  block rendering. The page is fully readable with JS off (stacked layout).
- `serve.mjs` copied into the project (`PORT=3005 node serve.mjs`). Puppeteer
  screenshots run from `~/Desktop/ZT/` (`node screenshot.mjs <url> <label>
  <viewport>`), where Puppeteer is installed.
- `?static` query param renders the final state with no animation.

## Visual system

| Token | Value | Role (inspiration equivalent) |
|---|---|---|
| `--navy` | `#0a1628` | hero, business band, footer (`bg-deep`) |
| `--amber` | `#e87722` | bold headline line, primary buttons, eyebrows on dark, stage bar (gold) |
| `--blue` | `#1a5fa8` | logo tab, links, focus rings |
| `--cream` | `#f5f0e8` | alternating light sections (`limestone`) |
| `--paper` | `#fbf9f5` | other light sections (`paper`) |
| `--ink` | `#0a1628` | headings on light |
| `--muted` | navy at ~68% | body copy on light |

- **Type:** headlines Montserrat 300 line + Montserrat 700 amber line,
  `letter-spacing: -0.03em`; body Inter 400, `line-height: 1.7`; eyebrows
  Montserrat 600, ~12px, uppercase, `letter-spacing: 0.22em`.
- **Depth:** base (section bg) → elevated (cards, navy-tinted layered low-opacity
  shadows) → floating (header tab, sticky nav, buttons).
- **Images:** navy gradient overlay from the bottom + a `mix-blend-mode: multiply`
  colour layer. SVG noise grain on hero and dark bands.
- **Motion:** only `transform` and `opacity`; spring-style easing
  (`cubic-bezier(.34,1.56,.64,1)` for small UI, `power3.out` for reveals); no
  `transition: all`. `prefers-reduced-motion` → no pins, no reveals.
- **States:** every link/button has hover, `:focus-visible` (blue ring) and
  `:active`.

## Page structure

1. **Header** — navy logo tab top-left (`brand assets/logo-cropped.png` +
   "ZARAS TRAVEL / HOLIDAYS · FLIGHTS · CORPORATE"); nav Packages · Services ·
   Our Story · Why Us · Contact; amber "Plan a trip" → WhatsApp. Becomes a
   compact sticky bar after scrolling past the hero. ≤1024px: hamburger →
   full-screen navy menu.
2. **Hero** (`#top`) — full-bleed `plane-hero.jpeg` / savannah image, navy
   gradient; eyebrow "HOLIDAYS · FLIGHTS · CORPORATE · KENYA & BEYOND"; headline
   "Making your travel" / **"plans a breeze."**; existing intro copy; buttons
   "Explore packages →" (amber, → `#packages`) and "Plan your journey" (outline,
   → WhatsApp). Bottom strip: "In the picture — Wasini Island, Kenya" + package
   facts (nights · from-price) + "View the package →".
3. **How it works** (`#how`) — pinned on desktop: one large rounded image frame,
   four photos crossfade; overlaid heading "Tell us the dream." / **"We handle
   the journey."**; stage bar beneath: 01 Enquire · 02 Plan · 03 Book · 04
   Travel, active stage + progress line driven by scroll.
4. **Our Story** (`#our-story`) — eyebrow "ABOUT US"; heading "One journey," /
   **"fourteen years in the making."**; four cards (The Sky, The Spark, The
   Founding, Today 14+) stack over each other while pinned on desktop; on
   mobile a plain vertical list. Founder quote closes the section.
5. **Featured packages** (`#packages`) — eyebrow "FEATURED PACKAGES"; heading
   "Explore new" / **"destinations."**; horizontal carousel with prev/next
   buttons and scroll-snap; tall photo cards (Wasini Island, Tanzania, Fiji,
   Dominica, Grenada, Haiti) with region badge, title, location, "From KES …",
   round arrow → WhatsApp prefilled "I'm interested in the <name> package".
6. **Why Zaras** (`#why-us`) — pinned on desktop with "01 / 03" counter and
   "SCROLL TO EXPLORE"; three panels (Expert Travel Planning, End-to-End
   Support, Fast Reliable Service) each with image + two sub-points (existing
   copy). Mobile: stacked panels.
7. **Services** (`#services`) — cream bg; "Plan, book, travel." heading; three
   service cards (Holiday Packages, Flight Booking, Corporate Travel) with
   eyebrow, image, copy.
8. **Business band** — navy; eyebrow "FOR COMPANIES AND ORGANISATIONS";
   "Travelling for business?"; copy; "Talk to us" → WhatsApp; three columns
   (Policy-friendly itineraries, One account manager, Round-the-clock support)
   drawn from existing corporate copy.
9. **Final CTA** (`#contact`) — paper bg; eyebrow "START HERE"; "Tell us where
   you want to go."; "Start planning" (WhatsApp) + "Call +254 713 576 767".
10. **Footer** — navy; brand + blurb; Explore links; Destinations list;
    Contact (phone, WhatsApp); © year.

## Links

- WhatsApp: `https://wa.me/254707350022?text=<urlencoded>` with `target="_blank"
  rel="noopener"`.
- Phone: `tel:+254713576767`.

## Testing

- Screenshot via `node screenshot.mjs http://localhost:3000/?static <label>
  <viewport>` at desktop, tablet, mobile; compare to the inspiration at the
  same widths; ≥2 rounds of fixes.
- Check pinned sections at several scroll positions on desktop.
- WebKit load check (Playwright) — page `load` must fire quickly.
- No horizontal page scroll at 390px. All WhatsApp/tel links correct.
- Reduced-motion: content visible, no pins.
