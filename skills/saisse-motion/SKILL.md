---
name: saisse-motion
description: 'Victor Saisse''s motion design and video studio playbook for coding agents. Makes product demos, launch films, social ads with CTA end cards, UGC-style ads with lip sync, explainer and 2.5D story shorts, AI model demo reels, looping hero videos, kinetic type, presentations with motion and screen-recording edits at motion-designer quality. Covers picking the stack (Remotion + GSAP, HyperFrames, Blender MCP, ffmpeg-skill, earshot, ElevenLabs), script and audio first, storyboards in frames, the six editable motion controls, ElevenLabs voiceover, narration and music, frame-exact sound effects, captions, the contact-sheet review loop, compression and delivery. Use when the user asks for any video, motion graphic, animation, demo, reel, ad, end card, voiceover, sound design or video edit, or says /saisse-motion.'
license: MIT
---

# Saisse motion

## What this is for

Making videos and motion pieces with a coding agent, and making them good. The agent writes every frame as code, so every piece can be reviewed frame by frame, every note becomes a number, and every change is a re-render.

Use it for:

| Ask | Example |
|---|---|
| Product demo, launch film, feature clip | "5 s vertical demo of the app, fast, British voice, a jingle" |
| Ads | "3 end-card variants for these ads", "replicate this viral ad format with our own characters" |
| Explainers and story shorts | "a 60 s 2.5D short about the seismoscope from 132 AD" |
| Model reels and loops | "reel for the new video model", "5 s looping card for the pricing page" |
| Voice, music, sound | "ElevenLabs narration for this script", "sound design for this render" |
| Editing real footage | "cut the pauses, add captions, 9:16, export for Reels" |
| Presentations | "1-minute visual update deck with motion" |
| Delivery | "compress these to 3 MB, no audio, 720p, replace the originals" |

It packs the stack, the numbers, the prompts and the review loop from the best public work on agent-made motion (posts and repos in `references/sources.md`) plus Victor's own lessons (`references/victor-notes.md`).

## The bar

- It looks like a motion designer made it: sound on, at phone size, at normal speed. Not a template, not a slideshow.
- One clear message per piece, or one fact worth repeating. The motion delivers it; it is never the point.
- Every value lives in one config. Feedback like "add 4 frames of anticipation" is one edit and one draft render.
- Nothing ships unreviewed: sheets, sound readings and an ffprobe check come before "done".

## Step 0: route the job

1. **Local studio first.** If `~/motion-studio` exists, it is Victor's working Remotion + GSAP + earshot + ElevenLabs studio. Read `~/motion-studio/README.md` and `~/motion-studio/studio/CLAUDE.md`, copy an existing project as the starter, and use its scripts (`render.mjs --draft`, `sfx.mjs`, `mix.mjs`, `align.mjs`, `stills.mjs`).
2. **Pick the stack** (details and install commands in `references/toolkit.md`):

| Job | Stack | Recipe |
|---|---|---|
| Demo, launch film, kinetic type, end cards | Remotion + GSAP + earshot + ElevenLabs | `formats.md` 1, 2, 5 |
| Site or app hero loop | Remotion or HyperFrames, muted | `formats.md` 3 |
| UGC ad with generated people | image + motion-transfer + lip-sync models, ffmpeg composite, Remotion end cards | `formats.md` 4 |
| HTML-first piece, slideshow, cloning a reference's motion | HyperFrames (`/reverse-any-video` is Pro) | `toolkit.md` |
| Opinionated launch film fast | cinetic or motionmaxxing | `toolkit.md` |
| 2.5D story or explainer | Blender MCP + SVG characters + Remotion captions + Whisper | `formats.md` 6 |
| Model demo reel or looping card | provider video/image models + ffmpeg | `formats.md` 7, 8 |
| Real footage edits | ffmpeg-skill, or `ffmpeg-cookbook.md` | `ffmpeg-cookbook.md` |
| Deck with motion | local HTML + Motion or GSAP | `formats.md` 10 |

3. **Load the craft references** you need: `motion-craft.md` before building scenes, `audio.md` before any voice, music or SFX, `review.md` before showing anything.

## The loop

Each step ends with something Victor can look at. Steps marked GATE wait for his OK.

1. **Brief (GATE).** Get only what is missing: goal and viewer; where it plays (aspect, duration, loop or not, sound on or off); assets (logo, screens, fonts, colours: pull them from the real site or repo); copy and CTA; voice (own recording or ElevenLabs, persona); music and SFX; deadline. For anything that costs credits or long renders, send the plan, the number of outputs, the models and the total cost first.
2. **Reference.** If there is one, extract frames and write `REFERENCE.md`: the format, not the content, every value labelled MEASURED, ESTIMATED or DECISION (`prompts.md` 2). Never copy characters, designs or scripts.
3. **Script, then audio.** About 2.5 spoken words per second. Generate the VO (ElevenLabs `eleven_v4` by default, 2 takes x 2-3 voices), pick by measurement, tighten pauses, get word timings by forced alignment, write them into `beats`. Music next (composition plan, several seeds). The audio decides the cuts (`audio.md`).
4. **Storyboard (GATE).** Shot list plus a beats table in global frames. Then three stills: opening, product result, closing. Approve message and layout before any motion detail.
5. **Build.** One config per piece: the six controls (spacing, typography, motion blur, easing, follow-through, anticipation) plus holds, beats, palette and SFX cues. Every animated value comes from the frame number; seeded randomness; GSAP only through `useGsapTimeline`. Real assets, never redrawn UI (`motion-craft.md`).
6. **Sound.** earshot cues placed so each sound's peak lands on its frame; voice on top, music ducked under it; mix to about -14 LUFS with true peak at or under -1.5 dBTP (`audio.md`).
7. **Review (GATE).** Half-scale draft -> labelled frame sheet -> fix timing -> full render -> contact sheet + phone sheet -> earshot `sheet` and `critique` -> critic pass in a fresh context -> four-field corrections. Fix order: wrong or missing, readability, timing, detail (`review.md`).
8. **Deliver.** Final render, ffprobe check, variants and platform cuts, compression to the target (`ffmpeg-cookbook.md`), files in `~/Downloads` with clear names. Save the approved render next to its config, brief and cue list. Report size, dimensions, fps, duration and LUFS.

