---
name: ai-kandian-video
description: "Generate daily Douyin/TikTok AI popular-science videos from AI briefings. Use when creating or editing AI看点 videos, Douyin科普 videos, or dark-tech style short videos with Mandarin voiceover. Always extract images from the uploaded briefing and fetch real external visuals."
type: workflow
lifecycle: active
---

# AI看点视频制作 — Daily Douyin AI Popular-Science Videos

Create professional yet engaging 9:16 vertical Douyin videos from daily AI briefings. Fixed dark-tech workstation style within each video, real materials only, precise subtitle alignment.

## Core Rules (Must Follow)

| Rule | Requirement |
|------|-------------|
| Style lock | Same dark-tech workstation background (black + gray + purple-blue + purple) per video; only inner content changes |
| No fakes | No AI-generated fake UIs, fake numbers, or digital humans. Use real webpages, real screenshots, real backend numbers, real past covers |
| Pacing | No one-sentence-per-page. No random style jumps. One keyword highlight max per sentence |
| Subtitles | Bottom placement, FontSize=11, ~15 chars/line wrap, BorderStyle=4, MarginV=40, side margins 40. Replace punctuation with spaces |
| Voice | Standard Mandarin (prefer Orion male). Match user's personal audio timbre when reference provided. End with "谢谢观看" |
| Framing | 9:16 vertical, safe-area padding for Douyin mobile playback |
| Materials | **Must** use images from the uploaded briefing file + real external web materials. Animate real screenshots with subtle Ken Burns / motion for silent inserts |

## Production Workflow

1. **Parse briefing & extract embedded images**
   - User uploads daily AI briefing (usually .docx).
   - Extract all text topics (usually 5).
   - **Extract all embedded images** from the docx (use python-docx or unzip + extract media/).
   - Map extracted images to corresponding topics.
   - Expand key AI terms with short definitions (Lean, Frostbite, Quaise, etc.).
   - Write concise Mandarin script covering all topics in one continuous narration.

2. **Generate voiceover**
   - Use Orion male voice (or closest match to user's reference audio).
   - Generate with character-level timestamps for SRT.
   - Speed: start at ~1.18x (atempo), then adjust only if user requests. Prefer adjusting visuals over speech.

3. **Build subtitle (SRT)**
   - Force wrap at ~15 characters.
   - Replace all Chinese/English punctuation with spaces.
   - Bottom alignment, FontSize=11, MarginV=40, MarginL/R=40, BorderStyle=4, white text with dark box.
   - Align precisely to voice timestamps.

4. **Source real visuals (briefing + external)**
   - **Priority 1**: Use images extracted from the uploaded briefing file. Display them on the workstation.
   - **Priority 2**: Search external real materials with `web_search` / `search_images` for each topic (Terence Tao blog, Lean proof screenshots, Anthropic IPO charts, Frostbite CPU diagrams, Quaise drilling rigs, data center photos, etc.).
   - Download authentic screenshots / photos only. Never invent fake interfaces or numbers.
   - Create reusable dark-tech 9:16 workstation background once (black + gray + purple-blue).
   - Animate real screenshots into short silent B-roll clips (Ken Burns / subtle motion via generate_video I2V) for inserts.

5. **Assemble visual base**
   - Fixed workstation background throughout.
   - Show briefing-extracted images and external real materials inside the workstation.
   - Intercut real-material inserts at topic changes.
   - Use crossfade transitions (0.3s).
   - Default pacing: hold each topic long enough to match narration. Adjust individual transition points only when user specifies exact seconds (e.g., "delay 16s transition by 1.5s", "advance 35s by 2s").

6. **Mux final video**
   - Overlay SRT with force_style parameters above.
   - Match audio duration (use -shortest).
   - Output 1080x1920, CRF 18, aac 192k.

7. **Iterate on timing only**
   - When user says "X秒的画面切换延后/提前 N 秒", modify only that transition point via freeze-frame insert or trim+concat. Do not change other transitions or speech unless explicitly requested.
   - Prefer extending freeze at the exact frame over global setpts slowdown.

## Key Technical Defaults

- Resolution: 1080x1920, 25 fps
- Voice: Orion (male)
- Subtitle style: `FontName=Noto Sans CJK SC,FontSize=11,PrimaryColour=&H00FFFFFF,OutlineColour=&H00000000,BackColour=&H80000000,BorderStyle=4,Outline=1,Shadow=0,MarginL=40,MarginR=40,MarginV=40,Alignment=2,Bold=1`
- Transitions: xfade fade 0.3s
- Silent inserts: real screenshot + subtle motion (generate_video I2V)
- Image extraction: from uploaded .docx media folder or python-docx

## Common Adjustment Patterns

| User Request | Action |
|--------------|--------|
| "语速快了/慢了" | Prefer adjust visual hold times; only change atempo if user insists on speech |
| "第N秒画面切换延后/提前 M 秒" | Freeze or trim only at that timestamp; keep all other cuts identical |
| "字幕太大/位置不对" | Adjust FontSize / MarginV only |
| "标点换成空格" | sed replace punctuation in SRT before mux |
| "用真实素材插播" | Extract briefing images first, then search external real images → animate → intercut, style-locked |
| "改成YouTube版" | Scale/pad or crop to 16:9 (1920x1080), offer both with-black-bars and no-black-bars versions |

## Output Naming

`YYYYMMDD_AI看点_描述.mp4` (e.g. `20261121_AI看点_本周结尾版.mp4`)

Always deliver the final mp4 for Douyin preview. Offer YouTube 16:9 version on request.
