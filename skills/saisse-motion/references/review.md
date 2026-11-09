# Review loop

How to judge a render without watching it ten times, how to write a correction an agent cannot misread, and what "done" means. Sources: [Dumble, 2.5D course](https://x.com/Y0urDumble/status/2115164938884862375), [beamnxw, motion engineering](https://x.com/beamnxw/status/2108616740846420354), [earshot](https://github.com/Leonxlnx/earshot), plus Victor's own review habits.

## 1. Make the frames come to you

Never judge from one playthrough. Generate sheets and open them (commands in `ffmpeg-cookbook.md`):

| Sheet | What it answers |
|---|---|
| Three stills: opening, product result, closing | Is the message readable? Is the result visible? Anything cropped or overlapping? Room for the closing action? Check these BEFORE adding motion detail. |
| Labelled frame sheet (every 6th frame, numbered) | Timing, overlaps, jumps between in-betweens, where exactly a fix goes |
| Contact sheet (1 frame per second) | Pacing, a new idea every few seconds, repetition, dead stretches |
| Phone sheet (360 px wide) | Readability at real viewing size |
| earshot `sheet` (frames + spectrogram + cue markers) | Did every sound land on its frame: green = within a frame, amber = within 100 ms, red = no sound |

## 2. Order of passes

1. Three stills -> approve layout and copy.
2. Draft render at half scale -> labelled frame sheet -> fix timing.
3. Scrub in the editor (Remotion Studio: `npx remotion studio`) on the moments that matter.
4. Full render -> contact sheet + phone sheet.
5. Sound on: headphones AND a phone speaker. Check the loudest event, the quietest useful cue and the ending.
6. Eyes closed once: does the audio alone tell the story and land the hits?
7. Watch it like a viewer: normal speed, on a phone, sound on, one time through without pausing.

## 3. Critic pass

Hand the sheets to a fresh context as a critic, not as the author. A second session with only the brief, the sheets and the extracted frames gives an independent read. Ask for up to 5 frame-specific problems, then send only the accepted ones to the builder. Prompt in `prompts.md` (critic pass).

## 4. Corrections: four fields

Every correction names where, what is wrong, what changes, and what must stay fixed. Then ask for proof at specific frames.

```text
WHERE      global frames 204-222 (scene PRODUCT)
PROBLEM    the cursor covers the result label at 210
CHANGE     only the cursor's exit path: leave at 204, rest 24 px clear of the label by 210
KEEP       the press at 195, the reveal at 210, card geometry, copy, audio cues

Render stills at 195, 210, 222, 240 and a preview of 180-249.
Check the in-between frames for overlap and jumps. Report what changed and the output files.
```

A scoped edit stops the agent from wandering. The repair loop stays local: defect -> one scoped edit -> affected frames -> the neighbouring scene boundaries -> normal-speed playback.

## 5. Fix order

1. Anything wrong or missing (facts, product state, assets, brand, claims)
2. Readability (text size, holds, contrast, safe areas, captions)
3. Timing (beats on words, holds, sound on frame, pacing)
4. Visual detail (easing, blur, polish)

## 6. Checklist before showing anyone

Picture
- [ ] Every line readable at phone size, including the longest copy variant
- [ ] Holds long enough to read aloud at normal speed
- [ ] Nothing cropped, overlapping, or under platform UI (9:16 safe areas)
- [ ] One thing owns the screen at a time; the cursor never crosses text mid-entrance
- [ ] A new idea or shot at least every ~3 s in short social pieces; nothing lingers without a reason
- [ ] None of the slop tells in `motion-craft.md` section 13
- [ ] Motion blur never smears text; scene boundaries cut clean
- [ ] Brand is exact: real logo file, real colours, real font, real product screens (not redrawn approximations)
- [ ] First frame works as a thumbnail; a loop's last frame flows into its first

Sound
- [ ] Voice always on top; music sits under it and resolves on the final frame
- [ ] Each SFX cue lands on its frame (earshot sheet green); sounds only where something physical happens
- [ ] No whoosh used more than twice; one swell per film at most
- [ ] Integrated loudness about -14 LUFS, true peak at or under -1.5 dBTP (`earshot measure` or `loudnorm` summary)

Truth
- [ ] Every number and claim on screen has a source the user approved
- [ ] AI-generated people, faces and characters stay consistent shot to shot (same face, same outfit, same place)
- [ ] Environments make physical sense (a driver is in a car, not inside the terminal)

Delivery
- [ ] ffprobe shows the planned width, height, fps and duration
- [ ] File size within target; plays in QuickTime and a browser
- [ ] The approved render is saved next to its config, brief and cue list

## 7. Sound review with earshot

You cannot hear. Never say audio "sounds good" or "fits" without a measurement behind it, and quote the number.

```bash
earshot timeline film.mp4 --cues cues.json     # events, sections, loudness curve, sync per cut/cue (offset_ms: negative = early)
earshot sheet film.mp4 -o sheet.png --cues cues.json   # open the PNG
earshot critique film.mp4                      # scorecard vs professional launch films: launch-grade / close / needs work
earshot measure mix.wav                        # LUFS, true peak, LRA, noise floor
```

Fix critical rows first, then re-run on the new render. Change one thing at a time and A/B it (`earshot ab before.wav after.wav --query "..."`).

## 8. AI-generated footage QA

- Generate in batches (2-4 takes per shot) at a cheaper resolution first (720p or lower), pick the best, then upscale or regenerate the winner.
- Shorter shots are easier to validate: more 5 s shots beat fewer 10 s shots.
- Check every take: identity (face, hair, outfit), props, environment logic, lip sync, hands, text artefacts. Regenerate anything off-model instead of hoping the edit hides it.
- Batch transforms break colour science on a minority of outputs (one team reports fixing ~15% by hand). Budget a creative QA pass; it is the new bottleneck.

## 9. Pitfalls that cost a night

- Render CLI flags drift between versions. A wrapper that silently ignores a renamed flag keeps rendering with defaults. Pin params as constants, diff against `--help` after upgrades, and eyeball frame 1 of a batch before queueing the rest.
- Watching a full export repeatedly instead of reading sheets.
- Re-rendering the whole piece to check one fix. Render the range around it.
- Feedback in vague words ("make it pop"). Translate it to a control and a number before touching code, and say which value you changed.
