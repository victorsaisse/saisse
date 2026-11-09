# Toolkit

Every repo and tool from the sources, checked on GitHub on 2026-11-09 (stars, licence, install command, catches). Skills are code that runs with your agent: open the repo and read it before installing. Pick the smallest set the piece needs.

## Which stack for which job

| Job | Start with | Why |
|---|---|---|
| Product demo, launch film, feature clip, CTA end card, kinetic type | **Remotion + GSAP** (+ earshot, ElevenLabs) | Frame-exact, React components (your real UI), one config, alpha renders. Victor's default. |
| HTML-first video, slideshow, quick brand piece, Figma import, cloning a reference's motion | **HyperFrames** | Write HTML, get MP4. 450-block registry. `/reverse-any-video` rebuilds a reference (Pro) |
| Opinionated launch film with measured timing and sound built in | **cinetic** or **motionmaxxing** | Strong defaults, contact-sheet checks, lint for slop layouts, -14 LUFS mix |
| Product promo from your real pages, with an editor UI | **video-shotcraft** | 124 recipe cards, 150 shots, CapCut-style workbench |
| Many formats end to end (explainer, avatar, documentary, dub, podcast clips) | **OpenMontage** | 12 pipelines, works without keys. AGPL |
| Edit real footage: silences, captions, 9:16, ducking, platform exports | **ffmpeg-skill** | 42 verified FFmpeg tools, probe-first, contact sheets |
| Real 3D sets, props, claymation, 2.5D story shorts | **MCP for Blender** (+ blender-skills) | Claude drives Blender and looks at its own viewport |
| 3D on the web: exploded machines, games, product viz | **anatomy**, **threejs-game-skills**, **3dviz-pro-max** | Interactive SVG/WebGL/three.js |
| UI micro-animation, app onboarding motion, AE-compatible files | **Lottie** (text-to-lottie, LottieFiles skill) | Small vector files, live player |
| Painted or hand-drawn look | **opus-video-skills**, **ClaudeAnimationBase** | p5.brush watercolour, kinetic reels |
| Stylised short films in code | **lemo-opuscar** | 66 film styles |
| Voice, narration, music, SFX | **ElevenLabs** + **earshot** | See `audio.md` |
| Captions from speech | **Whisper** or ElevenLabs Scribe | Word timestamps |

Victor's local studio: if `~/motion-studio` exists, use it first. It is a working Remotion + GSAP + earshot + ElevenLabs studio with `render`, `sfx`, `mix`, `align`, `stills` scripts and a `motion-studio` skill. Read its `README.md` and `studio/CLAUDE.md` before starting.

## Video frameworks

