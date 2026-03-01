# Chess Simul Scoreboard

Real-time scoreboard for a Chess Simultaneous Exhibition at the Gartner Data & Analytics Summit booth. A chess champion plays up to three games at once. Players sign up via QR code, get queued, play, and submit their move count. A large display shows an arcade-style leaderboard.

**Live site:** https://frasermarlow.github.io/chess-simul-scoreboard/

## Pages

| URL | Purpose | User |
|-----|---------|------|
| [`/`](https://frasermarlow.github.io/chess-simul-scoreboard/) | Scoreboard display | Large TV/monitor at booth |
| [`/join.html`](https://frasermarlow.github.io/chess-simul-scoreboard/join.html) | Player sign-up (QR code target) | Players on mobile |
| `/status.html` | Queue position & move submission | Players on mobile (redirected after sign-up) |
| `/thankyou.html` | Post-submission confirmation | Players |
| [`/admin.html`](https://frasermarlow.github.io/chess-simul-scoreboard/admin.html) | Admin panel | Event organizers |

## How It Works

1. **Player scans QR code** → opens `join.html` → enters their name → joins the queue
2. **Player waits** on `status.html` showing their queue position and live activity counts
3. **Admin sets player to "playing"** from the admin panel (up to 3 at a time)
4. **Player's phone updates automatically** to show "You're up!" alert
5. **After the game**, player enters their move count and optionally reports a win or draw
6. **Score appears on the scoreboard** in real time

## Scoring

- Players are ranked by move count (higher = better — you survived more moves against the champion)
- **Win bonus:** +50 ranking points (shown with 👑 on scoreboard)
- **Draw bonus:** +25 ranking points (shown with faded 👑 on scoreboard)
- The scoreboard displays actual moves played; bonuses affect ranking only
- Admins can override any player-submitted outcome

## Tech Stack

- **Frontend:** Vanilla HTML, CSS, JavaScript — no frameworks, no build step
- **Database:** Firebase Realtime Database (client-side SDK v10.14.1 via CDN)
- **Hosting:** GitHub Pages (static files)
- **Fonts:** Lexend, Share Tech Mono (Google Fonts)

## Firebase Setup

The Firebase project `gartner-chess-event` is already configured. Database rules allow open read/write until **March 14, 2026**, after which access is automatically revoked.

### Security

- Firebase API keys are client-side identifiers, not secrets — security is enforced by database rules
- The API key should be restricted to `frasermarlow.github.io/*` in the [Google Cloud Console](https://console.cloud.google.com/apis/credentials?project=gartner-chess-event)
- Database rules are in `database.rules.json` — deploy via the Firebase Console

### Deploying Rules

Paste the contents of `database.rules.json` into the [Firebase Realtime Database Rules editor](https://console.firebase.google.com/project/gartner-chess-event/database/rules) and click Publish.

## Firebase Data Model

```
/queue/<playerId>  → { name, status, queuedAt, moves }
/scores/<scoreId>  → { name, moves, outcome, rankingScore, completedAt }
```

- `status`: `"queued"` | `"playing"` | `"completed"`
- `outcome`: `"lost"` | `"won"` | `"drew"`
- `rankingScore`: moves + bonus (50 for win, 25 for draw, 0 for loss)
- Up to 3 players can have `"playing"` status simultaneously

## Deployment

Any push to `main` triggers a GitHub Pages rebuild (~60 seconds).

```bash
git add .
git commit -m "your message"
git push
```

## Admin Guide

The admin panel (`admin.html`) provides:

- **Currently Playing** — see active players, complete them with a move count and outcome, or clear without scoring
- **Queue** — view queued players, remove them, or manually start them playing
- **Score Entry** — manually add a score (useful if a player already left)
- **Submitted Scores** — view all scores, override outcomes (Lost/Won/Drew), or delete entries
- **Reset** — clear the scoreboard or reset everything (with confirmation)

## Project Structure

```
├── index.html           ← Scoreboard display
├── join.html            ← Player sign-up (QR target)
├── status.html          ← Queue status & move submission
├── thankyou.html        ← Post-submission thank you
├── admin.html           ← Admin panel
├── firebase-config.js   ← Firebase credentials (ES module)
├── style.css            ← Shared base styles
├── scoreboard.css       ← Scoreboard-specific styles
├── database.rules.json  ← Firebase security rules
├── firebase.json        ← Firebase CLI config
├── altimate-logo.svg    ← Altimate AI logo
├── chess-simul.png      ← QR code image
└── CLAUDE.md            ← AI assistant instructions
```
