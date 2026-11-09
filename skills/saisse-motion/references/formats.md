# Formats: recipes per deliverable

Pick the closest format, copy its spec into the brief, then run the loop in `SKILL.md`. Platform sizes at the end.

## 1. 5-second vertical product demo or hype ad

Spec: 1080x1920, 30 fps, 150 frames, VO + sung hook + SFX, -14 LUFS. Worked example in `victor-notes.md`.

```text
f0-12    hook words slam in (kinetic type, 150 px, heavy, slight slant)
f12-44   product name pill whips in; prompt types out (~3 chars/frame)
f44-75   "makes it a VIDEO": result cards fan up from below with stagger 2 f, shutter clicks
f75-106  "in SECONDS": film strip or rapid result flashes, riser into the hook
f106-129 sung brand name; logo drops with 5 f anticipation and slams on the sung word (sub hit + sting + flash)
f130-150 tagline + URL, 24 f lockup hold
```

VO: 10-14 words, `[excited]` style tags, stability around 0.3, 2 takes x 2-3 voices, pick by measurement, tighten, align. Music: two-chunk composition plan (drop, then sung hook), no intro, big final hit.

## 2. 20-second launch clip (one feature)

Spec: 1080x1080, 30 fps, 600 frames. Opening 0-119 (headline + card enter, hold), product 120-419 (cursor -> press at 195 -> result at 210, readable by 234, hold), closing 420-599 (logo + one call to action). Full BRIEF.md template in `prompts.md` section 5; sales package in `business.md`.

Review stills: 0, 18, 42, 60, 119, then 120, 195, 234, 419, 420, 540, 599. Sound: click on the press frame, reveal accent on the result frame, music settles in the closing hold.

## 3. Home-page hero loop (site or app)

