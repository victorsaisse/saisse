# Motion craft

The numbers and rules that make code-made motion look designed. Load this before building scenes or when tuning feedback like "it feels floaty" or "the title flies by".

## 1. Frames are the unit

- Plan in seconds, build in frames. At 30 fps: 0.1 s = 3 f, 0.5 s = 15 f, 0.6 s = 18 f, 1 s = 30 f. At 60 fps double it.
- Every timeline number is a **global frame** unless labelled. Inside a `<Sequence from={120}>`, local frame 30 is global frame 150. Write both when they differ.
- Keep a reference's timestamps labelled with the reference's own fps. A half-second move is 15 f at 30 fps and 30 f at 60 fps.
- Every revision becomes a number: "move the hit to 1260", "add 4 frames of anticipation". An agent cannot misread a frame number.

## 2. The six controls (plus holds and beats)

Turn any reference or brief into named, editable values in ONE config file per piece. Feedback then maps to one value. Source: [beamnxw, Remotion + GSAP motion lab](https://x.com/beamnxw/status/2112180280110776797).

| # | Control | What it changes | Feedback -> edit |
|---|---|---|---|
| 01 | spacing | how far an object travels between poses | "cards fly in from further" -> `spacing.cardTravel += 200` |
| 02 | typography | scale, tracking, line breaks, variable axes | "tighter title" -> `typography.titleTracking = -0.04` |
| 03 | motion blur | exposure during fast moves | "less smear" -> `motionBlur.shutterAngle = 180` |
| 04 | easing | how moves speed up and slow down | "softer landings" -> `easing.enter = 'expo.out'` |
| 05 | follow-through | the tail settles after the body stops | "delay the tail by 3 frames" -> `followThrough.tailDelay += 3` |
| 06 | anticipation | small pullback before the main move | "add 4 frames of anticipation" -> `anticipation.frames += 4` |
| + | holds | how long a pose reads | "hold the title for 1 second" -> `holds.title = 30` |
| + | beats | frames locked to VO words, sung words, music hits | "slam the logo on the sung 'loop'" -> `beats.logoSlam = 121` |

Template (Remotion project, 1080x1920 at 30 fps). This is the shape Victor's studio uses:

```ts
// <piece>/motion.config.ts: every tunable value lives here.
// Times are frames at 30 fps. Distances are px on the canvas.
export const fps = 30;
export const width = 1080;
export const height = 1920;
export const durationInFrames = 150;

export const config = {
  spacing: {titleTravel: 140, cardTravel: 520, logoTravel: 260},
  typography: {titleSize: 150, titleTracking: -0.035, titleWeight: 900, slant: -10, width: 112, lines: {hook: ['GOT AN', 'IDEA?']}},
  motionBlur: {enabled: true, shutterAngle: 200, samples: 6},
  easing: {enter: 'expo.out', exit: 'power3.in', snap: 'back.out(2.2)', whip: 'expo.inOut'},
  followThrough: {tailDelay: 3, overshoot: 1.12, settle: 9, stagger: 2},
  anticipation: {frames: 5, pullback: 0.9, lift: 36},
  holds: {hookTitle: 6, logo: 24},
  beats: {voOffset: 5, hook: 13, product: 44, logoSlam: 121},
  palette: {bg: '#111315', accent: '#ea580c', text: '#ffffff'},
};

// SFX cues: at = frame where the sound's PEAK lands (see audio.md).
export const sfx = [
  {id: 'whoosh_fast/crisp', at: 27, gain: -7, dur: 0.5},
  {id: 'cut_hit/sub', at: 121, gain: -3},
] as const;
```

Save the approved render next to the config that made it. Otherwise "go back to yesterday's version" becomes a memory test after twenty variations.

## 3. Starting values that work

These shipped in real pieces. Start here, then tune one value at a time with the destination fixed.

| Thing | Value |
|---|---|
| Arrival ease | `expo.out` (fast start, long soft landing). Close CSS curve for UI and HTML pieces: `cubic-bezier(0.22, 1, 0.36, 1)` |
| Departure ease | `power3.in` |
| Pop with overshoot | `back.out(2)` to `back.out(2.2)` |
| Whips, wipes, strips | `expo.inOut` |
| Linear entrance fallback | cubic ease-out with clamped endpoints, 18-24 f |
| Anticipation | 4-5 f, scale pullback to 0.9, or a 30-36 px lift before a drop |
| Follow-through | tail lags 3 f, overshoot 1.10-1.12, settle over 9-10 f |
| Stagger | 2-3 f between letters, cards, tiles, stars |
| Supporting label after its lead | about 0.1 s (3 f), then tune in playback |
| Subtle camera push | scale 1.00 -> 1.03 over 3 s, or 1.00 -> 1.06 over 60 f. Interpolate linearly: constant percentage changes compound |
| Final lockup hold | 24-36 f of stillness before the end |
| Readable title hold | about 1 s (30 f) from the moment it is legible |
| Typing speed | about 3 characters per frame for a fast prompt-typing beat |

Spring settings (`spring()` in Remotion): change damping, stiffness, mass or overshoot one at a time and replay the same entrance. [Remotion spring docs](https://www.remotion.dev/docs/spring)

## 4. Physics keeps it honest

- Free fall time `t = sqrt(2h / g)`. A ball falling 30 cm takes about 0.25 s, so 7-8 frames at 30 fps. 1 m takes about 0.45 s (13-14 f). Slower reads as floaty, and viewers feel it without naming it. Source: [Dumble, 2.5D course](https://x.com/Y0urDumble/status/2115164938884862375).
- Gravity is ease-in on the way down, no bounce unless the material bounces.
- Put a squash and a small overshoot on every drop and bounce. That alone stops a launch video looking like a slideshow (reply on the [six controls post](https://x.com/beamnxw/status/2112180280110776797)).

## 5. Holds and reading windows

- Count a hold from the frame the words are **legible**, not from when they start moving.
- 8 frames at 30 fps is 0.27 s: nobody reads that. Read the text aloud at normal speed while the clip plays. If the hold ends first, extend it.
- The title hold deserves its own control. The most common failure is great motion with half a second to read the line the scene exists for.
- During a hold, supporting motion stays quiet so the text owns the screen.

## 6. Attention: one thing owns the screen

- Introduce the main object first, then supporting information after a small delay.
- If a cursor crosses a headline during a card entrance, delay one of them or reroute the cursor.
- Keep attention on a button until its response appears, then let the result own the reading window.
- A product shot is an event the viewer can follow: starting state -> cursor on the action -> the press registers -> the result is revealed -> hold the result long enough to read it.
- Cursor press beat that works: scale 1 -> 0.92 over 3 f at the press frame, back to 1 over 6 f. Path stays clear of text; record the waypoints.

## 7. Typography in motion

- Lay out with the **longest** copy you expect. If it wraps to three lines, stop the motion pass: shorten, widen the box, or move info to another shot.
- Record the layout limits: headline width, max lines, logo space, final positions.
- Check the real font in the rendered frame (weight, spacing). Re-render the landing frame after every copy change before judging the entrance.
- Big kinetic titles: tracking around -0.035 to -0.04 em, weight 800-900. Variable axes add punch: slant (`slnt`) to match an italic wordmark, width (`wdth`) above 100 for impact.
- Captions: 1-2 words per card, cut on each word's start time, bold white with a dark outline, centre-low inside the 9:16 safe area (clear of platform buttons). Pop-in only on stressed words.

## 8. Motion blur

- Shutter angle 180 = film look, 360 = smeary. 5-6 samples is enough; render time scales with samples.
- Apply blur to fast entrances and whips only. Let it settle with the move and keep the reading hold sharp.
- Inspect sampled frames for smeared text and trails that persist after the card lands. [Remotion motion blur](https://www.remotion.dev/docs/motion-blur)

## 9. Camera

- Slow push-ins and dolly moves, each with a reason (reveal, emphasis, tension). No random drift, no constant orbit.
- Keep the camera static in the first pass. Add camera only after layout and timing are approved.

## 10. Scenes and boundaries

- Every scene has an incoming state and an outgoing state. For a continuous transition, the next scene starts from the exact position, scale and highlight the last one ended on.
- Fonts, palette, spacing and recurring elements live in shared settings. Write down any deliberate exception.
- Transitions that overlap scenes shorten the total. Account for it. [Remotion transitions](https://www.remotion.dev/docs/transitions)
- After a local edit, check both neighbouring boundaries before watching the whole film.
- Reuse approved entrance, cursor and text components in the next piece, re-timed for the new copy.

## 11. Determinism (Remotion and any frame renderer)

- Drive every animated value from the frame number (`useCurrentFrame()` + `interpolate()` or `spring()`). CSS transitions and timers flicker in renders. [Animating properties](https://www.remotion.dev/docs/animating-properties)
- Randomness comes from a fixed seed (`random('seed')`). Never `Math.random()`. [Seeded random](https://www.remotion.dev/docs/random)
- GSAP runs on Remotion's frame clock through `useGsapTimeline` from `@remotion/gsap`. No `play()`, no callbacks, no real-time tweens. [Remotion + GSAP](https://www.remotion.dev/docs/gsap/use-gsap-timeline)
- GSAP `fromTo` inside a timeline renders its *from* state before its start time. Anything that must stay invisible until later needs `immediateRender: false` (this is the classic white-flash bug).
- Text that changes per frame (typing, % counters, timers) comes from the frame number, not from GSAP.

## 12. Acting (characters)

Generated 2D gives itself away when the character never thinks: it bounces, waves and smiles with nothing in the face first.

For every shot write an acting line before animating:

```text
SHOT 07 (2.1 s)   words: "the whole room laughed"
thought        what the character knows or wants right now
anticipation   eyes narrow first, 4 f before anything else moves
action         head turns to camera on the stressed word
hold           10 f on the look so it reads
```

Rules: no idle bounce, every move starts with a look, swap mouths only on stressed words. Source: [Dumble, 2.5D course](https://x.com/Y0urDumble/status/2115164938884862375).

## 13. Tells that read as AI slop

Ban these unless the brief asks for them:

- Centred title on a gradient. Corner labels, timecodes, frame borders.
- Everything fading in the same way. Uniform card fades.
- A whoosh on every cut, or the same whoosh more than twice.
- Idle bounce, random drift, constant camera orbit.
- Motion on a timer instead of on an idea: a new shot should land on a new idea in the script, roughly every 2 s in fast formats.
- Slow Ken Burns zoom on still images where the brief wants energy. Fast cuts (several images per second) beat a zoom that nobody notices.
- Numbers or claims on screen that nobody sourced.
- Characters lit like 3D objects in a 2.5D piece (they stay flat, with a contact shadow so they sit on the floor).
