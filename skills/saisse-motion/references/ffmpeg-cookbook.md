# ffmpeg cookbook

Copy-paste recipes for probing, review sheets, cutting, stitching, reframing, captions, overlays and delivery. For edits on real footage with many steps (silence removal, karaoke captions, ducking, platform presets, timeline export), install [ffmpeg-skill](https://github.com/kajisho5/ffmpeg-skill) and let it run its probe -> plan -> process -> verify loop instead of hand-writing filters.

Rules for every recipe:
- **Probe first.** Read the real size, fps, duration, codecs and audio streams before writing a command. Never assume.
- Write to a new file, check it, then replace the original if asked (`mv` after the check).
- Even dimensions (`scale=-2:720`), `yuv420p`, `+faststart` for anything that plays in a browser or app.
- After every export: ffprobe the output and look at a contact sheet. Report size, dimensions, fps, duration.

## Probe

```bash
# Video stream: size, fps, duration, codec, pixel format
ffprobe -v error -select_streams v:0 -show_entries stream=width,height,avg_frame_rate,duration,codec_name,pix_fmt -of json in.mp4
# Audio streams (empty = no audio track)
ffprobe -v error -select_streams a -show_entries stream=codec_name,channels,sample_rate -of json in.mp4
# Container duration in seconds
ffprobe -v error -show_entries format=duration -of csv=p=0 in.mp4
# Loudness (integrated LUFS, true peak, LRA)
ffmpeg -hide_banner -i mix.wav -af loudnorm=I=-14:TP=-1.5:LRA=11:print_format=summary -f null - 2>&1 | tail -12
```

## Frames for analysis

```bash
# Overview of a reference: 2 images per second at 960 px wide
mkdir -p frames && ffmpeg -i reference.mp4 -vf "fps=2,scale=960:-2" frames/ref-%04d.jpg
# Consecutive source frames around an event (entrance, landing, hit)
ffmpeg -ss 6.0 -i reference.mp4 -frames:v 24 -fps_mode passthrough frames/evt-%02d.png
# One exact frame by number (0-based)
ffmpeg -i in.mp4 -vf "select=eq(n\,195)" -frames:v 1 f195.png
# One screenshot every 5 s, one folder per video
for f in *.mp4; do d="${f%.*}"; mkdir -p "$d"; ffmpeg -v error -i "$f" -vf fps=1/5 "$d/%03d.png"; done
```

## Review sheets

```bash
# Whole film, one frame per second, on one image
ffmpeg -i out.mp4 -vf "fps=1,scale=240:-1,tile=8x9" -frames:v 1 review/contact.png
# Phone test: how it reads at 360 px wide
ffmpeg -i out.mp4 -vf "fps=1/3,scale=360:-1,tile=6x4" -frames:v 1 review/phone.png
# Every 6th frame, labelled with its frame number (needs ffmpeg with drawtext / freetype)
ffmpeg -i out.mp4 -vf "drawtext=text='f%{n}':x=16:y=16:fontsize=44:fontcolor=yellow:box=1:boxcolor=black@0.6,select='not(mod(n\,6))',scale=270:-1,tile=9x3:padding=4" -frames:v 1 review/frames.jpg
```

Open the PNG and look at it. Adjust the tile grid to the clip length (8x9 holds 72 s at 1 fps).

## Partial renders (never re-render the whole piece to check one fix)

```bash
# Remotion: three stills that direct the next pass (opening, product result, closing)
npx remotion still <Comp> out/opening.png --frame=60
npx remotion still <Comp> out/product.png --frame=240
npx remotion still <Comp> out/closing.png --frame=540
# Remotion: only the frames around a scene boundary
npx remotion render <Comp> out/transition.mp4 --frames=90-149 --codec=h264
# Remotion: half-scale draft, then the final
npx remotion render src/index.ts <Comp> out/draft.mp4 --scale=0.5
npx remotion render src/index.ts <Comp> out/final.mp4 --crf=14 --audio-bitrate=320k
# Remotion: transparent overlay (end card, lower third) as ProRes 4444 with alpha
npx remotion render src/index.ts <Comp> out/cta.mov --codec=prores --prores-profile=4444 --image-format=png --pixel-format=yuva444p10le
# Blender: one still at frame 1262, or only the range around a fix (-s and -e before -a)
blender -b episode.blend -o //review/still_ -f 1262
blender -b episode.blend -o //review/fix_ -s 1215 -e 1290 -a
```

## Compress to a target size

Two-pass H.264 hits a size reliably. Video bitrate = target size / duration - audio bitrate, with 5% headroom for the container.

```bash
# usage: fit in.mp4 out.mp4 <MB> [audio_kbps, 0 = drop audio] [height, e.g. 720]
fit() {
  local in="$1" out="$2" mb="$3" ak="${4:-128}" h="${5:-}"
  local dur total vk vf=() aud=(-an)
  dur=$(ffprobe -v error -show_entries format=duration -of csv=p=0 "$in")
  total=$(echo "$mb * 8192 * 0.95 / $dur" | bc)
  vk=$(( total - ak ))
  [ -n "$h" ] && vf=(-vf "scale=-2:$h")
  [ "$ak" -gt 0 ] && aud=(-c:a aac -b:a "${ak}k")
  ffmpeg -v error -y -i "$in" "${vf[@]}" -c:v libx264 -preset slow -b:v "${vk}k" -pass 1 -passlogfile /tmp/fit2pass -an -f null /dev/null &&
  ffmpeg -v error -y -i "$in" "${vf[@]}" -c:v libx264 -preset slow -b:v "${vk}k" -pass 2 -passlogfile /tmp/fit2pass "${aud[@]}" -pix_fmt yuv420p -movflags +faststart "$out"
  ls -lh "$out"
}
fit in.mp4 out.mp4 5            # 5 MB with audio
fit in.mp4 out.mp4 3 0 720      # 3 MB, no audio, 720p
# Replace originals in a folder, max 2 MB each, no audio
for f in *.mp4; do fit "$f" "/tmp/fit-$f" 2 0 720 && mv "/tmp/fit-$f" "$f"; done
```

If the video bitrate comes out very low (under ~400 kbit/s at 720p), drop to 540p or shorten the clip instead of shipping mush.

Quality-first (no size target): `ffmpeg -i in.mp4 -c:v libx264 -crf 23 -preset slow -vf "scale=-2:720" -pix_fmt yuv420p -movflags +faststart -an out.mp4`. CRF 18 = near lossless, 23 = default, 28 = small.

## Web and app delivery

```bash
# Muted autoplay loop for a site, paywall or CDN: H.264 MP4, 720p, 30 fps, no audio
ffmpeg -i in.mp4 -an -c:v libx264 -profile:v high -crf 26 -preset slow -vf "scale=-2:720,fps=30" -pix_fmt yuv420p -movflags +faststart loop.mp4
# Smaller sibling for browsers that take it: VP9 WebM
ffmpeg -i in.mp4 -an -c:v libvpx-vp9 -b:v 0 -crf 36 -row-mt 1 -vf "scale=-2:720" loop.webm
# Poster image (first impression, also the thumbnail)
ffmpeg -ss 1 -i in.mp4 -frames:v 1 -q:v 2 poster.jpg
```

```html
<video autoplay muted loop playsinline preload="metadata" poster="poster.jpg">
  <source src="loop.webm" type="video/webm" />
  <source src="loop.mp4" type="video/mp4" />
</video>
```

Animated WebP or GIF is almost always bigger and worse than a muted MP4/WebM for anything over a second. Use them only where video tags are not allowed.

## Cut, trim, stitch

```bash
# Frame-accurate trim (re-encode): 2 s from 0:03
ffmpeg -ss 3 -i in.mp4 -t 2 -c:v libx264 -crf 18 -an clip.mp4
# Keep at most 30 s
ffmpeg -i in.mp4 -t 30 -c:v libx264 -crf 20 -c:a aac out.mp4
# Normalize clips (size, fps, pixel format) so they can be joined without re-encoding
mkdir -p norm && for f in *.mp4; do ffmpeg -v error -y -i "$f" -t 2 -an -c:v libx264 -crf 18 -vf "scale=1280:720:force_original_aspect_ratio=increase,crop=1280:720,fps=30,format=yuv420p" "norm/$f"; done
# Join in natural order (clip1, clip2, ... clip10)
ls norm/*.mp4 | sort -V | sed "s/.*/file '&'/" > list.txt && ffmpeg -f concat -safe 0 -i list.txt -c copy reel.mp4
# Crossfade two clips (offset = duration of the first clip - fade length)
ffmpeg -i a.mp4 -i b.mp4 -filter_complex "[0:v][1:v]xfade=transition=fade:duration=0.3:offset=1.7,format=yuv420p" -an ab.mp4
```

Pick each 2 s highlight with `-ss` at the best moment (look at the contact sheet first) instead of always taking the first 2 s.

Rotate several long videos in chunks (A1 B1 C1 A2 B2 C2 ...), muted:

```bash
parts=10; vids=(a.mp4 b.mp4 c.mp4)
for f in "${vids[@]}"; do d=$(ffprobe -v error -show_entries format=duration -of csv=p=0 "$f")
  for k in $(seq 0 $((parts-1))); do
    ffmpeg -v error -y -ss "$(echo "$d*$k/$parts" | bc -l)" -i "$f" -t "$(echo "$d/$parts" | bc -l)" -an -c:v libx264 -crf 20 \
      -vf "scale=1920:1080:force_original_aspect_ratio=decrease,pad=1920:1080:(ow-iw)/2:(oh-ih)/2,fps=30,format=yuv420p" "seg_${k}_$f"
  done; done
for k in $(seq 0 $((parts-1))); do for f in "${vids[@]}"; do echo "file 'seg_${k}_$f'"; done; done > list.txt
ffmpeg -f concat -safe 0 -i list.txt -c copy rotating.mp4
```

## Reframe and layouts

```bash
# 16:9 -> 9:16 with a blurred fill behind the original
ffmpeg -i in.mp4 -filter_complex "[0:v]split=2[bg][fg];[bg]scale=1080:1920:force_original_aspect_ratio=increase,crop=1080:1920,boxblur=20:2[b];[fg]scale=1080:-2[f];[b][f]overlay=(W-w)/2:(H-h)/2,format=yuv420p" -c:a copy vertical.mp4
# 16:9 -> 9:16 centre crop
ffmpeg -i in.mp4 -vf "crop=ih*9/16:ih,scale=1080:1920" -c:a copy vertical-crop.mp4
# Three 9:16 clips side by side in one 1920x1080 frame
ffmpeg -i a.mp4 -i b.mp4 -i c.mp4 -filter_complex "[0:v]scale=640:-2,crop=640:1080[a];[1:v]scale=640:-2,crop=640:1080[b];[2:v]scale=640:-2,crop=640:1080[c];[a][b][c]hstack=inputs=3,format=yuv420p" -an -shortest triptych.mp4
# Side-by-side comparison (ours vs reference), same height
ffmpeg -i ours.mp4 -i ref.mp4 -filter_complex "[0:v]scale=-2:1080[a];[1:v]scale=-2:1080[b];[a][b]hstack=inputs=2,format=yuv420p" -an compare.mp4
```

## Captions

```bash
# Word timestamps, 2 words per line, as SRT and JSON
whisper voice.wav --model small --word_timestamps True --max_words_per_line 2 --output_format all --output_dir captions/
# Burn an SRT (sizes are in a 288-line script space: FontSize 16 on a 1920-tall video is about 107 px)
ffmpeg -i in.mp4 -vf "subtitles=captions/voice.srt:force_style='FontName=Inter,FontSize=16,Bold=1,PrimaryColour=&H00FFFFFF,OutlineColour=&H00000000,BorderStyle=1,Outline=3,Shadow=0,Alignment=2,MarginV=60'" -c:a copy captioned.mp4
```

For animated captions (pop on stressed words, karaoke highlight), build them in Remotion from the word JSON, or use ffmpeg-skill's caption tools.

## Overlays and end cards

```bash
# Alpha end card (cta.mov, ProRes 4444) that starts 6 frames (0.2 s) before the ad's last frame at 12.0 s and runs 3.2 s.
# The ad's last frame is held underneath while the card plays; the card's SFX stem lands at the same offset.
ffmpeg -i ad.mp4 -itsoffset 11.8 -i cta.mov -i cta-sfx.wav -filter_complex \
"[0:v]tpad=stop_mode=clone:stop_duration=3.2[base];[base][1:v]overlay=0:0:eof_action=pass,format=yuv420p[v];\
[2:a]adelay=11800:all=1[s];[0:a]apad[a0];[a0][s]amix=inputs=2:normalize=0:duration=longest[a]" \
-map "[v]" -map "[a]" -t 15.0 -c:v libx264 -crf 18 -c:a aac -b:a 192k -movflags +faststart final.mp4
```

If the ad has no audio track, generate one first (`-f lavfi -i anullsrc=r=48000:cl=stereo`) or map only the stem.

## Images to video

```bash
# Fast reel: 5 images per second, 16:9, muted, loops cleanly if first and last images differ
ffmpeg -framerate 5 -pattern_type glob -i 'imgs/*.png' -vf "scale=1920:1080:force_original_aspect_ratio=increase,crop=1920:1080,fps=30,format=yuv420p" -c:v libx264 -crf 20 -an reel.mp4
```

## Audio utilities

```bash
ffmpeg -i in.mp4 -vn -c:a pcm_s16le audio.wav                      # extract audio
ffmpeg -i in.mp4 -c:v copy -an silent.mp4                           # drop audio, keep video untouched
ffmpeg -i in.mp4 -af silencedetect=noise=-35dB:d=0.4 -f null - 2>&1 | grep silence_   # find pauses
ffmpeg -i mix.wav -af loudnorm=I=-14:TP=-1.5:LRA=11 -ar 48000 mix-14.wav               # social loudness
```

## Screen recordings and timelapses

```bash
# Drop idle (near-duplicate) frames, then speed up 8x, muted
ffmpeg -i recording.mov -vf "mpdecimate,setpts=N/FRAME_RATE/TB,setpts=PTS/8,fps=30" -an -c:v libx264 -crf 20 -pix_fmt yuv420p timelapse.mp4
```

Record only the tab or window that matters, at a steady frame rate (24 or 30 fps). Cut to what is needed: dead time, loading spinners and mistakes go.

## Metadata and dates

```bash
ffmpeg -i in.mp4 -c copy -map_metadata 0 -metadata creation_time=2022-10-22T12:00:00Z out.mp4   # container date
touch -t 202210221200 out.mp4                                                                  # modified date
SetFile -d "10/22/2022 12:00:00" out.mp4                                                       # macOS created date (Xcode CLT)
```

## GIF (only when video is not allowed)

```bash
ffmpeg -i in.mp4 -vf "fps=15,scale=640:-1:flags=lanczos,split[a][b];[a]palettegen[p];[b][p]paletteuse" out.gif
```

Docs: [FFmpeg filters](https://ffmpeg.org/ffmpeg-filters.html), [ffprobe](https://ffmpeg.org/ffprobe.html), [Remotion CLI render](https://www.remotion.dev/docs/cli/render), [Remotion stills](https://www.remotion.dev/docs/cli/still), [Blender CLI](https://docs.blender.org/manual/en/latest/advanced/command_line/arguments.html).