Spec: muted autoplay loop, 5 s, sized to the slot (ask: 16:9, 1:1 or 9:16?), MP4 + WebM + poster, a few hundred KB to 2 MB.
- Story in three beats: brand mark in, the product doing its job (cards in the page's own art direction), the outcome; then a hold that flows back into frame 0.
- Use the page's exact tokens, fonts and assets. Motion stays calm enough to sit behind a headline.
- No VO and no music in 5 s of UI. If it ever plays with sound, SFX only, on beats.
- The last frame must match the first (or crossfade into it) so the loop has no jump.

## 4. UGC side-by-side ad ("your photo" -> AI video)

The format that won in Victor's Nov 2026 ads, replicated from a competitor's top performers with original characters.

Layout (1080x1920, black background):
- Top row: **YOUR PHOTO** (a static, real-looking phone photo) left, **VIRAL VIDEO** (the trend clip with lookalike performers, never the real artists) right.
- Bottom two thirds: **AI GENERATED VIDEO** (the person from the photo performing the trend).
- Labels: white caps, bold sans (Verdana Bold ~54 px), ~5 px black stroke.
- Two layout variants: square panels with gaps (photo and viral ~453x609 each at y 96, AI 1080x1020 at y 803), and rounded no-gap (photo and viral ~507x681 at y 75, AI ~958x1088 at y 804, 120 px corners, mask supersampled 4x for clean edges).

Timing: ~14.5 s of result, cut on a strong move, then a ~3 s end card. No VO, no captions, no hook text: it is a demo from frame 1. The trend song at about -14 LUFS.

Why it converts: instant demo, trend recognition, second person ("YOUR photo"), casting works as targeting, the payoff never stops, and the ad matches a one-tap template in the app. The advertiser's top ads changed only the casting.

Casting: cast to the audience the ad targets; saturated outfits that pop against the brand colour (nobody wears the brand colour); 3/4-length framing or wider; the photo's clothing carries into the video; if roles flip, mirror the photo. Use original characters (character sheets), never real people's likenesses.

"Your photo" prompt (image model, 3:4, 2K, then crop waist-up):

```text
Unedited iPhone photo taken by a friend standing a few steps away. [person, outfit, place].
Flat natural daylight in open shade. Deep depth of field: the whole background is sharp, no portrait-mode blur.
Slightly crooked amateur framing. Real skin with pores. Relaxed expression, no posed smile. No colour grading.
```

Character sheet prompt pattern: 16:9, plain light-grey studio backdrop; top row three expressions (mid-word with mouth open, big laugh showing teeth, confident smirk); bottom row four full-body views; no text, no labels.

Pipeline:
1. **Research.** Download the reference ads (Ad Library). Dedupe by hash. Find cuts with `ffmpeg -vf "select='gt(scene,0.30)',showinfo"`; make 2 fps timestamped sheets. Transcribe lyrics (Whisper). Locate the exact song window by cross-correlating the ad audio with the source track; estimate BPM.
2. **Characters and stills.** Character sheets, then stills that keep the reference's exact framing, crop, angle, scale and poses, with mouths open mid-lyric and lively eyes.
3. **Motion transfer.** Drive the stills with the reference performance clip (motion-transfer / character-replacement model), short prompt (40 words or fewer), 2 takes per cast. For two performers: "Replace both performers in every shot", plus a still for the second person.
4. **QA.** Strip of driving clip vs takes at 2 fps; prop sweep every 0.5 s; splice clean takes at the reference's hard cuts; patch broken frames with the same person from another shot.
5. **Lip sync.** Isolate vocals on the exact window (Demucs `--two-stems=vocals`), run a lip-sync model on the result (Victor used `fal-ai/sync-lipsync/v2/pro`, `sync_mode: cut_off`, about $1.25 per 15 s clip). Score it: MediaPipe Face Landmarker per frame (IMAGE mode), inner-lip gap / mouth width vs the original performer, Pearson r with a +-6 frame lag search. Keep the better take per shot (synced on rap shots, unsynced where the face is vibing or turned away). Typical gain: r 0.2-0.4 -> 0.4-0.6.
6. **End card.** Alpha card overlaid 6 frames before the cut (section 5).
7. **Assembly.** ffmpeg composite of the three panels + labels + card + song at -14 LUFS; x264 CRF 17, AAC 192k, `+faststart`. Extra cuts for 4:5 and 1:1 with a blurred fill.
8. **Variants.** 3 per cast (square + end card A, square + end card B, rounded + end card C), all three in one ad so the platform finds the winner. Read the winner with a breakdown by media.
9. **Package.** Final folder per cast with variant suffixes, a comparison video (ours over theirs), a zip with README. Upload copies under 10 MB for Meta's browser uploader (two-pass ~3.9 Mbit/s, AAC 128k).

Legal: trending songs and lookalike performances need rights cleared for paid use. Do not copy the reference's fake ratings. Keep moderation-safe, neutral descriptions.

## 5. CTA end cards (alpha overlays)

Spec: 1080x1920, 30 fps, 96 frames (3.2 s), rendered as ProRes 4444 with alpha so frame 0 is transparent and the card plays over the ad's last frames.
- An accent-colour slab whips across (clip-path) and covers the cut at card frame 6; the ad cuts underneath it; the card is revealed behind.
- Logo drops ~200 px and slams around frame 18; headline lines rise; button at ~32; 36-frame lockup hold.
- Values that shipped: anticipation 4 f, overshoot 1.1, settle 10 f, stagger 3 f, shutter 180 with 5 samples; headline 128 px, weight 800, -0.04 em tracking.
- Variant ideas that worked: **Plate** (logo slam + "YOUR PHOTO. ANY TREND." on a hero plate + "Make yours"), **Proof** (app-icon ring reveal + one benefit line + a verified rating + "Try it now"), **Wall** (wall of the campaign's cast tiles + "ANY TREND. YOUR FACE." + "Make yours").
- Rules: no URL line without the logo, no unverified ratings, one SFX stem per card with peaks on frames (whoosh on the whip at 6, sub hit on the slam, soft pop on the button).
- Overlay recipe: `ffmpeg-cookbook.md`, "Overlays and end cards".

## 6. 2.5D story or explainer short

Spec: 1080x1920, 30 fps, 55-70 s, one true story, one narrator, flat 2D characters inside lit 3D Blender sets, a new shot about every 2 s, 1-2 word captions. Source: [Dumble's course](https://x.com/Y0urDumble/status/2115164938884862375).

Pipeline (each step waits for approval):
1. Pitch 5 true stories with 2 sources each; pick one.
2. Script 150-170 words + sources.md.
3. Shot list: one shot per idea, 3-5 reusable sets.
4. Sets in Blender via MCP: BLOCK -> LIGHT -> DRESS, look sheet after each pass.
5. Hero prop in 3D with a 6-angle sheet. Characters as SVG parts on planes, shadeless, with contact shadows.
6. Acting line per shot, then animate.
7. Voice (yours, or ElevenLabs) -> Whisper word timestamps -> captions -> mix checked with earshot.
8. Contact sheet + phone test -> critic pass -> fix the 3 worst -> repeat.
Hard rules: render at size, true claims only, never copy another channel's characters, designs or scripts. Prompts in `prompts.md` section 7.

## 7. AI model demo reel

For a new video model card, launch post or comparison.
1. Research what the model is best at (official page, provider docs, top community clips). Compare provider prices for the configs you need.
2. Write ~10 prompts that play to those strengths. For realistic models: bright, beautiful, high contrast, real people, POV, drones, action, like a GoPro commercial.
3. Generate short clips (4-5 s) at a cheap resolution (480p-768p), 16:9 or the card's ratio. Script the generation (`.sh` with the key from env) and download to one folder.
4. Pick the best 2 s of each (contact sheet first), stitch in order, strip audio, compress (often 2 MB max). Recipes: `ffmpeg-cookbook.md`, "Cut, trim, stitch".
5. Save the prompts with the outputs so the reel can be extended.

## 8. Looping preview card (5 s, muted)

For model cards, galleries, paywalls. Many generated images flashing fast (about 5 per second), or quick 1 s clips; 16:9 unless the slot says otherwise; muted; compressed; it plays forever, so make the loop point invisible. Slow zoom on stills does not work here.

## 9. Product walkthrough timelapse with music

1. Record the real product flow at 24-30 fps while doing it (only the relevant tab or window).
2. Plan the story first: consistent scenes that make sense, shorter shots, verify every generated output in the product.
3. Speed up with idle frames dropped (`mpdecimate`), cut dead time.
4. Music and SFX: cut properly, transitions on cuts, balanced levels, hits on events; check with earshot.

## 10. Presentations and decks with motion

- Local HTML deck (one file, light theme by default), animated with Motion or GSAP for state changes and diagrams. Never published to a cloud artifact unless asked.
- One idea per slide, very visual, two or three bullets maximum, numbers big with small labels.
- Written for the audience: only what they should see. A 1-2 minute update needs 5-8 visuals.
- Animations explain (before -> after, flow, growth), they do not decorate. Respect reduced motion.
- If the deck becomes a video: record it, or rebuild key slides in Remotion with the same tokens.

## 11. Screen-recorded demo (app review, docs, support)

Record only the needed tab or window, good resolution, steady fps. Script the path first; cut to only what is needed; add short captions or callouts for each step; keep it under the reviewer's limit; upload where asked (unlisted when it is for a review).

## Platform sizes

| Placement | Size | Notes |
|---|---|---|
| TikTok, Reels, Shorts, Stories | 1080x1920 (9:16), 30 fps | Keep text and CTAs out of the bottom fifth and the right-edge button column; check with a safe-area overlay |
| Meta feed | 1080x1350 (4:5) or 1080x1080 | Make 4:5 and 1:1 cuts of vertical masters with a blurred fill |
| YouTube, landing pages, decks | 1920x1080 (16:9) | |
| Launch posts on X, product pages | 1080x1080 or 1920x1080 | Square reads well in feeds |
| Web loops | slot size, 720p max for backgrounds | muted MP4 + WebM + poster |
| Loudness, all social | about -14 LUFS, true peak at or under -1.5 dBTP | |
