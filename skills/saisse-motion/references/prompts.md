# Prompts and templates

Fill-in templates for each stage. They merge and rewrite the patterns from the sources in `sources.md` (mainly [Dumble's 2.5D course](https://x.com/Y0urDumble/status/2115164938884862375) and [beamnxw's motion engineering article](https://x.com/beamnxw/status/2108616740846420354)) with what worked in Victor's own studio. Replace everything in [brackets].

## 1. House rules (project CLAUDE.md)

Write the format once so every piece in the project starts from the same rules.

```markdown
# [Project] motion rules

## Format
- [1080x1920 | 1080x1080 | 1920x1080], [30] fps, [5-70] s. Render at exactly this size. Never render bigger and crop.
- One config per piece: src/projects/<piece>/motion.config.ts (six controls, holds, beats, palette, sfx cues).
- Audio first: VO and music word timings decide the cuts.

## Message
- Line 1 is the hook. The last line pays it off. [Every factual claim has a source in sources.md.]
- One product action per shot. Hold the result long enough to read it aloud.

## Look
- Brand: [font], [palette hex], [logo file], real product screens from public/. No redrawn UI.
- [Style notes: e.g. flat 2D characters in lit 3D sets | kinetic type on dark | bright real-world footage]

## Banned
- Centred title on a gradient. Corner labels, timecodes, frame borders.
- Everything fading in. A whoosh on every cut. Idle bounce. Random camera drift.
- Any number or claim on screen that is not approved.

## Before showing me anything
1. Three stills (opening, result, closing). Look at them.
2. Labelled frame sheet and contact sheet. List every problem with a frame number. Fix the 3 worst. Repeat.
3. Report changed files, output paths, checks run. Wait for my OK.
```

## 2. Reference breakdown

Pull frames first (`ffmpeg-cookbook.md`, "Frames for analysis"), then:

```text
Study reference.mp4 for its FORMAT and decisions, not as content to copy.
1. Report its size, frame rate and duration with ffprobe.
2. Look at the overview frames in frames/, then at consecutive frames around each important event.
3. Save REFERENCE.md with:
   - every cut and key event: timestamp, source frame number
   - per shot: what moves first, what the eye lands on, camera move (static / push-in / dolly / pan) and direction
   - the main object's start and landing boxes, travel, scale and opacity change, visible overshoot
   - text: words per card, size relationships, position, when it becomes readable, how long it holds
   - what is 2D, what is 3D, what is footage
   - sound: where hits land, music changes, voice on/off
   - the hook and where the turn lands
4. Label every value MEASURED (read from a frame), ESTIMATED (by eye) or DECISION (our creative choice).
   For anything uncertain, name the exact frames to check. Your spatial estimates from images are approximate.
5. Never copy characters, designs, scripts or story structure one to one.
6. End with a proposal for OUR version using our assets: layout, focal point, entrance, product response,
   reading hold, 5 creative decisions, missing assets, questions needing approval. Wait before building.
```

## 3. Script

Word budget: about 2.5 spoken words per second (150-170 words for 65 s; 10-14 words for a 5 s hype VO).

Product or ad VO:

```text
Write 3 VO options for a [N]-second [product demo | ad | launch film] for [product].
Viewer: [who]. Platform: [where]. Tone: [e.g. fast, enthusiastic, British young female].
Budget: [N x 2.5] words max. Line 1 hooks in under 1.5 s. End on the product name or the CTA.
Mark stressed words in CAPS. Add ElevenLabs audio tags only where they change delivery ([excited], [whispers], [laughs]).
For each option list the beats: which word lands which visual.
```

True-story or explainer short:

```text
Pitch 5 true stories from [history | science | topic] that fit "The [object] that [did something surprising]"
or "Why [thing] always [does X]". For each: one line, why both a 12-year-old and a 50-year-old would repeat it,
2 sources I can open.
Then for my pick: a [150-170]-word script, one narrator, spoken English, short sentences. Line 1 hooks and
promises. A turn around 60% in. The last line pays off the hook. Write sources.md with a source per claim.
```

Check the sources yourself. That step is never fully handed off.

## 4. Shot list and beats

```text
Split script.md into shots, one per idea, [1.5-3] s each. For every shot:
  id, words covered, duration
  SET / LAYOUT   where we are (scene, set, screen)
  CAMERA         framing + move, with the reason
  SUBJECT        who or what is on screen, pose or state
  HERO           any key object (product screen, prop): real asset or built properly, never a sketch
  SOUND          only if something physical happens
Reuse sets and layouts. A 60 s piece needs 3-5 sets, not 30.
Then write the key beats in GLOBAL frames at [30] fps.
```

```text
GLOBAL FRAME   TIME    EVENT
120            4.0 s   report visible, card settled
150            5.0 s   cursor heads to Export
195            6.5 s   press registers (click transient here)
210            7.0 s   finished file appears (reveal hit here)
234            7.8 s   result label fully readable
420            14.0 s  closing shot begins
```

## 5. Brief for a product shot (BRIEF.md)

```text
GOAL
Show how [product] [does the one action]. Viewer: [who]. Opening copy: [outcome, max 8 words].
Closing copy: [one next action + destination].

DELIVERY AND ASSETS
Composition [Name], [1080x1080], [30] fps, [600] frames. All numbers are global, inclusive frames.
Assets in public/: [logo], [screen before], [screen after], [font], [audio]. Inspect sizes and font loading first.
Ask for missing screens or illegible crops before building.

LAYOUT (origin top-left, px)
Background [#101318], text [#F5F7FA], accent [#8AD8B0]. Essential content inside a [64] px margin.
Headline box x[96] y[112] w[888], [64] px, line height 1.1, max 2 lines.
Product card x[120] y[350] w[840] h[480], radius [24]. Fit the screen so the key control stays readable.
Layer order: background, card, product content, cursor, headline.

OPENING 0-119     headline enters 0-18 (y +24 -> 0, opacity 0 -> 1); card enters 18-42 (y +60 -> 0); ease-out, clamped; hold to 119
PRODUCT 120-419   carry the card in exactly; cursor to the button 150-180 on a path clear of text;
                  press at 195 (scale 1 -> 0.92 -> 1 over 192-201); response at 195; result 210-234; hold to 419
CLOSING 420-599   cut to logo + closing copy at 420; copy fades in 420-438; hold to 599

IMPLEMENTATION AND REVIEW
Every animated value from the frame number; timing and layout constants exposed in one config; fixed seeds;
static camera in this pass. Audio: click on 195, reveal hit on 210.
Stage 1: build only the opening, render stills 0, 18, 42, 60, 119 and a preview. Check wrapping, margins,
readability, settled positions. Report files, outputs, open issues. Wait.
After approval: remaining scenes, then stills at 120, 195, 234, 419, 420, 540, 599.
```

Kick-off prompt:

```text
Read BRIEF.md and the installed Remotion guidance. Confirm the assets in public/ exist.
Summarize the opening's layout, event frames and curves. Flag conflicts or missing inputs.
Build only stage 1. Return stills, the moving preview, changed files and checks run. Wait for approval.
```

## 6. Six controls from a reference

```text
Recreate the motion in reference.mp4 with our assets in Remotion + GSAP (useGsapTimeline, frame clock).
Expose the animation as six controls in motion.config.ts: spacing, typography, motion blur, easing,
follow-through, anticipation. Add holds and beats. Every magic number in the scene reads from the config.
Then I will give feedback like "add 4 frames of anticipation" or "delay the tail by 3 frames";
change only that value, draft render, and show me the frames that changed.
```

## 7. Blender sets, props, characters (2.5D)

```text
Build SET [2] ([throne room]) for a vertical 2.5D short in Blender through MCP. Stop after each pass.
Pass 1 BLOCK: grey shapes for the architecture. Place every camera from shotlist.md. Show me a look sheet.
Pass 2 LIGHT: one motivated key (windows), warm practicals (candles, torches), a Poly Haven HDRI for fill.
  No 3-point studio lighting. Stills from 3 cameras.
Pass 3 DRESS: worn, varied materials, small props. Depth of field on every camera, focused where the character stands.
Render size [1080x1920]. Never render bigger and crop.
```

```text
Build the hero prop: [object + reference]. Real-world scale, bevelled edges, wear in the crevices.
Render a 6-angle look sheet on a neutral floor, then place it in SET [2] at the mark.
```

```text
Design [character, role, era] as separate SVG parts on transparent backgrounds: one round head, body, arms, legs, costume.
Expression sheet, one file each: eyes (neutral, wide, narrow, closed, side-look), mouth (closed, small open, wide open,
frown, grin), extras (sweat drop, blush). Flat colour, one thick dark outline, no gradients or texture.
Export PNG at 2x, import every part into Blender as planes on one rig with an emission (shadeless) material
and a soft contact shadow under the feet.
```

## 8. Captions from word timings

```text
Read captions/voice.json (word timestamps). Build the caption layer in Remotion:
1-2 words on screen at a time, each card cut exactly on its word's start; bold white, dark outline, centre-low,
inside the 9:16 safe area. Animate only stressed words. Then line up every shot cut with the word it belongs to.
```

## 9. Audio brief (audio.brief.json)

```json
{
  "vo": {
    "model_id": "eleven_v4",
    "text": "[excited] Got an idea? [Product] makes it a VIDEO. In seconds!",
    "voices": {"option_a": "[voice_id]", "option_b": "[voice_id]"},
    "voice_settings": {"stability": 0.3, "similarity_boost": 0.75, "speed": 1.05},
    "takes": 2
  },
  "music": {
    "model_id": "music_v2_5",
    "composition_plan": {
      "chunks": [
        {"text": "[Drop]", "duration_ms": 3500, "positive_styles": ["UK garage x hyperpop jingle", "136 bpm", "punchy 2-step drums", "starts at full energy, no intro"], "negative_styles": ["slow intro", "fade in"]},
        {"text": "[Hook]\n[Brand]! [Brand]!", "duration_ms": 3000, "positive_styles": ["catchy sung gang-vocal hook", "big final hit"], "negative_styles": ["fade out"], "context_adherence": "high"}
      ]
    },
    "seeds": [7, 21],
    "fallback_prompt": "A 6 second high-energy jingle for a social ad, 136 bpm, punchy drums from beat one, ends on a big hit, instrumental only"
  }
}
```

## 10. Sound review

```text
Listen to out/mix.wav with earshot and check it against shotlist.md and cues.json:
- the voice is always on top; music sits under it
- a sound only where something physical happens
- no whoosh more than twice; one swell at most
- every cue lands on its frame (sheet markers green)
- the music resolves on the final frame
- integrated loudness about -14 LUFS, true peak at or under -1.5 dBTP
Quote earshot's numbers for every claim. List problems with timestamps and the fix for each.
```

## 11. Critic pass (fresh context)

```text
Open review/contact.png, review/phone.png and review/frames.jpg, plus BRIEF.md.
You did not make this. You are the harshest motion director I know.
Check: is every line readable at phone size? Does any shot last longer than 3 s without a new idea?
Does anything overlap, crop or sit under platform UI? Is any claim missing from sources.md?
[2.5D: does every character sit on the floor? Is any character lit like a 3D object?]
List every problem with a frame number. Pick the 3 worst. For each, write a four-field correction
(WHERE / PROBLEM / CHANGE / KEEP) and the frames to re-render as proof.
```

## 12. Variants

```text
Make [3] variants of [the end card | the hook | the CTA] that differ in ONE idea each
(e.g. [plate wipe], [social proof with rating], [tile wall]). Same brand tokens, same duration, same audio cue frames.
Render each with a contact sheet, then a side-by-side against the reference. Name files <piece>-<variant>-v<n>.
```

## 13. Package the loop

When a format works end to end, save it as a project skill so the next piece is one line: the pipeline steps with "wait for my OK" gates, the hard rules, the house rules, the config template and the review commands. Keep script.md, sources.md, shotlist.md, the cue list and the approved export together in one folder: that folder is the template for the next one.
