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
- **3 stories** (difficulty matches storyline), each 10 problems to win:
  1. 🧁 The Frozen Cupcake Party — Easy (numbers to 20): add/subtract, make-10, counting.
  2. 🌉 Rescue the Baby Unicorn — Medium (to 50): add/subtract, number bonds, skip-count 2/5/10.
  3. 🐉 Break the Ice Dragon's Spell — Big Kid (to 100): add/subtract, harder bonds, skip-count 3/5/10.
- Read-aloud via Web Speech API (🔊/🔇 toggle). Sounds via Web Audio (no files).
- 🏠 button returns to story menu.

## Code map (inside the single file, in the `<script>`)
- `state` — name, uni, story, fed count, goal, voice, current problem.
- `UNICORNS[]` — color palettes. `unicornSVG(u, tag)` — builds the SVG (tag makes gradient IDs unique).
- Generators: `makeArith(max)`, `makeMake10()`, `makeBonds(maxWhole)`, `makeSkip(steps)`, `makeCount()`.
- `STORIES[]` — each has `gens[]`, `intro(name,uni)`, `winTitle`, `win(name)`, themed `item` emoji.
- `show(id)` — screen switcher. Screens: name, chooseUni, chooseStory, intro, game, win.
- `newProblem()` / `renderAnswers()` / `choose()` — gameplay loop (3 answer options, correct + 2 near distractors).
- Reactions: `happy()`, `sad()`, `cheer()`, `sparkleAt()`. Audio: `sound()`, `whinny()`, `speak()`.

## Ideas discussed for NEXT (not yet built)
- Give each unicorn a spoken personality (voice pitch / catchphrase).
- A 4th "endless" free-play story with no goal.
- Save progress between sessions (stickers/medals per story) via localStorage.
- Publish as a shareable link (Artifact) so she can play on a tablet without the file.
- Possible additions: Elsa/Anna cameo between rounds, word problems.

## Design principles to keep
Big touch buttons, minimal reading (spoken prompts), no punishment for wrong answers,
short 10-problem sessions ending in a win, lots of reward animation.
