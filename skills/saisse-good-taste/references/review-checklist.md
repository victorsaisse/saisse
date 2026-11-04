# Review checklist (the Saisse pass)

Run this on the live screen, not on the code. Each "no" is a finding with a screenshot and a fix.

## Familiarity
- [ ] Would a regular user of the platform know how to use this without reading anything?
- [ ] Is every pattern (filters, search, sort, pagination, dates, modals) the platform's own, or a faithful copy?
- [ ] Were values (radius, shadow, type, color, easing) taken from the reference, not guessed?

## First screen
- [ ] Is the most-used action reachable in one click?
- [ ] Is there one clear focal point?
- [ ] Is it obvious whose product this is and what the next step is?
- [ ] Could any text be replaced by an icon, a title or a button? Then replace it.

## Layout
- [ ] Left edges line up, card heights match per row, buttons sit at the same offset.
- [ ] No empty holes; vertical space used; room left for system overlays.
- [ ] Tables: text left, numbers right, blank empties, clear row separation.
- [ ] Works at desktop and ~390 px.

## States
- [ ] Selected looks selected; selectable looks selectable at rest.
- [ ] Hover, pressed, focus, disabled (with reason), loading (skeleton), empty, error (says what to do).
- [ ] Optimistic: the control shows the new value immediately.

## Interaction
- [ ] Search debounced, Enter submits, input forgiving, keyboard navigation in results.
- [ ] Escape closes, focus returns, Tab order sane.
- [ ] Dismissed things can come back.

## Motion
- [ ] Every animation answers an action. Nothing moves at rest.
- [ ] Gentle: small distances, strong ease-out, platform timing.
- [ ] Effects (glass, glow) are light and meaningful, not heavy.
- [ ] Reduced motion respected.

## Performance
- [ ] LCP, CLS, INP measured. No layout shift on first load.
- [ ] Images sized and compressed (WebP 1x/2x), videos compressed with a poster.
- [ ] Secondary data loads after first paint.

## Assets
- [ ] One art direction across the set; background matches the slot color; no seams when the card stretches.

## Copy
- [ ] Short, active, same name for the same action everywhere, matches what actually happens.
