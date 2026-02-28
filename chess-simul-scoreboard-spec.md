# Chess Simul Scoreboard — Build Specification

## Overview

A lightweight, multi-device web application for running a Chess Simultaneous Exhibition ("Simul") scoreboard at a conference booth. A chess champion plays three simultaneous games. Players compete to survive as many moves as possible before being defeated. The app manages the sign-up queue, tracks active games across three boards, captures move counts, and displays an arcade-style high-score leaderboard.

---

## Tech Stack

| Layer | Choice |
|---|---|
| Frontend | Vanilla HTML, CSS, JavaScript (no frameworks) |
| Database | Firebase Realtime Database (real-time sync across devices) |
| Hosting | GitHub Pages (static hosting) |
| Auth | None — Firebase anonymous open read/write, admin via obscure URL |

> All Firebase config (API key, project ID, etc.) should be stored in a `firebase-config.js` file that is imported by each page. This is standard practice for client-side Firebase apps — security is enforced via Firebase Realtime Database Rules.

---

## Branding Guidelines

These guidelines are derived from the official Altimate AI color palette (`Colors.pdf`) and the live website (altimate.ai). Apply them across all pages to ensure the app looks on-brand at the Gartner booth.

---

### Typography

| Role | Font | Weight | Notes |
|---|---|---|---|
| All player-facing pages | `Lexend` | 400–700 | Altimate's primary font. Load via Google Fonts CDN |
| Scoreboard headings | `Press Start 2P` | 400 | Arcade/retro feel for the large display only |
| Scoreboard secondary text | `Lexend` or `Share Tech Mono` | 400 | For queue strip and runner-up rows |

Load both fonts in `<head>`:
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Lexend:wght@400;500;600;700&family=Press+Start+2P&family=Share+Tech+Mono&display=swap" rel="stylesheet">
```

---

### Colour Palette

These hex values come directly from the Altimate brand guide (`Colors.pdf`) and computed styles on altimate.ai.

#### Brand Colours
| Token | Hex | RGB | Usage |
|---|---|---|---|
| **Brand Blue** (primary) | `#247EFF` | rgb(36, 126, 255) | Primary CTA buttons, links, active states, icons |
| **Dark Navy** (heading) | `#082247` | rgb(8, 34, 71) | H1/H2 headings, dark text |
| **Brand Purple** (accent) | `#8B5CF6` | rgb(139, 92, 246) | Gradient endpoint, scoreboard highlights |
| **Gradient** (brand accent) | `#3F8CFF → #8B5CF6` | — | `linear-gradient(to right, #3F8CFF, #8B5CF6)` — use on scoreboard winner rows, winner badge |

#### Text Colours
| Token | Hex | Usage |
|---|---|---|
| Title / heading | `#082247` | Page headings |
| Body / paragraph | `#5E7493` | Descriptive text |
| Caption / secondary | `#808080` | Queue position labels, timestamps |
| On-dark / white | `#FFFFFF` | Text on coloured buttons or dark backgrounds |
| Disabled / muted | `#8390A0` | Placeholder rows, disabled states |

#### Background Colours
| Token | Hex | Usage |
|---|---|---|
| Page background (light pages) | `#F5F6F7` | join.html, status.html, thankyou.html |
| Dark base (scoreboard) | `#1E1E1E` | Scoreboard full-page background |
| Dark surface (scoreboard) | `#282929` | Winner / runner-up card backgrounds |
| Dark elevated (scoreboard) | `#3C3C3C` | Subtle panel borders on scoreboard |
| White | `#FFFFFF` | Cards, inputs, secondary buttons |
| Light blue tint | `#EBF5FF` | Highlighted input focus states |

#### Stroke / Border Colours
| Token | Hex | Usage |
|---|---|---|
| Default stroke | `#E8E8E8` | Input borders, card borders (light pages) |
| Active stroke | `#247EFF` | Focused input border |
| Disabled stroke | `#424758` | Disabled elements on dark backgrounds |
| Orange highlight | `#E38E00` | Use sparingly — warning states |

---

### Component Styles

**Buttons (player-facing pages):**
```css
/* Primary CTA — e.g. "Join the Queue!", "Submit My Score" */
background-color: #247EFF;
color: #FFFFFF;
font-family: 'Lexend', sans-serif;
font-weight: 600;
border-radius: 12px;
padding: 14px 28px;
border: none;

/* Secondary — e.g. "Book a demo" style */
background-color: #FFFFFF;
color: #082247;
border: 1px solid #E8E8E8;
border-radius: 12px;
```

