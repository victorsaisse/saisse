---
name: saisse-good-taste
description: 'Victor Saisse''s design taste and review process for any product surface (web, mobile, Shopify apps, dashboards, landing pages). Copy the platform''s native patterns for muscle memory, cut text to icons + titles + buttons, make the first screen the hero, polish states and micro-interactions, treat performance as design, and review the result live like a picky senior designer. Use when designing, redesigning, polishing or reviewing UI, or when the user says /saisse-good-taste.'
---

# Saisse good taste

How Victor designs and reviews interfaces. Platform agnostic: the same rules apply to a web dashboard, an iOS or Android app, a Shopify admin app or a landing page. Only the "native reference" changes.

The bar: **the product looks like it was built by the platform's own team, then gets one memorable detail of its own.** Get to 70% by copying the platform's basics exactly. The last 30% is assets, states, motion and performance.

## The 10 rules

### 1. Familiarity beats novelty
Users should never relearn anything. They open the screen and their hands already know where to go.
- Find the platform's best reference screen for the same job (Shopify Orders for a list, Shopify Home for a home page, Apple Settings for settings, Linear for an issue list). That is the spec.
- Copy its patterns literally: filter pills, search-as-you-type, view tabs with counts, sort menu, pagination, date presets, table alignment, empty states.
- Use native components first (Polaris, SwiftUI, Material, the design system in the repo). Build custom only when nothing native exists, and then match the native look.
- Do not eyeball. Read the real values: computed CSS in the browser, radii, shadows, font sizes, colors, easing curves, timing. Then use the same ones.

### 2. The first screen is the verdict
The home or landing screen is where the user decides "this is good" or "this is bad". Treat it as the hero of the product.
- What do people do most? Put those actions first, with live counts (Ready for pickup 2, Overdue 48).
- What is the next natural step? Show it (setup checklist that ticks itself off from real data, can be hidden, can be brought back).
- One focal point. If there is a video, it gets a real thumbnail, a play badge with duration, and opens in a modal on the page, never an external link.
- Say whose product this is: the wordmark as the title plus one quiet line about what it does. No big "Welcome to X" block.

### 3. Less text, more signal
Nobody reads paragraphs on a dashboard.
- Icons, titles, buttons. That's it. One short line under a title at most.
- Remove anything without a job: "latest release" badges, giant logos, decorative headers, explanations of what the page is.
- Secondary content gets its own small place (documents under the help center), never mixed into the main flow.
- Numbers speak: a big number, a small label, a trend. Color from the platform's own tones (success, critical, warning, info).

### 4. Consistency and symmetry
Misalignment reads as "cheap" before anyone can say why.
- Blocks start on the same edge, cards in a row are the same height, buttons sit the same distance from the bottom.
- Tables: text left, numbers right, empty cells blank (not a dash floating somewhere), rows clearly separated.
- No weird empty space. Use the vertical space available, leave room only for system overlays (chat bubbles, toasts).
- `box-sizing: border-box` and a spacing scale; fix padding at the cause, not per card.

### 5. States must read as states
- A selected filter looks selected, not like a default button. A selectable thing looks selectable even before hover.
- Every interactive element has hover (pointer devices only), pressed, focus-visible, disabled, loading and empty states.
- Loading uses skeletons in the final layout shape. Optimistic updates: the control shows what was asked for immediately.
- Disabled controls say why (tooltip or one line), and copy matches behaviour exactly.

### 6. Interaction details people feel
- Search: debounce ~300 ms, Enter submits immediately, forgiving input (with or without "#"), results as you type (top 3 under the box, arrow keys + Enter + click).
- Keyboard works everywhere: Tab order, Enter, Escape, arrows in lists.
- Touch targets ≥ 44 px on mobile, hover effects only where a mouse exists.
- Remember user choices (hidden columns, dismissed cards) and give a way back.

### 7. Motion with a reason
- Animate only on hover, press and state change. Nothing loops at rest.
- Strong ease-out curves and the platform's own timings (Shopify ~420 ms for card lifts, 150-250 ms for menus). Small moves: card lifts 4 px, art floats 3 px, scale ≤ 4%.
- Delight is quiet at rest and reveals on interaction (a background pattern that only lights up around the cursor, stops when the cursor stops).
- Effects like glass, glow, distortion: gentle and light, and they must make sense (the status color glowing through a pill). If it looks heavy, it is wrong. Dial it down.
- Respect reduced motion: one still frame, nothing moves.

### 8. Performance is part of the design
A beautiful page that loads slowly is an ugly page.
- Measure LCP, CLS, INP before and after. Target CLS < 0.1 (aim for ~0), no layout shift on first load (reserve space for late content).
- Images: WebP/AVIF at 1x and 2x, sized to the slot, a few KB per card illustration. Videos compressed hard (86 MB → 10 MB) with a poster image and captions.
- Load secondary data after the page appears; never block the first paint on counts.

### 9. Assets with one art direction
Icons get you to 70%. Custom art gets you to 100%.
- One lighting and material language for the whole set (e.g. soft 3D, cream and gold light), on a flat background matched exactly to the card color so wide cards show no seam.
- Crop so the object fills ~80% of the slot, keep the floor shadow, export small.
- Generate with AI image tools (Nano Banana, GPT Image) from written prompts with a variables block and reference images. Study reference creators' techniques, write prompts in your own words. Save the prompts so the set can be extended later.
- Pick one set; consistency beats the "best" single image.

### 10. Identity in small places
- Brand lives in precise details, not decoration: the wordmark, one accent, one signature interaction.
- Environment-coded icons and names (green icon + "QA" for develop) so the team never confuses apps.

## Process

1. **Research (~20 min).** Open the platform's own equivalent screen and note patterns, values and micro-interactions. Pull 5-10 top-rated references (Mobbin or similar) for the same job. Record a short video of the reference interactions when motion matters.
2. **Three low-fi directions.** Simple mockups, each with a one-paragraph rationale. Recommend one. Let the user pick before building.
3. **Build on native components.** Copy the reference's values. Real content, real data, no lorem ipsum.
4. **Look at it live** at the sizes it ships (desktop and ~390 px mobile), in the real host (embedded admin, simulator, device). Screenshot every state.
5. **Review with the checklist** in [references/review-checklist.md](references/review-checklist.md). Fix what one look shows, then look again.
6. **Performance and accessibility pass**: LCP, CLS, image weight, keyboard, screen reader labels, reduced motion.
7. **Ship in small commits**, each one reviewable with before/after screenshots.

## How to talk about it

- Lead with what changed and where to look ("hover the greeting to see the slats").
- Before/after with numbers (CLS 0.0006, image 5.3 KB, video 86 → 10 MB).
- Give a pick when offering options, with the reason in one line.
- Name trade-offs honestly; never claim "pixel perfect" without having looked.

## Platform translation

| Rule | Web / Shopify admin | iOS | Android |
| --- | --- | --- | --- |
| Native reference | Shopify Orders, Home, Products; Polaris web components | Apple's own apps, HIG, SwiftUI defaults | Google's own apps, Material 3 |
| States | hover + focus-visible + pressed | highlight + haptics | ripple + state layers |
| Motion | CSS transitions, strong ease-out | springs, interruptible | Material motion tokens |
| Lists | filter pills, search-as-you-type, row click opens | swipe actions, pull to refresh, search in nav bar | chips, FAB for primary action |
| Performance | LCP / CLS / INP, WebP | launch time, 60/120 fps, image decode off main thread | startup, jank, baseline profiles |
