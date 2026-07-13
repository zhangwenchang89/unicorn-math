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
Name entry → **Pick a fairy creature to tame** (B&W) → **Color it** → **Pick a companion** from already-collected creatures (skipped in round 1) → Pick story (3) → Story intro (spoken) → Play → single-adventure Win → back to story menu. Finishing **all 3** adventures in a round → **Tame screen** (creature saved to collection) → new round.

## What's built so far
- **Name first**: child types name; spoken greeting by name. Name saved (`localStorage['ange_name']`) and pre-filled next visit.
- **Collectible fairy creatures** (2026-07-13) — the core new loop:
  - **Pick to tame**: `chooseCreature` shows a **randomized subset** (4 of 5) of creatures in **black & white** line-art, each with a **random shape variant** (so the set — and each creature — looks different every time). Creatures: Dragon, Phoenix, Pixie, Unicorn, Sky Kitten (`CREATURES[]`, each a `drawX(F,v)` fn).
  - **Color it** (`colorCreature`): coloring-book style — tap a palette swatch to pick `state.paintColor`, then tap any body part (`.part[data-part]`) to paint it; repaint anytime. 15-color `PALETTE`. Colors saved live to the round.
  - **Tame**: completing all 3 adventures (`state.round.done` reaches all `STORIES` keys) → `showTame()` adds `{key,variant,colors}` to `state.collection` (persisted), shows a party finale + message, then a new round begins.
  - **Collection**: 🎒 button (floating `#bag` on menus, `#bagGame` in game topbar) opens a modal showing every tamed creature **with the colors she chose**.
  - **Naming** (2026-07-13): tame screen has `#tameName` input (defaults to the type name; blank falls back). Each collection card also has an editable `.cname` input. Name stored on the collection item (`item.name`) + companion snapshot; shown in collection & companion picker.
  - **Recolor** (2026-07-13): each collection card has a 🎨 Recolor button → reopens the coloring stage on that saved creature (`recolorItem`). Coloring is now generalized via `startColoring(target,onDone)` + `renderColorStage()` + `state.colorTarget/colorDoneCb`; the round flow uses `colorDoneRound`, recolor returns to a menu and reopens the collection modal. (Because the companion snapshot shares the item's `colors` object by reference, recoloring a creature that's also the current companion updates it too.)
