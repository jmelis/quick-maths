# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

A small arithmetic game for a young child, who is good at maths and puzzles. Favour playful, puzzle-style framings over drill. It is one self-contained page, not an application: no build, no dependencies, no tests.

## Layout

- `index.html`: the whole game (HTML, CSS and JS in one file). Open it by double-click.
- `.github/workflows/pages.yml`: publishes `index.html` to GitHub Pages (https://jmelis.github.io/quick-maths/) on every push to `main`. On the way it stamps `const BUILD = 'dev';` with a hash of the file and writes a matching `version.json` next to it (see Updates below); the line must stay exactly like that, at the start of a line, or the deploy fails.
- `README.md`: short description for visitors.

## The game

`index.html`: single-file, dependency-free web app with two games, picked by a switch at the top of setup (`[Classic | Drills]`). Both share the same play screen: the equation is one row and the answer box sits on its own row below it, at a fixed width that fits the longest possible answer (3 digits), so it never resizes as she types. Answers are accepted automatically as soon as the typed number matches; a wrong answer clears the box and she can't move on until it is right (the question is tallied as a "slip"). A ✕ at the top left stops a game: during the 3-2-1 it leaves at once; after that it pauses (clock stopped, question covered, keypad dimmed) and asks "Stop this game?" with Keep playing / Stop (Esc does the same). Paused time doesn't count in either game, and a stopped game isn't saved or counted as an attempt.
- **Classic**: Zetamac-style 2-minute countdown. One range for the first number (n1) and one for the second (n2), plus any combination of the three signs. Questions read `n1 sign n2`, so e.g. n1 = 7 to 7, n2 = 1 to 10, × is the 7 times table. Default: n1 1 to 10, n2 1 to 10, adding only. Subtraction never goes negative: if n1 < n2 the second number is redrawn to fit under n1, and only if n2's whole range is above that n1 are the two swapped. 1 point per question (a point still counts after a slip); score = points, higher is better.
- **Drills**: inputs are a number, a max and one sign (+ or ×); default 6, 9, +. It is a count-up race against the clock, not a countdown: the timer starts after the 3-2-1 and stops at the last answer; score = time in tenths of a second, lower is better. The list for number 6, max 9, + is: (1) 6+1 to 6+9 once, the number always first (never 1+6): in order for × and shuffled for + (`DRILL_FIRST_PASS`; for + the answers would just go up by 1); (2) then, all shuffled together, 6+1 to 6+9 twice as they are and once turned round (1+6 to 9+6). Any question failed on the first try is appended to the very end of the list (so the list grows with every slip, and a slip on a repeat is appended again). The play screen shows done/total and a bar that fills.
- Records: best score kept in `localStorage`, one record per configuration (Classic key `120s|n1:1-10|n2:1-4|ops:add,mul`, Drills key `drill|n:6|max:9|op:add`), plus a games-played count and the date the record was set (a record saved before counting existed counts as 1 attempt). A Records screen (reached from setup and result) lists every configuration with its best, attempts and date, a ▶ button loads that setup, switching to the right game, and a 🗑 button deletes the record (the row asks "Delete this record?" first; works for old rows without a ▶ too). Data is per browser; nothing syncs between devices. Classic settings and which game is picked live under `quickmaths.custom.v1` (the key predates the rename of Custom to Classic), Drills settings under `quickmaths.drill.v1`.
- Migration (`migrateRecords`, runs on every load, idempotent): the original Classic game had one range per operation (key `120s|add1-4|mul1-4`) and was dropped when Custom became Classic. Old records whose operations all share one range are rewritten to the equivalent new key (n1 = n2 = that range) and merged with any record already there (best score, attempts added). Old records with different ranges per operation can't be expressed any more: they stay in Records as rows without a ▶ button.
- Updates (for the iOS home-screen icon, which is usually resumed rather than reloaded, and may load from the HTTP cache): on load, on every return to the app (`visibilitychange`) and on a back/forward-cache restore, `checkForUpdate` fetches `version.json` (`no-store`); if its build isn't this page's `BUILD`, the page reloads itself from `?v=<build>`, a URL no cache has seen. Never mid-game or over a result: then it waits until she is back on setup (`toSetup` calls `applyUpdate`). If the page at `?v=<build>` is still old, it doesn't try again (no loop). `BUILD = 'dev'` (opened from disk) never checks.
- `DURATION`, `LIMITS`, `DRILL_LIMITS`, `DEFAULT_MODE`, `DEFAULTS_CLASSIC`, `DEFAULTS_DRILL` and `STRINGS` at the top of its script control the games and wording.

## Conventions

- Strings shown to the learner live in one `STRINGS` object at the top of the script, so they can be translated.
- Keep it a single file with no dependencies, so it keeps working when opened straight from disk.
- Don't put personal details (names, birth dates) in this repo: it is public.
