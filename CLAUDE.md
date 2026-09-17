# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file static marketing/admissions landing page for Palm Global Academy (an online British-curriculum + Islamic-education school), built for a specific enrolment campaign (currently "September 2026 Admissions, Years 7–9"). There is no framework, no build step, and no package.json — `index.html` is the entire site (inline `<style>` and `<script>`), plus a handful of images in `assets/`. `.nojekyll` indicates it is deployed via GitHub Pages, served as-is.

## Commands

There is no build, lint, or test tooling in this repo. To preview changes, just open `index.html` in a browser or serve the directory statically, e.g.:

```
python3 -m http.server 8000
```

There are no automated tests to run.

## Architecture

Everything lives in `index.html`, organized top-to-bottom as a conversion funnel:

1. Sticky header — logo, countdown timer (`data-deadline`), "Book a Call" button anchored to `#book`.
2. Hero — "Step 1: watch video" (autoplaying muted YouTube iframe built from `data-youtube` via JS) then "Step 2: book a call" (inline Calendly widget, `#book`).
3. Proof strip, 3-pillar education framework, first-term phase cards, Pearson Edexcel accreditation block.
4. Testimonials and teacher cards — each video is a facade (`<button class="js-youtube" data-youtube="...">`) that opens the real YouTube iframe in a lightbox modal (`#video-modal`) rather than embedding inline, to avoid loading many iframes at once.
5. FAQ (native `<details>`/`<summary>`), final CTA.

The single inline `<script>` at the bottom does three things: builds the hero iframe `src` from `data-youtube`, wires up the video lightbox (open/close/escape/backdrop-click), and drives the countdown timer(s) via `setInterval`. Any element with `.countdown[data-deadline]` gets wired automatically, so adding another countdown elsewhere just means adding the markup.

Styling is a single hand-written CSS system defined via custom properties in `:root` (colors, spacing, radii) at the top of the `<style>` block — no framework/utility classes. Responsive behavior is handled by a small number of `@media` breakpoints near the bottom of the `<style>` block (1020px, 760px, 540px), not per-component.

## Notable repo-specific quirks

- The stylesheet contains rules for sections that no longer exist in the markup (e.g. `.footer`, `.nav__links`, `.mobile-cta`, `.leadership`, `.journey`, `.fit-grid`, `.year-rhythm`, `.long-game`, `.credentials`) — leftovers from earlier campaign versions that were trimmed from the HTML without cleaning up the CSS. Don't assume a CSS class in use just because it's defined.
- The `#fees` section is fully built out in HTML but has the `hidden` attribute — it's intentionally disabled, not broken. Check git history/commit messages before re-enabling or deleting sections; this page is actively iterated as a live campaign (swapping video IDs, toggling sections, correcting names) rather than incrementally engineered.
- Sections are also disabled in-place via HTML comments (e.g. the "choice many Muslim parents feel forced to make" section, "Phase 4 · the long game") — search for `<!--` before assuming a topic isn't covered anywhere in the file.
- YouTube videos are referenced only by ID via `data-youtube`; the embed URL (autoplay/mute/origin/referrer params) is constructed in JS, not hardcoded per video.
