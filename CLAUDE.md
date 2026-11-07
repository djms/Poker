# Poker Table — project context

Single-file, phone-first Texas Hold'em table tracker for live home games. Everything lives in `poker-table.html` (HTML + CSS + vanilla JS, no build step, no dependencies). `index.html` only redirects to it.

## Purpose
Daniel plays a low-stakes home game (10c/20c blinds, chips 10c/20c/50c/$1/$5) and uses this page on his iPhone to see the live situation: his hand, the board, exposed/burned cards, who is still in, the pot, and whether a call is correct. The design goal is **minimum taps while a hand is live**.

## Where it runs
- Source of truth: this folder, git repo pushed to **https://github.com/djms/Poker** (personal account `djms`, NOT the Lumenalta account; `gh auth switch --user djms` before pushing; commits use `15113403+djms@users.noreply.github.com`).
- Public page: **https://djms.github.io/Poker/** (GitHub Pages, branch `main`, root). Deploys take 2-5 min; if a deploy stalls, push an empty commit to get a fresh deployment ID.
- Private mirror: Claude artifact https://claude.ai/artifact/9uYQpw5BmEHtT8thSQAjL8 (republish the same file path from Claude Code to update).
- After any change: commit, push, and republish the artifact.

## Features (as of 2026-11-07)
- Oval table, 4-10 players, hero at the bottom. Seat order is clockwise = poker action order. Gear menu sets player count.
- Card picker bottom sheet: suit first, then rank in A,2..K order; taken cards dimmed with their location. Keyboard shortcuts h/d/c/s then rank work on desktop (not advertised).
- Per-seat face-down cards; tap to enter exposed cards. Tap a seat name to fold (fades out, excluded from equity). Burned/exposed pile under the board.
- Board: 5 slots that unlock in order; phase label derived from count.
- Dealer button + SB/BB badges. `D ›` corner button advances the dealer (re-posts blinds if no action yet). **New hand** = clear cards/bets/pot/folds, move dealer, post blinds.
- Chip tracking: per-seat `Call` (one tap) and `Bet`/`Raise` (chip popup, tap chips the player pushes in). `All call` corner button. Bets sweep into the pot with a flying-chip animation when the flop/turn/river goes in, or via `Bets → pot`.
- Odds panel: players still in, hero position name, best hand, starting-hand chart verdict (pre-flop), Monte Carlo equity vs active opponents (random hands, known cards respected), outs counts and hit probabilities. Collapsible sections: Outs detail (card list), Starting hands chart (Unibet chart reproduced exactly, hero cell highlighted, position-aware Play/Fold), Unseen cards.
- "Should I call?" panel: pot and to-call auto-fill from tracked chips (typed values override until `↺`); shows pot odds, required equity, verdict Call / Thin call / Fold.
- Gear: deck colors (4-color default or red/black), blinds, chip names/values, **Track stacks & side pots** (off by default; stack tap-to-edit, all-in badge, side pot breakdown), **Training mode**.
- Training mode: `New hand` deals random cards; bots act by simple rules (pre-flop by chart category + position, post-flop by quick equity vs pot odds, capped at 15bb per bet, one raise per street); hero acts via the fixed bottom bar; at showdown all cards are revealed, pots awarded (side pots if stacks on), and each hero decision is graded good/bad against equity vs required equity. Running score persisted.
- State persists in localStorage key `pokerTableState.v1` (with `migrate()` filling new fields).

## Code map (poker-table.html)
- `// --- PURE START/END`: hand evaluator (`evaluate`, `describe`), chart data (`CHART`, `chartCat`, `handKey`), `posInfo`, `chartAdvice`, `sidePots`. These are extracted and unit-tested in node (see Testing).
- State `S`, accessor object `P` (index 0 = hero, 1..n-1 = seats), `render()` rebuilds the table DOM.
- Betting: `putIn`, `doCall`, `allCall`, `postBlinds`, `collectBets(animate, done)` (timer-driven, not animation-event-driven).
- Sheet modes: `card`, `chips`, `number`.
- Odds: `simulate()` Monte Carlo, `computeOdds()`, `renderPot()`, `renderChart()`.
- Training: `T` in-memory hand state, `trainDeal`, `startStreet`, `nextAction`, `botAct`, `heroDecision`, `endStreet`, `showdown`, `renderTrain`.

## Testing
No test framework. Pattern used so far (from a scratch dir):
1. `sed -n '/<script>/,/<\/script>/p' poker-table.html | sed '1d;$d' > whole.js && node --check whole.js`
2. Extract `PURE START..END` into a file and `eval` it in a node script with the RANKS/RK/SUITS constants defined; assert evaluator, chart, positions, side pots.
3. Headless Chrome (`/Applications/Google Chrome.app/Contents/MacOS/Google Chrome --headless=new`) for screenshots: wrap the page in a 390px iframe because Chrome enforces a minimum window width. Seed state by `sed`-replacing `let S = load();` and add `window.onerror` writing to `document.title` to catch runtime errors. Training hands can be auto-played with a `setInterval` that clicks `barCheck` when `T.waiting`, then `--dump-dom`.

## Decisions and conventions
- Equity assumes opponents hold random cards; it is the optimistic baseline. Range-based equity from betting actions was discussed and rejected for now (false precision, too many taps).
- Outs = unseen cards that raise the hero's hand category (board-pairing cards included; documented in the UI).
- Folded seats are excluded from equity; the "fair share" shown is 1/(players still in).
- No em-dashes in UI copy. Money shown as `$0.20`. Amounts rounded to cents (`r2`).
- Keep everything in one file; keep phone layout at 390px working (seats shrink and the table gets taller at 8+ players).

## Ideas not built yet
- Range-adjusted equity from raise/call tags (see decision above).
- Hand history export.
- Smarter training bots (bet sizing by board texture, bluffing patterns).