- **Companion** (2026-07-13): during questions the reacting buddy is a creature she **already collected** (`chooseCompanion` grid). **Round 1 has no companion** (collection empty → `#game.nocompanion` hides the companion column; audio reactions still play). Picked per round; "Play by myself" opt-out exists.
- **Creature animations** reuse the old unicorn reaction CSS: creatures render as `.uni-svg.creature` with shared `face()` pieces (`.cheek/.mouth-happy/.mouth-sad/.tear`), so `happy()`/`sad()`/breathing/spin all work unchanged.
- **"Elsa" voice** (2026-07-12): read-aloud auto-picks the warmest female English voice on the device (`bestVoice()` scoring) and tunes it gentle/regal (`rate .9, pitch 1.12`). NOTE: the *real* Frozen/Idina Menzel voice is copyrighted and unavailable to browsers — this is the closest system-voice approximation. A **voice picker** on the name screen (`#voiceSel` + 🔊 Try) lets you choose any installed voice; choice saved in `localStorage['elsaVoice']`. On macOS, install premium voices (System Settings → Accessibility → Spoken Content → Manage Voices) for better quality.
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
- `state` — name, voice on/off, story, fed, goal (20), current, currentFrame, surpriseAt, voiceList/voiceObj, **`round`** `{creatureKey,variant,colors,done[]}`, **`companion`** `{key,variant,colors}|null`, **`collection[]`**, **`paintColor`**.
- **Creatures**: `CREATURES[]` (key,name,`draw`), `drawDragon/Phoenix/Pixie/Unicorn/Kitten(F,v)`, `variantFor(key)` (random shape knobs), `creatureSVG(key,colors,variant,extraClass,bw)`, `makeFill(colors,bw)` + `P(id,F,def)` (colorable-part fill resolver; `bw=true` forces white for the B&W selection/coloring canvas), shared `face(cx,cy,r)`, `DARK`, `PALETTE[]`.
- **Persistence**: `saveAll()`/`loadAll()` — `localStorage` keys `ange_collection`, `ange_round` (`{round,companion}`), `ange_name`, plus `elsaVoice`. Round resumes across sessions (goName → chooseStory if a round is in progress).
- Generators (each takes `lvl` 0..1, scaled via `lerp`): `makeArith(loMax,hiMax)`, `makeMakeN()`, `makeBonds(loWhole,hiWhole)`, `makeSkip(steps)`, `makeCount()`, `makeDoubles(loMax,hiMax)`, `makeAdd3(loMax,hiMax)`, `makeMult()`.
- `STORIES[]` — each has `key`, `gens[]`, `frames[]`, `parade`, `intro(name)`, `winTitle`, `win(name)`, themed `item` emoji, `goal`.
- `show(id)` — screen switcher (also toggles the floating 🎒). Screens: name, chooseCreature, colorCreature, chooseCompanion, chooseStory, intro, game, win, tame. Plus `#collection` modal (`openCollection`/`closeCollection`).
- Flow fns: `openChooseCreature`→`pickCreature`→`openColor`/`buildPalette`→`colorDone`→`openChooseCompanion`→`openChooseStory`→`openIntro`→`startStory`. `companionOrRoundSVG()`/`miniCreature()` render helpers.
- `newProblem()` / `renderAnswers()` / `choose()` — gameplay loop (3 options; ramps difficulty; triggers surprises). `winGame()` records `done`, branches to `showTame()` when all 3 finished.
- Reactions: `happy()`, `sad()`, `cheer()`, `sparkleAt()`, `funSurprise()`, `finale()`. Effects: `emojiRain()`, `paradeAcross()`, `floatUp()`, `flash()`.
- Audio: `tone()`, `melody()`, `sound()`, `whinny()`. Voice: `loadVoices()`, `bestVoice()`, `buildVoiceMenu()`, `speak()`.

## Verifying edits before deploy (now with a real DOM harness)
`jsdom` is installed in the session scratchpad. The integration test there loads `index.html`, stubs speech/audio, uses a fake-timer `drain()` to skip the inter-question delays, and drives the whole flow by clicking (name → pick creature → paint → auto-answer all 3 adventures → tame → collection → round-2 companion path). Re-run/adapt it after flow changes. Also still: `node --check` the extracted `<script>`, and the ~150k-run generator math validator if generators change. Then `open index.html`, commit, push.

## Verifying edits before deploy
The whole game is inline JS, so after edits: syntax-check (`node --check` on the extracted `<script>`) and, if generators changed, re-run the math validator (see git history / earlier session — it runs each generator ~150k times checking answers are correct, ≥0, and always among the 3 options). Then `open index.html` to eyeball, then commit + push.

## Ideas discussed for NEXT (not yet built)
- More fairy creatures + more colorable parts / variant knobs; maybe let her **name** each tamed creature.
- Per-creature spoken personality (different pitch / catchphrase per companion).
- A 4th "endless" free-play story with no goal.
- A "recolor" option to repaint a creature already in the collection; delete-from-collection.
- **Multi-game arcade**: once a 2nd game exists, build an `AngeGames` menu/home page linking to each game (one bookmark for Ange).
- Possible additions: Elsa/Anna cameo between rounds, word problems.

## Design principles to keep
Big touch buttons, minimal reading (spoken prompts), no punishment for wrong answers,
sessions ending in a win, lots of reward animation. (Sessions are now 20 problems w/ gentle difficulty ramp — watch that the "Big Kid" dragon story stays winnable for a 6-year-old; dial back the `hiMax` values if it feels too hard.)
