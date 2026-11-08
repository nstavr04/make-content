# Make content: instructions for your agent

You are setting up and then running a short-form content workflow for Instagram Reels and TikTok, inside Claude Code (or a similar coding agent). Read this whole file first.

It has two parts:
1. **Setup**: install the tools with the user, once.
2. **The workflow**: how to make each piece. Save this part as a skill so it's used every time.

The user posts everything themselves. Never post, like, follow, comment or DM on their behalf.

---

## Part 1: Setup

Walk the user through this one step at a time. Explain in one line what each tool is for, and ask before installing anything. Use each project's own README for the install steps; they change more often than this file.

1. **Ask which modes they want:**
   - **Footage**: they film themselves talking, and you edit it.
   - **Faceless**: motion-design videos with a voiceover, or carousels. Good for a product or brand.
   - Or both.
2. **Writing and research skills** (both modes):
   - [instagram-agent-skill](https://github.com/Jakeschincariol/instagram-agent-skill): `ig-viral` (finds over-performing reels), `ig-reel` (hooks and timed script), `ig-caption`, `ig-human` (removes the AI sound from text), `ig-carousel`.
   - [content-skills](https://github.com/artemnovitckii/content-skills): `viral-hooks` and `storytelling`.
3. **Research in the browser:** [OpenCLI](https://github.com/jackwener/OpenCLI). It turns websites into a CLI and runs through the user's own logged-in Chrome (via its extension), so you can search Instagram and TikTok as them. The user logs in to Instagram and TikTok in Chrome themselves.
4. **Footage mode:**
   - [Video Use](https://github.com/browser-use/video-use) for cutting, plus `ffmpeg`.
   - **Transcription:** [ElevenLabs](https://elevenlabs.io) Scribe is the best choice. It needs an ElevenLabs API key, which the user creates and saves in their own `.env` (never print it). If they don't want a paid key, use a local Whisper (whisper.cpp, faster-whisper or WhisperX) if their machine can run it. Read the Whisper caveat in Part 2.
5. **Visuals and faceless videos:** [HyperFrames](https://github.com/heygen-com/hyperframes) and its skills (HTML rendered to video). Faceless voiceover uses ElevenLabs text-to-speech, with the same key.
6. **Scheduling (optional):** [Postiz](https://github.com/gitroomhq/postiz-app). It schedules posts to Instagram and TikTok, including the cover image. The user connects their own accounts.
7. **Save Part 2 as a skill:** write everything under "Part 2" to `~/.claude/skills/make-content/SKILL.md`, with this front matter:
   ```
   ---
   name: make-content
   description: Make a TikTok/Instagram video or carousel from an idea - research what's working, agree the angle, write the script, edit the user's footage or animate a faceless video, preview, then package with thumbnails and a caption. Use when the user wants to make a reel, TikTok, video or carousel, or drops raw footage.
   ---
   ```
8. **Make a profile:** ask 3-4 quick questions (who the account is for, how they sound, topics, anything they never say) and save the answers to `profiles/<account>.md`. Make one profile per account.

Finish by telling the user what's installed and suggesting a first video idea to try.

---

## Part 2: The workflow

### What makes a video work

Name a problem the viewer already has, in their words, then hit them with "here's the fix". Value first; never an ad.

- **The viewer's problem, not the research.** The hook is about them, never about a study or a product. Research backs a claim; sources go in the caption, not on screen.
- **One idea, one example** carried from the hook to the payoff. If a beat doesn't push that idea, cut it.
- **Built on what already works.** Take the format, hook shape and pacing from real over-performing videos, not from taste.
- **Show the real thing** (the screen, product, repo or food) before reaching for text cards.
- **Clear on mute, in one watch.**
- **For products, sell the category, not the brand** ("a calorie tracker makes this take seconds"), unless the user asks otherwise.

### Every piece gets a folder

`projects/<YYYY-MM-DD>-<slug>/` with `raw/` (footage, never modified), `research.md`, `brief.md`, `storybook.md`, `edit/`, and `export/`.

### The steps

The flow stops three times for the user's OK (✋). Between those stops, keep going without asking.

1. **Start at once.** In the same turn:
   - start researching references in the background
   - start transcribing any footage
   - ask 2-4 short questions: the one thing viewers should remember, who it's for, what must stay in
2. **Research (15-25 minutes, not a report).**
   - Search Instagram and TikTok with OpenCLI for the topic.
   - Rank results by **outlier multiple**: views divided by that account's usual views. Above 3x is a signal.
   - Watch the best 2-3. Note the first 2 seconds, the structure, the cut rhythm, the captions and the ending.
   - Take the formula, never the script.
   - Fact-check what will be said.
3. **✋ The angle.** Offer 2-3 angles: the viewer's problem, the fix moment, the example, and the reference it borrows from. The user picks one.
4. **The storybook.** Load `ig-reel`, `viral-hooks` and `storytelling`, and use them for real, not from memory.
   - Write the hook options with scores, then a beat table: spoken line, on-screen text, source and edit notes.
   - Footage: map every beat to real takes and timestamps.
   - Nothing filmed yet: give talking points and a shot list, not a word-for-word script, so the user still sounds like themselves.
5. **✋ Storybook approval.**
6. **Build.** Footage or Faceless, below.
7. **✋ Style preview.** Render the first 2 seconds plus 5-8 seconds of the body (a carousel: the cover and one slide). Adjust, then render the whole piece and check it yourself before showing it.
8. **Package.** Put these in `export/`:
   - the final video
   - two frame thumbnails and one designed cover (a 2-5 word title over the best frame). Keep key content centred so the 3:4 grid crop keeps it.
   - a caption from `ig-caption` and then `ig-human`. Its first line carries the one ask, for example "Comment SETUP and I'll send it". Up to 5 specific hashtags, no links.

### Footage: how the edit works

These rules come from real cuts. The early versions felt rushed and choppy until they were followed.

- **Transcription decides every cut.** Use ElevenLabs Scribe with word timestamps (in Video Use, or `scribe_v1` with `timestamps_granularity=word`, `tag_audio_events=true`). Local Whisper leaves out "uh", "um" and restarts and stretches the next word over them, so cut points can be up to a second off: clipped words, half fillers, abrupt joins. If you must use Whisper, pad cuts more and check every join by looking at the waveform or listening.
- **Cut whole passages.** A cut is one passage the person said in one go. Never glue half of one sentence onto half of another. Keep the bridge line that sets up each point ("And then step three, I'm using...").
- **Inside a passage, remove only fillers and stutters**, at exact word edges.
- **Leave breaths.** Joins between passages get about 0.35s (0.12s before the first word, 0.22s after the last). Shorten pauses inside a passage only when they're over 0.4s, and then to 0.3s. Speed up at most 1.13x.
- **Fit the length first.** Instagram Reels cap at 3:00. If the passages you want run over, drop whole side passages (asides, lists, repeats); never trim inside the ones you keep. Tell the user what was dropped.
- **Read every join back** from the transcript: the end of one passage, then the start of the next. Each should read like a sentence they'd actually say.
- **The first frame is already moving or mid-sentence.** No intro, no logo.
- **Punch-ins:** alternate framing (100% and about 112%) at cuts within the same take, so jump cuts feel intentional.
- **Captions:** word-synced, 1-3 words per card, bold, kept out of the bottom 20% and the right edge, where the app's buttons sit.
- **Show what's mentioned.** When they name a tool, site or product, show it (split screen or full screen) while their audio keeps running. Hide any personal information in screenshots: usernames, avatars, taskbars, notifications.
- **Time each visual to the word it illustrates** (that word's time after the cut), not to a fixed second, so it stays in sync when the cut changes. Add at most one graphic every 8-10s.
- **Look and sound:** tone-map phone HDR to standard colour first. Loudness around -14 LUFS, peaks at or below -1 dBTP. Use sound effects sparingly. No music under a talking head unless the user wants it.
- If the project sits in a synced folder (Google Drive, iCloud, Dropbox), render from a local copy, because syncing can stall long renders.

### Faceless: how it works

- **The first frame is the hook:** a bold line or visual already moving, and the promise is clear in 1.5s with the sound off.
- **Something changes every 0.7-1.5s.** No still frame longer than about 2s.
- Kinetic text follows the voiceover word by word. Each beat raises the stakes, and the payoff lands with a visual hit and a sound effect.
- One palette, one or two typefaces, strong contrast, smooth eased motion. 1080x1920 at 30fps, built with HyperFrames.
- **Voiceover:** write for the ear, with short lines and contractions.
  - The first time, generate only the hook line in 2-3 voices, let the user pick, and save the choice in the profile.
  - Generate the full script in one call with timestamps, then regenerate only the lines that land badly.
  - Use a music bed under the voice, with the voice clearly on top.
- **Carousel:** use `ig-carousel`. Cover that earns the swipe, one idea per slide, payoff and one ask at the end. 1080x1350, 5-8 slides.

### Rules

- Browsing is read-only research. No likes, follows, comments, DMs or posting.
- Facts must be backed by research, or said as the user's opinion or experience.
- In Footage mode, the user's voice stays their own.
- ElevenLabs costs credits: transcribe each take once, and generate voiceover only for what the storybook needs.
- Never print API keys. Keep raw footage out of git.
- The first videos usually take a few rounds of iteration. With a good prompt and clear direction, they become mostly one shot.
