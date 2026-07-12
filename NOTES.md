# Unicorn Math Game — Project Notes

A first-grade math game for **Ange (age 6)**, who loves Elsa/Frozen and unicorns.
Built as a single self-contained HTML file (no external assets — works offline, just double-click).

## Files
- `index.html` — the whole game (HTML + inline CSS + inline JS). This is the only file to edit.
  (Renamed from `feed-the-unicorns.html` so GitHub Pages serves it at the root URL.)

## How to run
Double-click `index.html`, or from terminal:
`open "index.html"`

## How it's deployed
Hosted on GitHub Pages from this folder's git repo. To publish an edit:
`git add index.html && git commit -m "update" && git push`
The live site updates automatically about a minute after each push.

## Game flow
Name entry → Pick unicorn (6 choices) → Pick story (3) → Story intro (spoken) → Play → Win → replay / pick another story.

## What's built so far
- **Name first**: child types name; unicorn greets & cheers her by name (text + read-aloud voice).
- **6 pickable unicorns** (Sparkle, Blueberry, Rainbow, Buttercup, Minty, Lavender). Chosen one appears beside every question and in intro/win screens.
- **Full-body animated SVG unicorn** (`unicornSVG()` function):
  - Idle: breathing + tail sway.
  - Correct: jumps/dances, blush cheeks, big smile, whinny sound, sparkles.
  - Wrong: slumps, frowns, sheds tears, gentle shake — never a "game over".
- **"Elsa" voice** (2026-07-12): read-aloud auto-picks the warmest female English voice on the device (`bestVoice()` scoring) and tunes it gentle/regal (`rate .9, pitch 1.12`). NOTE: the *real* Frozen/Idina Menzel voice is copyrighted and unavailable to browsers — this is the closest system-voice approximation. A **voice picker** on the name screen (`#voiceSel` + 🔊 Try) lets you choose any installed voice; choice saved in `localStorage['elsaVoice']`. On macOS, install premium voices (System Settings → Accessibility → Spoken Content → Manage Voices) for better quality.
- **3 stories**, **20 problems each**, with **difficulty that ramps within the adventure** (`lvl = fed/(goal-1)`, fed into every generator):
  1. 🧁 The Frozen Cupcake Party — Easy: add/subtract (to ~30), make-10/make-20, counting, small doubles.
  2. 🌉 Rescue the Baby Unicorn — Medium: add/subtract (to ~80), number bonds (to ~40), skip-count 2/5/10, add-3-numbers, doubles.
  3. 🐉 Break the Ice Dragon's Spell — Big Kid: add/subtract (to ~130), bonds (to ~60), skip-count 3/5/10, **simple multiplication** (×2–5,×10), add-3, bigger doubles.
- **Per-question story flavor** (2026-07-12): each story has a `frames[]` pool of short themed lines (e.g. "Charge the ice crystal! 💎"); one shows as the label above the question and is spoken before the problem.
- **Random surprises** (2026-07-12): at story start ~5 random question numbers are chosen (`state.surpriseAt`); hitting one after a correct answer fires `funSurprise()` — confetti / disco hue-shift / emoji parade / unicorn spin / music-note float, each with a jingle. Not every question.
- **Themed finale** (2026-07-12): `finale(storyKey)` gives each ending its own celebration — cupcake+confetti party, blue sparkle bridge reunion, or ice-shatter → sun/rainbow kingdom-thaw for the dragon — with a matching Web-Audio `melody()`, colored screen `flash()`, `emojiRain()` + `paradeAcross()`.
- Read-aloud via Web Speech API (🔊/🔇 toggle). Sounds via Web Audio (no files).
- 🏠 button returns to story menu.

## Code map (inside the single file, in the `<script>`)
- `state` — name, uni, story, fed count, goal (20), voice on/off, current, currentFrame, voiceList, voiceObj, surpriseAt.
- `UNICORNS[]` — color palettes. `unicornSVG(u, tag)` — builds the SVG (tag makes gradient IDs unique).
- Generators (each takes `lvl` 0..1, scaled via `lerp`): `makeArith(loMax,hiMax)`, `makeMakeN()`, `makeBonds(loWhole,hiWhole)`, `makeSkip(steps)`, `makeCount()`, `makeDoubles(loMax,hiMax)`, `makeAdd3(loMax,hiMax)`, `makeMult()`.
- `STORIES[]` — each has `key`, `gens[]`, `frames[]`, `parade`, `intro(name,uni)`, `winTitle`, `win(name)`, themed `item` emoji, `goal`.
- `show(id)` — screen switcher. Screens: name, chooseUni, chooseStory, intro, game, win.
- `newProblem()` / `renderAnswers()` / `choose()` — gameplay loop (3 answer options, correct + 2 near distractors; ramps difficulty; triggers surprises).
- Reactions: `happy()`, `sad()`, `cheer()`, `sparkleAt()`, `funSurprise()`, `finale()`. Effects: `emojiRain()`, `paradeAcross()`, `floatUp()`, `flash()`.
- Audio: `tone()`, `melody()`, `sound()`, `whinny()`. Voice: `loadVoices()`, `bestVoice()`, `buildVoiceMenu()`, `speak()`.

## Verifying edits before deploy
The whole game is inline JS, so after edits: syntax-check (`node --check` on the extracted `<script>`) and, if generators changed, re-run the math validator (see git history / earlier session — it runs each generator ~150k times checking answers are correct, ≥0, and always among the 3 options). Then `open index.html` to eyeball, then commit + push.

## Ideas discussed for NEXT (not yet built)
- Give each unicorn its *own* spoken personality (different pitch / catchphrase per unicorn).
- A 4th "endless" free-play story with no goal.
- Save progress between sessions (stickers/medals per story) via localStorage.
- **Multi-game arcade**: once a 2nd game exists, build an `AngeGames` menu/home page linking to each game (one bookmark for Ange).
- Possible additions: Elsa/Anna cameo between rounds, word problems.

## Design principles to keep
Big touch buttons, minimal reading (spoken prompts), no punishment for wrong answers,
sessions ending in a win, lots of reward animation. (Sessions are now 20 problems w/ gentle difficulty ramp — watch that the "Big Kid" dragon story stays winnable for a 6-year-old; dial back the `hiMax` values if it feels too hard.)
