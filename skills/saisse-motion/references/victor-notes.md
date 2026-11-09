# Victor's notes: preferences and lessons from past sessions

Rules that came out of Victor's own motion and video work in Claude Code (March to November 2026). When one of these conflicts with a generic tip elsewhere, this file wins.

## How he wants the work run

- **Plan and cost before spending.** Before generating anything that costs credits or takes long renders, send the plan: what you will make, how many outputs, the models, the total cost. Wait for approval.
- **Variants to choose from.** "It needs to be perfect, so do a few variations I can choose from." Default to 3 options that differ in one idea each. Park the ones he rejects; do not keep polishing them.
- **Status he can act on.** Where you are, what is next, a time estimate. Short.
- **Outputs in `~/Downloads`** with clear names (`<piece>-<variant>-v<n>.mp4`). Keep research and final deliverables in separate folders. When he will hand work to someone else, zip it with a README and folders that explain themselves, and test the zip.
- **Side-by-side review files.** When matching a reference, build a comparison video (ours next to theirs, labelled, with a timecode) so he can pause and compare.
- **Keys are read, never printed.** "Don't output, just read it." Read from env or the project's secret file at runtime. Check that a value is a real secret key and not a key ID before debugging auth for an hour.
- **Measure, then let his ears decide.** The agent cannot hear: pick voices and mixes by measurements (speech span, pitch range, LUFS, earshot readings), then present the pick with the numbers. He makes the final call by listening.
- **Do your best = the bar is high.** A first version that merely works gets "this is so bad". Iterate with sheets before showing.

## Taste, from his feedback

- **Fast, dynamic, modern, launch-video energy.** A slow zoom over still images was rejected outright. For a looping preview card, flash many images quickly (about 5 per second) instead; it will play in an infinite loop, so design for the loop.
- **Demo reels for video models:** "straight out of a GoPro commercial": very bright, beautiful, intriguing, high contrast, real people, POV, drone shots. Short clips, best 2 s of each, stitched, muted, small file.
- **Lip sync is non-negotiable** on any talking or singing face. He minds bad sync far more than cuts or close-ups. Once sync is right, close-ups look great; no need to re-cut wide.
- **Expressive characters.** Push emotions a bit further than feels natural; flat faces read as AI.
- **"Your photo" must look like a real phone photo,** not AI: no big posed smiles, no portrait-mode blur, no warm cinematic light. See the prompt in `formats.md` (UGC ad).
- **Consistency across generated shots:** the same face in every shot, environments that make physical sense (a driver sits in the car, not inside the terminal), cuts set up properly. Shorter scenes (5 s) validate better than 10 s ones. Generate batches of 4 at 720p and check them before moving on.
- **Brand from the real source.** Pull design tokens, colours, fonts and assets from the product's actual site or monorepo and get heavily inspired by them. A plain URL line without the logo "looks weird": drop it or use the logo.
- **No fake claims.** No invented ratings or stats on end cards; confirm any rating or number is current before it ships.
- **Editing with music and SFX:** cut properly, add transitions, balance volumes, time every hit. "We are editing the best video ever."
- **Presentations:** very visual, two or three bullets at most per visual, easy to talk over in one to two minutes. Content only the audience should see (never personal notes in a deck for someone else). Light theme by default.
- **Screen-recording demos** (app reviews, walkthroughs): good quality, only the needed cuts, record only the relevant tab or window.
- **Delivery jobs come constantly:** compress to a size cap (500 KB to 40 MB), strip audio, 720p, web or CDN optimised, replace originals, rename in sequence, flatten folders. Use the `fit` recipe in `ffmpeg-cookbook.md` and report final sizes.

## What shipped and how (worked examples)