**Inputs:**
```css
font-family: 'Lexend', sans-serif;
border: 1px solid #E8E8E8;
border-radius: 12px;
color: #082247;
background: #FFFFFF;
/* on focus: */
border-color: #247EFF;
outline: none;
```

**Scoreboard winner rows:**
```css
/* Gold/gradient treatment for winner entries */
background: linear-gradient(to right, #3F8CFF, #8B5CF6);
-webkit-background-clip: text;
-webkit-text-fill-color: transparent; /* for winner name */
/* or use as row border accent */
border-left: 4px solid #247EFF;
```

---

### Page-by-Page Application

| Page | Background | Heading colour | Accent | Font |
|---|---|---|---|---|
| `index.html` (scoreboard) | `#1E1E1E` | `#FFFFFF` / gradient | `#247EFF`, `#8B5CF6` | `Press Start 2P` + `Lexend` |
| `join.html` | `#F5F6F7` | `#082247` | `#247EFF` | `Lexend` |
| `status.html` | `#F5F6F7` | `#082247` | `#247EFF` | `Lexend` |
| `thankyou.html` | `#FFFFFF` | `#082247` | gradient | `Lexend` |
| `admin.html` | `#F5F6F7` | `#082247` | `#247EFF` | `Lexend` |

---

### Logo Usage

The `altimate-logo.svg` file should be displayed:
- **join.html**: centered, max-width 180px, top of page with ~24px padding below
- **index.html** (scoreboard): centered, max-width 220px, top of page
- **thankyou.html**: centered, max-width 260px, prominent placement

Do not apply filters, shadows, or colour tints to the SVG logo.

---

## Critical Implementation Notes for Claude Code

Read these before writing any code.

**1. Use relative paths everywhere — never absolute paths.**
The app is hosted at `https://frasermarlow.github.io/chess-simul-scoreboard/`. Any redirect or link using an absolute path like `/status.html` will resolve to `https://frasermarlow.github.io/status.html` and return a 404. Always use relative paths: `status.html`, `./join.html`, `thankyou.html`, etc.

**2. `firebase-config.js` must use ES module `export` syntax.**
All HTML pages import Firebase using `<script type="module">`. The config file must export its value: `export const firebaseConfig = { ... }`. Without `export`, the import will silently fail.

**3. Pin the Firebase CDN version — do not use wildcards.**
Use `https://www.gstatic.com/firebasejs/10.14.1/firebase-app.js` (and the same version for `firebase-database.js`). Do not write `10.x.x` or `latest` — those are not valid CDN paths.

**4. `status.html` must check Firebase on page load, not only via listener.**
A Firebase `onValue` listener only fires when data *changes*. If a player was already assigned to `"playing"` status before opening (or re-opening) `status.html`, the listener will never fire a change event. The page must read the player's current status once on load and act on it immediately, in addition to attaching the real-time listener.

**5. Auto-assignment must handle an empty queue gracefully.**
When a board is freed (player submits score or is cleared by admin), the auto-assignment logic must check whether any players with `status: "queued"` exist before attempting to assign. If the queue is empty, silently leave the board empty — do not throw or log an error.

---

## Firebase Realtime Database Structure

```json
{
  "queue": {
    "<playerId>": {
      "name": "Jane Smith",
      "status": "queued",       // "queued" | "playing" | "completed"
      "queuedAt": 1700000000000,
      "boardNumber": null,      // 1, 2, or 3 when playing
      "moves": null             // integer when completed
    }
  },
  "boards": {
    "1": { "playerId": null, "playerName": null },
    "2": { "playerId": null, "playerName": null },
    "3": { "playerId": null, "playerName": null }
  },
  "scores": {
    "<scoreId>": {
      "name": "Jane Smith",
      "moves": 42,
      "completedAt": 1700000001000
    }
  }
}
```

### Firebase Security Rules

Allow open read/write for the event (no auth required), but restrict destructive operations to the admin URL pattern if possible. At minimum:

