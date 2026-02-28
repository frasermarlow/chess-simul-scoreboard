# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Real-time chess simultaneous exhibition scoreboard for a Gartner booth event. Players sign up via QR code on their phones, get queued, play chess on one of 3 boards, then submit their move count. A large display shows a retro arcade-style leaderboard.

## Tech Stack

- **Frontend:** Vanilla HTML5, CSS3, JavaScript (no frameworks, no build system)
- **Database:** Firebase Realtime Database (client-side SDK v10.x via CDN)
- **Hosting:** GitHub Pages (static files only)
- **Fonts:** `Press Start 2P` and `Share Tech Mono` from Google Fonts

There is no package.json, no bundler, no test runner, and no linter. Files are served as-is.

## Architecture

### Pages

| File | Purpose | Primary User |
|------|---------|--------------|
| `index.html` | Arcade-style scoreboard display | Large TV/monitor |
| `join.html` | Player name entry & queue registration | Players (mobile, QR target) |
| `status.html` | Queue position display + move count submission | Players (mobile) |
| `thankyou.html` | Post-submission confirmation | Players |
| `admin.html` | Queue/score management | Event organizers |

### Firebase Data Model

```
/queue/<playerId>    → { name, status, queuedAt, moves }
/scores/<scoreId>    → { name, moves, completedAt }
```

- `status` values: `"queued"` | `"playing"` | `"completed"`
- Up to 3 players can have `"playing"` status simultaneously (no board assignment — facilitators manage physical boards)
- All pages use Firebase `onValue()` listeners for real-time sync
- Firebase config lives in `firebase-config.js`

### Key Business Logic

- Scores sort by moves descending, ties broken by `completedAt` ascending
- Scoreboard shows top 3 winners + runners-up (4-10), with placeholder rows if fewer than 3 scores
- Player identity stored in localStorage (`chessPlayerId`, `chessPlayerName`)
- Duplicate name check on sign-up redirects returning players to `status.html`
- Auto-assign: when a player completes, the next queued player is promoted to `"playing"` if fewer than 3 are playing

## Specification

The full spec is in `chess-simul-scoreboard-spec.md`. Refer to it for detailed UI requirements, styling details, and edge case handling.