## Feedback -> edit

| He says | You change |
|---|---|
| "add 4 frames of anticipation" | `anticipation.frames += 4` |
| "delay the tail by 3 frames" | `followThrough.tailDelay += 3` |
| "hold the title for 1 second" | `holds.title = 30` (at 30 fps) |
| "cards come from further" | `spacing.cardTravel` up |
| "softer landings" / "snappier" | `easing.enter` (`expo.out` softer, `back.out(2)` snappier) |
| "less smear" | `motionBlur.shutterAngle` toward 180 |
| "the logo should hit on the word" | move `beats.logoSlam` to the word's frame; SFX follows the beat |
| "it feels floaty" | shorten falls to physics (30 cm = 7-8 f at 30 fps), add squash on landing |
| "too slow / boring" | more ideas per second, fewer holds on non-text, cut to the beat |

Change one value, draft render, show the frames that changed and name the value you edited.

## Hard rules

1. Audio first. VO and music timings decide the cuts.
2. Render at the delivery size. Never render bigger and crop.
3. Frames, not seconds, in every plan and correction. Global frames unless labelled.
4. One thing owns the screen. Main object first, supporting info about 3 frames later.
5. Holds count from legibility. Read every line aloud while it plays.
6. Lay out with the longest copy. Three lines = stop and fix the copy or the box.
7. A sound only where something physical happens. No whoosh more than twice. One swell per film.
8. Peaks on frames: place each SFX so its measured peak lands on the cue frame.
9. You cannot hear: quote earshot numbers, never "it sounds good".
10. Lip sync must match on every talking or singing face. Measure it.
11. Generated people stay consistent shot to shot; environments make physical sense.
12. Real brand assets and tokens. No invented ratings, numbers or claims on screen.
13. Take the grammar of a reference, never its content. Clear the rights to any trend audio before paid use.
14. Read sheets, not playthroughs. Re-render only the frames around a fix.
15. Plan and cost before paid generation. Keys from the environment, never printed.
16. Save the approved render with the config that made it.

Banned unless asked: centred title on a gradient, corner labels, timecodes, frame borders, everything fading in, idle bounce, random drift, constant orbit, slow zoom on stills where energy is wanted.

## Victor's defaults

- Fast, dynamic, launch-video energy. Bright and high-contrast for real-world footage.
- 3 variants that differ in one idea; he picks.
- Outputs in `~/Downloads`; research and finals in separate folders; side-by-side comparison videos when matching a reference.
- ElevenLabs for voice and narration (v4 for narration, v4 turbo for realtime, Flash v2.5 for cheap). Victor's studio reaches ElevenLabs through fal.
- Social loudness -14 LUFS. Muted MP4 + WebM + poster for web loops.
- Decks: visual, two or three bullets per slide, local HTML.
- Everything local. Do not publish to the cloud unless asked.

More in `references/victor-notes.md`.

## Quick commands

```bash
ffprobe -v error -select_streams v:0 -show_entries stream=width,height,avg_frame_rate,duration -of json out.mp4
ffmpeg -i reference.mp4 -vf "fps=2,scale=960:-2" frames/ref-%04d.jpg          # reference frames
ffmpeg -i out.mp4 -vf "fps=1,scale=240:-1,tile=8x9" -frames:v 1 review/contact.png   # contact sheet
npx remotion still <Comp> out/result.png --frame=240                             # one still
npx remotion render <Comp> out/fix.mp4 --frames=180-249 --codec=h264             # only the fix
earshot sheet out.mp4 -o review/sound.png --cues cues.json                       # did the sounds land
```

Size-capped export (`fit`), captions, overlays, reframes and stitching: `references/ffmpeg-cookbook.md`.

## References

| File | Load when |
|---|---|
| `references/toolkit.md` | choosing or installing tools; every repo with install command, licence and catches |
| `references/motion-craft.md` | building or tuning scenes: six controls, starting values, physics, holds, typography, determinism, slop tells |
| `references/audio.md` | voice, ElevenLabs models and tags, word timings, music, earshot SFX, mix, captions |
| `references/prompts.md` | copy-paste prompts and templates for every stage |
| `references/formats.md` | specs and pipelines per deliverable, platform sizes |
| `references/review.md` | sheets, critic pass, four-field corrections, checklists |
| `references/ffmpeg-cookbook.md` | probing, sheets, compression to size, stitching, reframing, captions, overlays |
| `references/business.md` | selling launch clips, running the studio, licences, platform rules |
| `references/victor-notes.md` | Victor's preferences, worked examples, pitfalls already paid for |
| `references/sources.md` | every X post, reply, repo and doc behind this skill |