```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

> Note to developer: For a production event, tighten rules to prevent arbitrary deletion except from known admin flows.

---

## Pages & Routes

| Route | Page | Primary User |
|---|---|---|
| `/` | Scoreboard Display | Large screen / TV at booth |
| `/join` | Player Sign-Up | Players on their phones (QR code target) |
| `/status` | Queue Status & Move Entry | Players on their phones (after sign-up) |
| `/thankyou` | Thank You | Players on their phones (after submitting) |
| `/admin` | Admin Panel | Organizers (hidden URL) |

> The QR code printed at the booth should point to `/join`.

---

## Page Specifications

---

### 1. Scoreboard Display (`/index.html` — root)

**Purpose:** Shown on a large TV or monitor at the conference booth. Automatically updates in real time as scores are submitted.

**Design — Arcade High-Score Aesthetic:**
- Dark background (deep navy or black)
- Bright accent colors (gold for winners, silver/white for runners up)
- Use a retro pixel/monospace font (e.g. Google Fonts: `Press Start 2P` or `Share Tech Mono`)
- Subtle scanline overlay or CRT-style visual effect for arcade feel
- Pulsing or animated entrance for new high scores

**Layout:**

```
[Altimate Logo — centered, top]

  ♟ CHESS SIMUL HIGH SCORES ♟

  ┌─────────────────────────────────┐
  │  🏆 WINNERS — TOP 3            │
  │                                 │
  │  1.  JANE SMITH         42 moves│
  │  2.  JOHN DOE           38 moves│
  │  3.  ALEX LEE           35 moves│
  └─────────────────────────────────┘

  ┌─────────────────────────────────┐
  │  RUNNERS UP                     │
  │  4.  SAM BROWN          33 moves│
  │  5.  PAT JONES          31 moves│
  │  ...                            │
  │  10. RILEY FORD         18 moves│
  └─────────────────────────────────┘

  [Currently Playing: Board 1: Jane  Board 2: —  Board 3: Alex]
  [Players in queue: 5]