### 5-second vertical product demo (Oct 2026)
1080x1920, 30 fps, 150 frames. Brief: very fast, an enthusiastic young British woman, a sung jingle, earshot SFX.
- VO: ElevenLabs v3 through fal, stability 0.3, tags `[excited][rushed]`, script cut from 9 s of words to "Got an idea? [Product] makes it a video. In seconds!". Three voices x 2 takes (Lily, Alice, Alicia). Picked by `vo_report.py` (shortest speech span, widest pitch range): Lily take 2.
- Tighten: energy gate -34 dB, pauses capped at 0.07 s, 6 ms fades, then ffmpeg `atempo=1.06`: 4.68 s -> 3.39 s. Re-timed with forced alignment, words written into `beats`.
- Music: ElevenLabs music v2.5 composition plan, two chunks (`[Drop]` 3.5 s UK garage x hyperpop at 136 bpm, no intro; `[Hook]` 3 s sung brand name, young British female vocals, context adherence high), seeds 7 and 21 plus an instrumental fallback. Sung words found with Scribe v2; the logo slams on the sung "loop" at frame 121 with a flash, ring and shake.
- Picture: one `useGsapTimeline`, every tween keyed to `config.beats`; typing and counters from `useCurrentFrame`; camera motion blur 200 degrees, 6 samples; TikTok Sans variable at 150 px, -0.035 em, weight 900, slant -10, width 112.
- SFX: 22 earshot cues between -3 and -16 dB: pops, typing clicks, one fast whoosh, a four-note `ui_pop/step` arpeggio (C5 F5 G5 C6), a punch hit, shutters, one riser, and reverse swell + sub hit + spark sting on the slam.
- Mix: song -3 dB ducked under VO (sidechain ratio 9), loudnorm to -14 LUFS, true peak -1.5. Delivered at -14.7 LUFS, 4.6 MB, 5.01 s. Generation cost: a few cents.

### 5-second home-page hero for a Shopify app (Nov 2026, planned)
Muted autoplay loop: brand mark in with a glass badge (0-1 s), three cards telling the product story in the page's art direction (1-3 s), a "setup complete" path filling (3-4.3 s), logo hold. SFX only on beats if it ever plays with sound; no VO or music in 5 s of UI. Before building, ask: home-page hero (loop, sound off) or social clip? Which aspect ratio?

### UGC-style paid ads with lip sync and end cards (Nov 2026)
Replicated a competitor's top-performing format with original characters. Full recipe in `formats.md` (UGC side-by-side ad). Highlights: casting is the only variable across that advertiser's top ads; lip sync measured and fixed per shot; three end-card variants per cast; Meta's browser uploader caps files at 10 MB.

## Technical pitfalls already paid for

| Pitfall | Fix |
|---|---|
| GSAP `fromTo` shows its from-state before it starts (white flash) | `immediateRender: false` |
| ElevenLabs v3 timestamps stretch over pauses; breaths pass a -38 dB gate | gate at -34 dB, tighten, `atempo` (not a phase vocoder), forced alignment |
| ElevenLabs music v2.5 returns 422 | the plan uses `chunks`, not `sections` |
| earshot peak location | `res.meta.peak_s` from `--json`; check `meta.checks.ok` |
| earshot pitch audit: E5 landed 84 cents sharp | retune to a note that lands (F5) |
| Contact sheet shows tile numbers, not frame numbers | draw the frame number before `select` |
| drawtext fontconfig errors | pass `fontfile=` explicitly |
| zsh eats `$s:e` / `$i:a` as modifiers | always write `${s}`, `${i}` |
| `vstack` / `hstack` fails on 1694 vs 1693 px | scale every input to the same even size |
| A motion-transfer model returns 14.708 s instead of 15 s | it normalises frame counts (8n+1 at 24 fps); not drift |
| Replacing two performers: only one swapped | "Replace both performers in every shot ..." plus a separate still of the second person |
| "hands empty" in a video prompt | caused extreme close-ups; describe the framing instead |
| Props morph (bracelets into phones) | QA strip every 0.5 s, splice clean takes, patch bad frames from another shot of the same person |
| Silent driving clip disables the model's vocal conditioning | run a separate lip-sync pass on the result |
| Lip-sync pass made one panel worse | score per shot and keep the better take per shot |
| Ads mixed at -9.8 LUFS vs the reference's -14 | bring the music down (-4.5 dB landed at -14) |
| Moderation blocks facial-feature words next to an ethnicity | use neutral wording; never work around a block |
| Meta Ads Manager offers AI copy with invented claims | decline; also remove the "related media" it re-adds after each swap; skip its auto-captions on songs |
| Meta browser uploader caps at 10 MB | upload copies: two-pass x264 ~3.9 Mbit/s, AAC 128k, ~8 MB |
| Chrome blocks multiple downloads | bundle files into one download |
