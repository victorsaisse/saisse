# Audio: voice, music, SFX, mix

Most AI films look done with the sound off. Turn it on and the cuts miss the beat, the hits land late and the music ends before the picture does. Treat sound as half the piece.

**Order:** script -> voice -> word timings -> music -> picture beats -> SFX cues -> mix -> listen with earshot. The voice and music timings decide the cuts, never the other way round.

## 1. Voice

### Pick the source
- **Your own recorded voice** for story, explainer and founder content. Real breaths are the biggest trust signal and avoid any voice-clone questions ([Dumble](https://x.com/Y0urDumble/status/2115164938884862375)).
- **ElevenLabs** for ads, demos, product films, multiple languages, or when nobody can record. Victor's default for narration and VO.

### ElevenLabs models (checked Nov 2026, [models page](https://elevenlabs.io/docs/models))

| Model id | Use it for |
|---|---|
| `eleven_v4` | Default for narration and VO: most expressive, audio tags, multi-speaker dialogue, 10k chars |
| `eleven_v4_turbo` | Real-time / low latency (~100 ms) with audio tags |
| `eleven_multilingual_v2` | Long-form, stable reads; better at numbers than Flash |
| `eleven_flash_v2_5` | Cheapest and fast (~100 ms), 32 languages, 40k chars |
| `eleven_v3` | Previous gen with audio tags (what Victor's studio used through fal) |
| `scribe_v2` | Speech to text with word timestamps (find sung words, align captions) |
| `music_v2_5` | Music and jingles, vocals or instrumental, 3 s to 5 min |
| `eleven_text_to_sound_v3` | Text to SFX, 0.1-30 s, loopable |
| `eleven_ttv_v3` | Design a new voice from a text description |

### Directing the read
- **Audio tags** (v4, v3): `[excited]`, `[whispers]`, `[sighs]`, `[laughs]`, `[sarcastic]`, `[curious]`, `[mischievously]`, `[strong British accent]`, plus effects like `[applause]`. Match tags to the voice: a serious voice ignores playful tags. Describe quality (`[low, gravelly voice]`) rather than ambiguous cues. Tags combine: `[excited][rushed] Got an idea?` worked for a 5 s hype VO.
- **No SSML breaks on v4/v3.** Use punctuation and structure: ellipses for weight and hesitation, dashes for short pauses. `<break time="1.5s" />` works on other models (max 3 s; too many cause artefacts).
- **Emphasis:** CAPS on the stressed word ("a VERY long day").
- **Speed:** voice setting 0.7-1.2 (default 1.0).
- **Stability:** lower = more expressive and variable (0.3 suited a hype ad), higher = consistent narration. `similarity_boost` 0.75 default, `style` 0 default.
- **Numbers and symbols:** write them as spoken ("forty-two dollars"), expand abbreviations. v4 takes IPA between slashes for names: `/ˌbaɪoʊˈkemɪstri/`.
- **Consistency across takes:** pass a `seed`. For long scripts in chunks, pass `previous_text` / `next_text` so prosody flows between requests.
- **Delivery directions get spoken aloud** if written as prose ("she said excitedly"). Use tags, or trim them in post.
- **Casting:** generate 2 takes for each of 2-3 candidate voices and pick by data: the shortest speech span and widest pitch range read as the most energetic (`tools/vo_report.py` in Victor's studio measures both). Example brief that worked: "young British woman, very enthusiastic, very fast".

### Word timings
- Ask for timestamps at generation: `POST /v1/text-to-speech/{voice_id}/with-timestamps` returns `audio_base64` plus `alignment` (`characters`, `character_start_times_seconds`, `character_end_times_seconds`). Group characters into words.
- v3-style expressive takes stretch timestamps across pauses. Fix: tighten the take first (shrink every pause over ~70 ms to 70 ms, 6 ms fades at each cut), then re-time the final file with **forced alignment** (audio + transcript -> word times). Victor's studio: `tools/tighten.py` then `scripts/align.mjs`.
- Write the word frames into the config's `beats` (`frame = round(seconds * fps)`). Cuts and slams land on those frames.
- For recorded voice or finished songs, use Whisper (`--word_timestamps True`) or `scribe_v2`.

### Access
- Official skills: `npx skills add elevenlabs/skills` (text to speech, speech to text, sound effects, music, voice changer, voice isolator, dubbing, setup). They need `ELEVENLABS_API_KEY`.
- Official Claude Code plugin (skills + hosted MCP, signs in with your ElevenLabs account, no key): `/plugin marketplace add elevenlabs/plugin` then `/plugin install elevenlabs@elevenlabs`. Hosted MCP URL: `https://api.elevenlabs.io/v1/mcp`. The old `elevenlabs/elevenlabs-mcp` (uvx) is archived. Docs index for agents: `https://elevenlabs.io/docs/llms.txt`.
- Direct API: header `xi-api-key: $ELEVENLABS_API_KEY`. SDKs: `elevenlabs` (Python, JS) and the `elevenlabs` CLI (`elevenlabs music compose --prompt "..." --music-length-ms 10000 --model-id music_v2_5 --output music.mp3`).
- Through fal.ai when only a fal key is available: `fal-ai/elevenlabs/tts/eleven-v3` (text, voice, stability, timestamps), `fal-ai/elevenlabs/forced-alignment`, `elevenlabs/music/v2.5`. Victor's studio runs this way (`scripts/lib/fal.mjs`).
- Keys: read from the environment at runtime. Never print a key, never write it into a repo, never paste it in chat.

## 2. Music

- A prompt must settle five things or the model picks the average: **genre, mood, instrumentation, tempo (BPM), production era**. Add key ("in A minor"), vocal delivery words (breathy, belted, gang vocal) and production terms (tape saturation, plate reverb).
- Narrate the arrangement in order ("start with", "then bring in"), mark silences, and give timing cues ("lyrics begin at 2 s"). Say "instrumental only" when you need no vocals.
- Short jingles: use a **composition plan** with chunks and exact `duration_ms`, and ban intros and fades: `negative_styles: ["slow intro", "fade in", "fade out"]`, `positive_styles: ["starts at full energy, no intro", "big final hit"]`. Template in `prompts.md` section 9.
- Generate 2-3 seeds plus an instrumental fallback, then pick with earshot (`rank`, `ab`).
- Lock picture to the song: find sung words with `scribe_v2` and put the logo slam on the sung brand word.
- The music resolves on the final frame. Trim or re-time so the last hit and the last picture change coincide.
- Copyright filter: a rejected prompt returns `prompt_suggestion`; a rejected plan returns `composition_plan_suggestion`. ElevenLabs Music is cleared for most commercial uses depending on plan ([music terms](https://elevenlabs.io/music-terms)).
- Free alternative: HyperFrames ships a library of 10,000+ music tracks, SFX, stock images and TTS ([Dumble](https://x.com/Y0urDumble/status/2119023461267833047)).

## 3. Sound effects

### earshot (default for code-made films)
[earshot](https://github.com/Leonxlnx/earshot) by Leon Lin gives an agent ears (listen, review, measure) and a bank of sounds made in code (201 in v0.3, 252 in v0.4), CPU only, no licensing questions ([v0.3 announcement](https://x.com/LexnLin/status/2111927727318245426)). Install: `uv tool install "earshot[mcp] @ git+https://github.com/Leonxlnx/earshot"` then `earshot doctor --fetch` (~1.1 GB of models). Local copy in Victor's studio: `~/motion-studio/vendor/earshot` (venv at `.venv`).

- Kit presets: `earshot kit init --preset crisp -o audio/kit.json` (`calm` for text-led films, `crisp` for energetic product films, `minimal` for effects-only, `launch` for films cut to music). Render with `earshot kit render`, check with `earshot kit check`.
- Single cues: `earshot sfx list --category ui_pop` then `earshot sfx render ID -o x.wav [--pitch E5] [--dur 0.8 --apex 0.32] --json`. The JSON reports the measured onset and `peak_s`.
- Families that suit music-led launch films: `ui_pop` (chips, rows, bubbles arriving: the most used), `knock`, `cut_hit` (punchy sub hits), `mallet` (tuned glock / marimba notes), `drip`, `shutter` (snap zooms, whip-cuts), `reverse_swell` (one per film, peaks just after a cut), `whoosh_fast`, `riser`, `swish`, `glitch`, `notify`, `logo_sting`, `click`.
- **Peak alignment:** a soft lead-in shifts the audible hit. Place each file so its measured peak lands on the cue frame: `start = cue_frame / fps - peak_s`. Victor's `scripts/sfx.mjs` does this from the config's `sfx` list.
- Pitch tuned elements to the music's key (e.g. ascending `ui_pop/step` at C5, F5, G5, C6 for four items landing).
- Levels ride 3-10 dB over the music bed and read by timing and spectrum. Pull the music down 6-12 dB for 0.3-0.5 s before a big hit or drop and bring it back on the hit; do not duck under every effect.
- Runs keep their gaps (pops about 114 ms apart, key taps about 87 ms). Typing gets 12+ takes so it never machine-guns.

### Cue rules
- A sound only where something physical happens or a cut needs a hit.
- No whoosh more than twice. One riser or reverse swell per film at most. The lockup gets one clean hit (sub + spark sting).
- Write cues next to the picture events, in frames:

```text
CLICK          frame 195   click transient
REVEAL         frame 210   reveal accent
LOGO SLAM      frame 121   cut_hit/sub + logo_sting/spark
FINAL HOLD     frame 438   music settles
```

### Other sources
- Recorded realism required: earshot's `real-sound` skill, or ElevenLabs `eleven_text_to_sound_v3` (describe action, setting and performance: "footsteps on gravel, then a metal door opens"; useful words: impact, whoosh, braam, glitch, drone, one-shot, loop).
- Check any downloaded or generated candidate with `earshot rank`, `find` (clean trims), `realness` and `ab` before using it.

## 4. Mix

Victor's studio mix (ffmpeg `filter_complex`, see `scripts/mix.mjs`):

1. Resample everything to 48 kHz stereo. VO at 0 dB, song around -3 dB, SFX at their cue gains (typically -3 to -16 dB).
2. Duck the song under the VO with a sidechain: `sidechaincompress=threshold=0.02:ratio=9:attack=8:release=220` (ratio about the dB of ducking you want).
3. `amix=normalize=0` (keep levels), trim to the picture length, 0.12 s fade at the very end.
4. `loudnorm=I=-14:TP=-1.5:LRA=11`, then `alimiter=limit=0.89`. Export 24-bit WAV, mux into the render.

Targets: about -14 LUFS integrated, true peak at or under -1.5 dBTP for social and YouTube. Check the loudest event, the quietest useful cue and the ending on headphones and a phone speaker.

## 5. Review with earshot

Never claim how something sounds without a reading. Quote numbers ("strong match, z +3.9; -14.2 LUFS").

- `earshot timeline film.mp4 --cues cues.json`: events, loudness, sync per cut (negative offset = early).
- `earshot sheet film.mp4 -o sheet.png --cues cues.json`: open the PNG; green = on the frame, amber = within 100 ms, red = missing.
- `earshot critique film.mp4`: scorecard against professional launch films.
- CLI calls each load models (~20-40 s). For many calls use the MCP server: `claude mcp add earshot --scope user -e EARSHOT_THREADS=3 -- earshot mcp` (or point at a venv: `-- /path/to/earshot/.venv/bin/python -m earshot mcp`). Start Claude with `MCP_TIMEOUT=60000` if the tools do not show.

## 6. Captions and lip sync

- Captions come from word timings (Whisper or Scribe JSON): 1-2 words per card, cut on word starts, bold white with a dark outline, inside the safe area, animate only stressed words.
- Lip sync on generated characters: see `formats.md` (ads). Mouth movement that misses the words is the first thing viewers notice and the biggest quality gap against top ads. Fix it before shipping anything with a talking face.