```

**Scoreboard Logic:**
- Fetch and listen to `/scores` in real time via Firebase `onValue`.
- Sort scores descending by `moves`. Ties in moves are ordered by `completedAt` ascending (first to achieve the score ranks higher — though for prizes this is inconsequential).
- **Winners section:** Display the top 3 scores. If there is a **tie for 3rd place** (i.e. 3rd and 4th place have equal `moves`), expand the winners section to show 4 entries and add a note like `"Tie for 3rd — 4 winners!"`.
- **Runners Up section:** Display the next 7 players after the winners section (positions 4–10 normally, or 5–11 in a 4-winner tie scenario).
- If fewer than 3 scores exist, show placeholder rows with `---` for empty slots.
- **Currently Playing strip:** Read from `/boards` and display the player names on each of the 3 boards. Show `—` for empty boards.
- **Queue count:** Read from `/queue` filtered by `status === "queued"` and display count.
- Auto-refresh via Firebase real-time listeners — no manual refresh needed.

---

### 2. Player Sign-Up (`/join.html`)

**Purpose:** The QR code target. Players open this on their phone, enter their name, and join the queue.

**Design:**
- Mobile-first, clean and simple
- Altimate logo centered at top
- Large text input for name
- Prominent CTA button ("Join the Queue!")

**Flow:**
1. Player enters their name and taps "Join the Queue."
2. A new entry is written to `/queue` with `status: "queued"` and the current timestamp.
3. The generated `playerId` (Firebase push key) is saved to `localStorage` as `chessPlayerId`, and the player's name is saved as `chessPlayerName`.
4. Player is redirected to `status.html` (use a **relative path** — not `/status.html` — so it works correctly on GitHub Pages subdirectory hosting).

**Validation:**
- Name field is required (non-empty, trimmed).
- Minimum 2 characters, maximum 30 characters.
- If `localStorage` already contains a `chessPlayerId`, check Firebase to see if that player is still active (status `queued` or `playing`). If so, redirect directly to `status.html` (relative path) to prevent duplicate sign-ups.

---

### 3. Queue Status & Move Entry (`/status.html`)

**Purpose:** After signing up, the player's phone shows this page. It has two modes that switch automatically based on the player's status in Firebase.

**Mode A — Waiting in Queue:**
- Show the player's name and their current position in the queue.
- Show a message like: *"Get ready! The organizer will call you when a board is free."*
- On page load, **immediately read the player's current status from Firebase** (do not rely solely on the listener firing). If status is already `"playing"` when the page loads, jump straight to Mode B. This handles the case where the player closed their phone and reopened the page after being assigned.
- Also attach a Firebase `onValue` listener to `/queue/<playerId>`. When `status` changes to `"playing"`, automatically switch to Mode B without any page reload.
- If `localStorage` has no `chessPlayerId` (e.g. player navigated directly to this URL), redirect to `join.html`.
- Also show the live scoreboard or current board activity so the player stays engaged while waiting.

**Mode B — Enter Your Moves:**
- Show a large, clear heading: *"Game Over! How many moves did you play?"*
- Large number input (min 0, no max — but reasonable UX cap of 999)
- Prominent submit button: "Submit My Score"
- On submit:
  1. Write the score to `/scores` (`name`, `moves`, `completedAt` timestamp).
  2. Update `/queue/<playerId>` status to `"completed"`.
  3. Clear the board entry in `/boards/<boardNumber>` (set `playerId` and `playerName` to `null`).
  4. Trigger auto-assignment: query `/queue` for the next player with status `"queued"` (lowest `queuedAt`). If one exists, assign them to the now-free board number (update their `/queue/<id>/status` to `"playing"`, set `/queue/<id>/boardNumber`, and update `/boards/<boardNumber>`). If no queued players exist, simply leave the board empty — do not throw an error.
  5. Redirect the current player to `thankyou.html` (relative path).

> **Note on race conditions:** For a small event (max ~3 simultaneous submissions), client-side Firebase assignment is acceptable. If needed, this logic can be moved to a Firebase Cloud Function for atomicity.

---

### 4. Thank You Page (`/thankyou.html`)

**Purpose:** Confirmation screen shown after a player submits their score.

**Design:**
- Centered, full-screen, clean
- Large Altimate logo
- Tagline beneath the logo: **"The Agentic Platform for Data Engineering."**
- A warm thank-you message, e.g.: *"Thanks for playing! Check the scoreboard to see where you landed."*
- Optional: Link back to the scoreboard (`/`)
- No further interaction required

---

### 5. Admin Panel (`/admin.html`)

**Purpose:** Hidden management page for event organizers. No authentication — security through obscurity (unpublicised URL). Organizers should bookmark this page.

**Sections:**

#### A — Active Boards
Display the three boards and which player is currently on each. Allow the admin to:
- **Manually mark a board as complete:** Opens a modal to enter the move count on behalf of the player, then triggers the same score-submission and auto-assignment flow as above.
- **Clear a board** (e.g. player abandoned the game without submitting): Removes the player from active status without adding a score. Triggers auto-assignment for the next queued player.

#### B — Queue Management
Display the full queue (all players with `status: "queued"`) ordered by `queuedAt`.
- **Remove a player from the queue:** Deletes their entry from `/queue` (e.g. player opted out).
- **Manually assign the next player** to a specific board (in case auto-assignment failed or organizer wants control).

#### C — Score Entry on Behalf of Player
A simple form: enter player name + move count → writes directly to `/scores`. Useful if a player already left but the organizer recorded their score.

#### D — Scoreboard Management
- **Reset Scoreboard:** Clears all entries in `/scores`. Asks for a confirmation prompt ("Are you sure? This cannot be undone.") before executing.
- **Reset Everything:** Clears `/scores`, `/queue`, and `/boards` (full reset for a fresh start). Also requires confirmation.

**Admin UI Style:** Functional and clear — no need for arcade aesthetics. A clean, minimal admin dashboard style.

---

## Assets

| File | Usage |
|---|---|
| `altimate-logo.svg` | Displayed on: Scoreboard (top), Sign-Up page (top), Thank You page (large/centered) |

Reference the logo as a relative path from each HTML file, e.g. `<img src="altimate-logo.svg" ...>` or `<img src="../altimate-logo.svg" ...>` depending on folder structure.

---

## Project Structure

```
/
├── index.html              ← Scoreboard display
├── join.html               ← Player sign-up (QR code target)
├── status.html             ← Queue position & move entry
├── thankyou.html           ← Post-submission thank you
├── admin.html              ← Admin panel (hidden URL)
├── firebase-config.js      ← Firebase project config (imported by all pages)
├── altimate-logo.svg       ← Altimate AI logo
├── style.css               ← Shared base styles
├── scoreboard.css          ← Arcade-specific styles for index.html
└── README.md               ← Setup and deployment instructions
```

---

## Firebase Setup Instructions (for README)

The Firebase project (`gartner-chess-event`) has already been created and configured. The Realtime Database is live at `https://gartner-chess-event-default-rtdb.firebaseio.com` with test-mode rules (open read/write until 2026-03-30).

The `firebase-config.js` file should contain the following **real credentials** — do not use placeholders:

```js
// firebase-config.js
const firebaseConfig = {
  apiKey: "AIzaSyCOuDDp0wn7Dhw9VW0OTNxhCjMGgj0Y0V8",
  authDomain: "gartner-chess-event.firebaseapp.com",
  databaseURL: "https://gartner-chess-event-default-rtdb.firebaseio.com",
  projectId: "gartner-chess-event",
  storageBucket: "gartner-chess-event.firebasestorage.app",
  messagingSenderId: "924783521413",
  appId: "1:924783521413:web:7a23375354f3a094e683ec",
  measurementId: "G-17BX8JP3TK"
};
```