### Remotion + official skills
- [remotion-dev/remotion](https://github.com/remotion-dev/remotion) (72k stars). React components rendered frame by frame. New project: `npx create-video@latest` (or `npx create-video --yes --blank my-video`).
- Skills: `npx skills add remotion-dev/skills` (or `npx remotion skills add` inside a project). 13 skills: `remotion-best-practices` covers the rest (create, markup, studio, render, maps, captions, saas, interactivity, docs, license, upgrade, multimedia). [Remotion AI docs](https://www.remotion.dev/docs/ai/skills)
- GSAP on Remotion's frame clock: `@remotion/gsap` + `useGsapTimeline`. [Docs](https://www.remotion.dev/docs/gsap/use-gsap-timeline)
- Motion blur: `@remotion/motion-blur`. Fonts: `@remotion/fonts`.
- **Licence:** free for individuals, non-profits and for-profit companies with up to 3 employees. A company licence is needed from 4 employees (price on remotion.pro). Check before client or company work.

### HyperFrames (HeyGen)
- [heygen-com/hyperframes](https://github.com/heygen-com/hyperframes) (81.6k stars, Apache-2.0). HTML, CSS and seekable animation -> MP4 via headless Chrome + FFmpeg. Node 22+.
- Skills: `npx skills add heygen-com/hyperframes` (`--all` for all 9). Plugin: `claude plugin marketplace add heygen-com/hyperframes` then `claude plugin install hyperframes@hyperframes`.
- CLI: `npx hyperframes init my-video`, `preview`, `render`. Refresh skills to match the CLI: `npx hyperframes skills update` (`skills check` reports stale ones).
- Skills: `/hyperframes`, `/hyperframes-cli`, `/hyperframes-registry` (~450 blocks, e.g. `npx hyperframes add data-chart`), `/media-use` (get or make music, SFX, images, icons, voiceover, transcripts, cutouts and colour looks, with HeyGen or free local tools; Dumble's post counts 10,000+ music tracks and 75,000+ stock images), `/figma`, `/slideshow`, `/remotion-to-hyperframes`.
- `/reverse-any-video`: rebuilds any video as an editable project, checked frame by frame (`npx hyperframes reverse start <video> --dir <project>`, then `measure cuts`, `measure frames`, `diff`). **HyperFrames Pro only** (free accounts get an upgrade link). `/style-sampling` is also Pro. Studio Pro is $25/month.
- Local renders are free with no account. Telemetry is on by default: `npx hyperframes telemetry disable`.

### cinetic (uses earshot)
- [Leonxlnx/cinetic](https://github.com/Leonxlnx/cinetic) (MIT). Launch films from code, Remotion by default or HyperFrames. 23 text animations, 466 devices, music search (Mixkit, CC0).
- Install: `npx skills add Leonxlnx/cinetic -g -a claude-code -y`. Needs Node 22+, ffmpeg 6+, Python 3.11+, Chromium and earshot 0.4+ (`EARSHOT_PYTHON`).

### motionmaxxing
- [Tejashmakwana/motionmaxxing](https://github.com/Tejashmakwana/motionmaxxing) (Apache-2.0). Launch films and kinetic type in plain HTML on a GSAP runtime, timed from 53 measured professional moments (~4,300 moves, 12 eases).
- Install: `git clone https://github.com/Tejashmakwana/motionmaxxing ~/.claude/skills/motionmaxxing && bash ~/.claude/skills/motionmaxxing/install.sh`, then `/motionmaxxing`.
- Checks: `look.py` (contact sheet), `lint.mjs` (flags corner labels and slide-deck layouts), `brand.mjs` (captures a site's brand), `mix.py` (-14 LUFS). Bundled GSAP is under GreenSock's own licence.

### video-shotcraft
- [Vincentwei1021/video-shotcraft](https://github.com/Vincentwei1021/video-shotcraft) (Apache-2.0). Cinematic product promos in Remotion from your real pages: 124 recipe cards, 150 shots, beat sync, sound design, frame checks.
- Install: `npx skills add Vincentwei1021/video-shotcraft`. Editor: `node workbench/scripts/open.mjs <project>`.

### OpenMontage
- [OpenMontageInc/OpenMontage](https://github.com/OpenMontageInc/OpenMontage) (79k stars, **AGPL-3.0**; old name calesthio/OpenMontage redirects). 12 pipelines: animated explainer, animation, avatar spokesperson, character animation, cinematic, clip factory, documentary montage, hybrid, localization/dub, podcast repurpose, screen demo, talking head.
- Install: `git clone https://github.com/OpenMontageInc/OpenMontage.git && cd OpenMontage && make setup` (`make demo` needs no keys). Picks Remotion or HyperFrames per project.

### lemo-opuscar, opus-video-skills, ClaudeAnimationBase
- [lemomo-ai/lemo-opuscar](https://github.com/lemomo-ai/lemo-opuscar): 66 film styles made in code. `claude plugin marketplace add lemomo-ai/lemo-opuscar` + `claude plugin install lemo-opuscar@lemolab`. 30-60 min per film.
- [tuzhechen2005/opus-video-skills](https://github.com/tuzhechen2005/opus-video-skills): `painted-animation` (watercolour, beat cuts, karaoke lyrics) and `kinetic-reel` (kinetic type over three.js). `/plugin marketplace add tuzhechen2005/opus-video-skills`.
- [JohnHeibel/ClaudeAnimationBase](https://github.com/JohnHeibel/ClaudeAnimationBase): p5.js + p5.brush starter with 31 acted emotions. `npm install && node render.mjs --clip --out=out/video.mp4`.

## Motion taste and principles

- [LottieFiles/motion-design-skill](https://github.com/LottieFiles/motion-design-skill) (MIT): timing, easing, choreography, Disney's 12 principles for UI. `npx skills add LottieFiles/motion-design-skill`.
- [greensock/gsap-skills](https://github.com/greensock/gsap-skills) (MIT): official GSAP skills (core, timeline, scrolltrigger, plugins, utils, react, performance, frameworks). `npx skills add https://github.com/greensock/gsap-skills`. GSAP and its plugins are free for commercial use.
- [MotionVideo](https://motionvideo.xyz/en): paid skill pack (storyboard skills, scene starters, motion tokens, renderer guides for Remotion, HyperFrames, Editframe, fframes) plus a directory of AI-made motion videos with prompts. MCP: `claude plugin marketplace add motionvideohq/motionvideo-mcp`, then `/plugin install motionvideo` (search is free; move prompts need Pro). Its method: storyboard before scene code, motion tokens in one file, plain-word tweaks.
- Local skills worth loading when installed: `12-principles-of-animation`, `animation-vocabulary`, `emil-design-eng`, `improve-animations`, `text-to-lottie`, `motion-studio`.

## Editing real footage

- [kajisho5/ffmpeg-skill](https://github.com/kajisho5/ffmpeg-skill) (MIT, 4.9k stars). 42 structured FFmpeg tools for Claude Code, Codex and Cursor: cut, trim, merge with transitions, speed, B-roll, multicam, silence and filler-word removal, animated and karaoke captions, 9:16 reframing that keeps captions clear of platform buttons, noise reduction, ducking, loudness, LUTs, stabilisation, chroma key, HDR to SDR, platform presets (YouTube, TikTok, Reels, Shorts, X, LinkedIn, Facebook), FCPXML / EDL / OTIO export.
- Install: `npx ffmpeg-skill` (`--cursor`, `--codex`, `--all`). Check the machine: `npx ffmpeg-skill doctor`. Every tool takes `--dry-run`, `--json`, `--plan`. MCP advertises 12 core tools (`FFMPEG_SKILL_MCP_FULL=1` for all 42). Speech captions need `pip install faster-whisper`. Requires FFmpeg 6.1+, Python 3.10+.
- Its loop is the right one for any footage edit: probe -> plan -> process locally -> verify (duration, size, codecs, loudness, platform rules) -> inspect a contact sheet. Its own caveat: automated checks measure properties, not whether the cut is good. Review framing, caption readability and removed speech yourself.

## 3D and Blender

- [ahujasid/mcp-for-blender](https://github.com/ahujasid/mcp-for-blender) (MIT, 33.8k stars; was blender-mcp). Install: `uvx mcp-for-blender setup` (manual: `claude mcp add blender uvx mcp-for-blender` and `uvx mcp-for-blender install-addon`). Blender 4.2+ and uv.
  - Tools: `execute_blender_code`, `look` (viewport, camera, multi-angle sheet, animation strip; solid, material, rendered, wireframe, x-ray), `get_scene_info`, `search_assets` / `import_asset` (Poly Haven, Sketchfab, Poly Pizza), `generate_3d` (Tripo, Hunyuan3D, Rodin, Meshy).
  - `look` is the point: the agent checks its own work instead of guessing.
  - `BLENDER_MCP_SAFE_MODE=1` screens scripts. Telemetry on by default: `DISABLE_TELEMETRY=true`. Poly Haven assets are CC0 ([licence](https://polyhaven.com/license)).
- [arjun988/blender-skills](https://github.com/arjun988/blender-skills) (MIT): 94 skills through Blender MCP, `blender-director` routes (blockout -> UVs -> materials -> engine export). Copy `.claude/skills` into the project.
- [achimala/dream-loop](https://github.com/achimala/dream-loop) (MIT): image generation dreams the target, the agent builds toward it, a critic scores every screenshot. `npx skills add achimala/dream-loop`.
- [angrypenguinpng/blender-claymation-skill](https://github.com/angrypenguinpng/blender-claymation-skill) (MIT): stop-motion clay at 12 fps with boil and flicker in Cycles. Good example of a Blender SKILL.md. A GPU helps.
- [majidmanzarpour/threejs-game-skills](https://github.com/majidmanzarpour/threejs-game-skills) (MIT): playable three.js games. `npx skills add majidmanzarpour/threejs-game-skills --skill '*' -a claude-code -g -y`.
- [wheresryan22/anatomy](https://github.com/wheresryan22/anatomy) (MIT, 26k stars): isometric exploded explainer figures in SVG / SVG + WebGL. `npx skills add wheresryan22/anatomy -g -a claude-code`.
- [viettranx/3dviz-pro-max](https://github.com/viettranx/3dviz-pro-max) (MIT): idea -> three.js or Blender scene, 223 recipes.
- Opus can build a claymation in Blender from one prompt ([Alex Albert](https://x.com/alexalbert__/status/2102458348511879448)).

## Lottie and web effects

- [diffusionstudio/lottie](https://github.com/diffusionstudio/lottie) (MIT): `npx skills add diffusionstudio/lottie`, then ask for `text-to-lottie`. Live player reloads the JSON as it changes; output imports into After Effects.
- [ybouane/liquidglass](https://github.com/ybouane/liquidglass): WebGL glass refraction for web UI (`npm install @ybouane/liquidglass`). UI polish, not video.

## Sound and speech

- [Leonxlnx/earshot](https://github.com/Leonxlnx/earshot) (MIT, v0.4.1): listening, review and a 252-sound code-made bank. Install: `uv tool install "earshot[mcp] @ git+https://github.com/Leonxlnx/earshot"` then `earshot doctor --fetch` (~1.1 GB models). MCP: `claude mcp add earshot --scope user -e EARSHOT_THREADS=3 -- earshot mcp`. Skills: copy `skills/earshot` and `skills/real-sound` into `~/.claude/skills/`. Timing flags are in seconds (`--dur`, `--apex`); frame alignment happens in your mixer or the kit lock.
- ElevenLabs: skills `npx skills add elevenlabs/skills` (TTS, STT, SFX, music, voice changer, isolator, dubbing, agents; need `ELEVENLABS_API_KEY`). Plugin with hosted MCP: `/plugin marketplace add elevenlabs/plugin` then `/plugin install elevenlabs@elevenlabs` (hosted MCP `https://api.elevenlabs.io/v1/mcp`, account sign-in). `elevenlabs/elevenlabs-mcp` is archived: do not install it.
- [openai/whisper](https://github.com/openai/whisper) (MIT): `pip install -U openai-whisper`; `--word_timestamps True --output_format srt|vtt|json`.

## Generated footage (AI video and image models)

Use for B-roll, UGC-style ads, character shots and model demo reels; keep motion graphics, type and end cards in code.
- Video models Victor has shipped with: Seedance (2.x), Veo (3.x), Kling, MiniMax Hailuo (H3), through a provider API (his own engine, kie.ai, OpenRouter or fal). Images: Nano Banana, GPT Image, Seedream.
- Seedance shines at real people, POV, drone and action shots, bright and contrasted like a GoPro commercial. Generate short clips (4-5 s) at low resolution first, cut the best 2 s, stitch, then upgrade the winners.
- Image-to-video with a first frame and an audio reference gives the most control over identity and timing.
- Seedream 5.5 outputs native transparent PNGs (stickers, lettering, compositing assets).
- MotionClone (motionclone.lol) turns a reference clip into an editable HyperFrames project. Early access, Windows setup, sends frames to ChatGPT; canonical repo was 404 on Nov 9.

## Lists and research

- [zhuyansen/awesome-claude-video-skills](https://github.com/zhuyansen/awesome-claude-video-skills) (CC0): 459 video skill repos, security-graded. Its head-to-head test of 16 skills on one brief picked explainroo, onetake and remotion-dev/skills; plain Claude Code with no skill also passed every check, in 4.4 minutes. Skills speed you up; the review loop and taste decide quality.
