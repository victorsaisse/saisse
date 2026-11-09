# Sources

Where every idea in this skill comes from. X posts were read in full (including X Articles and replies) on 2026-11-09. Repos were checked on GitHub the same day (details in `toolkit.md`).

## X posts (the MOTION-SKILLS reading list)

| Post | Author, date | What it adds |
|---|---|---|
| [3D + video skill stack, /reverse-any-video](https://x.com/Y0urDumble/status/2119023461267833047) | Dumble (@Y0urDumble), 2026-11-07 | The stack map: HyperFrames, Remotion skills, OpenMontage (video); GSAP skills, video-shotcraft, motionmaxxing (motion); MCP for Blender, blender-skills, dream-loop (Blender); threejs-game-skills, anatomy, 3dviz-pro-max (3D web). HyperFrames media library (10,000+ tracks, SFX, 75,000+ images, TTS). `npx hyperframes skills update`. |
| [How to build a 2.5D motion studio with Opus 5.5 (Full Course)](https://x.com/Y0urDumble/status/2115164938884862375) | Dumble, 2026-10-27, X Article | Frame-by-frame anatomy of a 27M-view short (1080x1920, 30 fps, 65 s, flat 2D characters in lit 3D sets, a cut every ~2 s, 1-2 word captions). Reference breakdown with MEASURED / ESTIMATED / DECISION labels. CLAUDE.md house rules. 160-word scripts with sources.md. Shot lists and beats in frames. Blender BLOCK / LIGHT / DRESS passes with MCP `look`. Hero props in 3D. SVG cut-out characters with expression sheets. Acting lines. Whisper word captions. earshot sound review. Contact sheet + phone test. Critic pass and four-field corrections. Platform rules (monetisation, AI disclosure). |
| [ffmpeg-skill](https://x.com/cutetoxicguy/status/2117380985289413060) | CuteGuy (@cutetoxicguy), 2026-11-02 | 42-tool local FFmpeg editing for agents, its verification loop (probe, plan, process, verify, inspect contact sheets), MCP with 12 core tools, install and doctor commands, honest limits. |
| [Opus 5.5 + Remotion + GSAP motion design lab](https://x.com/beamnxw/status/2112180280110776797) | beamnxw (@beamnxw), 2026-10-19 | The six editable controls (spacing, typography, motion blur, easing, follow-through, anticipation) in one config, feedback as frame numbers, build -> render -> scrub -> tune -> repeat, GSAP on Remotion's frame clock. |
| [How I built a motion design studio my AI employee runs](https://x.com/beamnxw/status/2111079523089322438) | beamnxw, 2026-10-16, X Article (paid partnership with Viktor) | The business around the videos: inquiry qualification, briefs from call notes, two-option quotes, asset checklists, revision rounds, delivery and invoices, a Monday report. "He proposes, I approve." |
| [earshot v0.3](https://x.com/LexnLin/status/2111927727318245426) | Leon Lin (@LexnLin), 2026-10-18 | 201 code-made sounds, `earshot kit` (whole sound kits), `earshot sfx` timing to the frame, used by cinetic. |

## Posts linked from those

| Post | Author, date | What it adds |
|---|---|---|
| [Motion Engineering with Opus 5.5: Build Launch Videos You Can Sell](https://x.com/beamnxw/status/2108616740846420354) | beamnxw, 2026-10-09, X Article | Give the reference one job. Product shot as an event (start, cursor, press, result, hold). BRIEF.md with layout coordinates and frame ranges. Longest-copy layout checks. Frame-driven Remotion, seeded randomness, springs, motion blur. Three stills before motion. Partial renders. Four-field corrections. Sound on event frames. Scene boundaries. The $150 pilot Launch Clip offer and how to find buyers. |
| [Introducing Claude Opus 5.5](https://x.com/claudeai/status/2102435511222890900) | Claude (@claudeai), 2026-09-22 | The model these workflows were built on. |
| [Opus 5.5 makes claymations in Blender](https://x.com/alexalbert__/status/2102458348511879448) | Alex Albert (@alexalbert__), 2026-09-22 | One-prompt Blender claymation. |
| [Favourite things built with Opus 5.5](https://x.com/claudeai/status/2103515655760982273) | Claude, 2026-09-25 | Roundup, incl. an interactive camera lens lab built in one shot. |
| [AI is becoming a production pipeline (Nov 9 digest)](https://x.com/cutetoxicguy/status/2119854220459331675) | CuteGuy, 2026-11-09, X Article | MotionClone (reference -> editable HyperFrames project), Seedream 5.5 native transparency, ComfyUI <-> After Effects / Nuke bridges. Editable outputs matter as much as generation quality. |

## Useful replies

| Reply | Tip |
|---|---|
| [@wonraai](https://x.com/wonraai/status/2119389236101001597) | `/reverse-any-video` is HyperFrames Pro; `reverse start` refuses on free accounts. |
| [@Izkszk](https://x.com/Izkszk/status/2119276329548050829) | Batch reversals break colour science; they hand-fix ~15% of outputs. QA is the new bottleneck. |
| [@CadabraAIfilms](https://x.com/CadabraAIfilms/status/2119095077930979343) | No skill covers taste: which moment to cut on, when to hold a beat longer. |
| [@MatiasMatthews](https://x.com/MatiasMatthews/status/2112193862597025946) | Save the approved render next to its config. |
| [@baboonAI4S](https://x.com/baboonAI4S/status/2113423865695383632) | Render CLI flags drift; pin them, diff `--help` after upgrades, check frame 1 before batch renders. |
| [@dovrikson](https://x.com/dovrikson/status/2112188899770671604) | The title hold deserves its own control. |
| [@ayushhjo](https://x.com/ayushhjo/status/2112968988758327803) | Squash and small overshoot on every drop and bounce stops the slideshow look. |
| [@vibewrightai](https://x.com/vibewrightai/status/2112297868778508636) | Make the agent expose knobs, not code. |
| [@WildHoney_xy](https://x.com/WildHoney_xy/status/2112345629553045538) | Repeatable timing beats a bigger sound catalogue; keep cues attached to events through revisions. |

## Docs

- Remotion: [fundamentals](https://www.remotion.dev/docs/the-fundamentals), [animating properties](https://www.remotion.dev/docs/animating-properties), [spring](https://www.remotion.dev/docs/spring), [random](https://www.remotion.dev/docs/random), [motion blur](https://www.remotion.dev/docs/motion-blur), [sequence](https://www.remotion.dev/docs/sequence), [transitions](https://www.remotion.dev/docs/transitions), [audio](https://www.remotion.dev/docs/html5-audio), [GSAP timeline](https://www.remotion.dev/docs/gsap/use-gsap-timeline), [still](https://www.remotion.dev/docs/cli/still), [render --frames](https://www.remotion.dev/docs/cli/render#--frames), [coding agents](https://www.remotion.dev/docs/ai/coding-agents), [licence](https://github.com/remotion-dev/remotion/blob/main/LICENSE.md)
- ElevenLabs: [models](https://elevenlabs.io/docs/models), [TTS best practices](https://elevenlabs.io/docs/overview/capabilities/text-to-speech/best-practices), [with timestamps](https://elevenlabs.io/docs/api-reference/text-to-speech/convert-with-timestamps), [music](https://elevenlabs.io/docs/overview/capabilities/music), [music prompting](https://elevenlabs.io/docs/overview/capabilities/music/best-practices), [music cookbook](https://elevenlabs.io/docs/eleven-api/guides/cookbooks/music), [sound effects](https://elevenlabs.io/docs/overview/capabilities/sound-effects), [agent tooling](https://elevenlabs.io/docs/eleven-api/resources/agent-tooling.md), [llms.txt](https://elevenlabs.io/docs/llms.txt)
- FFmpeg: [filters](https://ffmpeg.org/ffmpeg-filters.html), [ffprobe](https://ffmpeg.org/ffprobe.html)
- Blender: [Images as Planes](https://docs.blender.org/manual/en/latest/modeling/meshes/import_images_as_planes.html), [command line](https://docs.blender.org/manual/en/latest/advanced/command_line/arguments.html)
- Claude: [vision limits](https://platform.claude.com/docs/en/build-with-claude/vision), [Claude Code memory (CLAUDE.md)](https://code.claude.com/docs/en/memory), [model config](https://code.claude.com/docs/en/model-config)
- Platforms: [YouTube monetisation policies](https://support.google.com/youtube/answer/1311392), [YouTube AI disclosure](https://blog.youtube/news-and-events/disclosing-ai-generated-content/), [Poly Haven licence](https://polyhaven.com/license)
- [MotionVideo](https://motionvideo.xyz/en): storyboard-first motion skills and a prompt directory.

## Victor's own work

The rules marked as Victor's come from his Claude Code sessions between March and November 2026: a Remotion map animation (LA -> NY -> Paris in 3D), dozens of compression and delivery jobs, AI-model demo reels (H3, Seedance), a Film Studio timelapse edit with music and SFX, a 5-second product demo that became his local studio (`~/motion-studio`: Remotion + GSAP + earshot + ElevenLabs via fal), a 5-second Shopify app home-page motion piece, presentation decks, and a set of UGC-style paid ads with lip sync and CTA end-card variants. Details in `victor-notes.md`.