The `firebase-config.js` file must use ES module `export` syntax so it can be imported by each page:

```js
// firebase-config.js  ← use this exact format with "export"
export const firebaseConfig = {
  apiKey: "AIzaSyCOuDDp0wn7Dhw9VW0OTNxhCjMGgj0Y0V8",
  authDomain: "gartner-chess-event.firebaseapp.com",
  databaseURL: "https://gartner-chess-event-default-rtdb.firebaseio.com",
  projectId: "gartner-chess-event",
  storageBucket: "gartner-chess-event.firebasestorage.app",
  messagingSenderId: "924783521413",
  appId: "1:924783521413:web:7a23375354f3a094e683ec",
  measurementId: "G-17BX8JP3TK"
};
```

Import Firebase via CDN in each HTML file using a **pinned version** (do not use `x` wildcards):

```html
<script type="module">
  import { firebaseConfig } from './firebase-config.js';
  import { initializeApp } from "https://www.gstatic.com/firebasejs/10.14.1/firebase-app.js";
  import { getDatabase, ref, set, push, onValue, update, remove }
    from "https://www.gstatic.com/firebasejs/10.14.1/firebase-database.js";

  const app = initializeApp(firebaseConfig);
  const db = getDatabase(app);
</script>
```

---

## GitHub Pages Deployment

The local project folder has already been initialised with `git init`. Follow these steps to create the remote repo and deploy.

### Step 1 — Create the GitHub repository

Run the following command from the project root. This uses the GitHub CLI (`gh`) to create a new **public** repo under the `frasermarlow` account and push the initial commit:

```bash
# Stage all project files
git add .
git commit -m "Initial commit: chess simul scoreboard"

# Create the remote repo and push (requires gh CLI to be installed and authenticated)
gh repo create frasermarlow/chess-simul-scoreboard \
  --public \
  --source=. \
  --remote=origin \
  --push
```

If the `gh` CLI is not available, create the repo manually:
1. Go to [https://github.com/new](https://github.com/new)
2. Set Owner to **frasermarlow**, Repository name to **chess-simul-scoreboard**, visibility **Public**
3. Do **not** initialise with a README (the local repo already has files)
4. Click **Create repository**, then run:

```bash
git remote add origin https://github.com/frasermarlow/chess-simul-scoreboard.git
git branch -M main
git push -u origin main
```

### Step 2 — Enable GitHub Pages

1. Go to `https://github.com/frasermarlow/chess-simul-scoreboard/settings/pages`
2. Under **Build and deployment → Source**, select **Deploy from a branch**
3. Set Branch to `main`, folder to `/ (root)`
4. Click **Save**

GitHub Pages will publish within ~60 seconds. The live URLs will be:

| Page | URL |
|---|---|
| Scoreboard (large screen) | `https://frasermarlow.github.io/chess-simul-scoreboard/` |
| Player sign-up (QR target) | `https://frasermarlow.github.io/chess-simul-scoreboard/join.html` |
| Admin panel (hidden) | `https://frasermarlow.github.io/chess-simul-scoreboard/admin.html` |

### Step 3 — Subsequent deployments

Any future changes are deployed by simply pushing to `main`:

```bash
git add .
git commit -m "your message"
git push
```

GitHub Pages will rebuild automatically within ~60 seconds.

> **Important:** `firebase-config.js` will be publicly visible in the GitHub repo. This is acceptable for a short-lived event app. Firebase Realtime Database test-mode rules expire 2026-03-30 — no further action needed for the event window.

---

## Key UX Notes

- **Mobile-first:** `/join.html` and `/status.html` are used on players' phones. Large tap targets, readable font sizes (min 16px), minimal scrolling.
- **Scoreboard display:** `/index.html` is shown on a large TV. Design for legibility at ~3 meters. Large font sizes (48px+ for winner names), high contrast.
- **Real-time updates:** All pages use Firebase `onValue` listeners so no manual refresh is ever needed.
- **No timer in the app.** Players use physical chess clocks. The app does not track or enforce the 10-minute limit.
- **No login/auth required** for players or admins.

---

## Out of Scope

- QR code generation (organizer generates separately using any QR tool pointing to `/join.html`)
- Chess move validation
- Game timer
- Email/SMS notifications
- Player authentication
