# Poker

A single-file, phone-first Texas Hold'em table tracker. Enter your hand, the community cards, any exposed or burned cards, and see your best hand, outs and equity against the players still in.

**Live page:** https://djms.github.io/Poker/

## Features

- Oval table for 4 to 10 players, you at the bottom
- Tap any slot to pick a card: suit first, then rank
- Exposed opponent cards and a burned/exposed pile are removed from the deck
- Five community slots that unlock in order (flop, turn, river)
- Best hand, outs with hit probabilities, and Monte Carlo equity that update as cards go in
- Tap a seat name to mark it folded
- Dealer button with automatic small and big blinds
- Chip tracking per seat (call in one tap, bet/raise from a chip popup), bets slide into the pot on each street
- "Should I call?" panel with pot odds filled in from the tracked chips
- Starting hands chart (pairs/suited and unsuited) with your hand highlighted and position advice
- Optional stacks and side pots
- Training mode: random deals against rule-based bots, showdown reveal, and grading of your decisions
- Four-color or red-and-black deck, editable chip values and blinds in the gear menu
- State survives a refresh (browser local storage)
- No dependencies: one HTML file

## Run locally

Open `poker-table.html` in any browser.
