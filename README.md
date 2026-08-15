# speakeasy-terminal-access

Spec: [🕹️ Speakeasy Terminal Entry Experience Spec](https://app.notion.com/p/Speakeasy-Terminal-Entry-Experience-Spec-3967871fd43c800ea7a2e5f2be7c5a11)

A single-page, interactive terminal puzzle originally built for the DEXM Speakeasy and now reused and updated for the Dedalus Intern Expo. It renders a large purple-and-white terminal designed for an event monitor and drops the player into a simulated shell where they explore Dedalus Machines product differentiators and capture flags.

## Intern Expo format

The Intern Expo version replaces the earlier open-ended format, where participants completed as many challenges as possible before time expired and were ranked on a leaderboard. The updated experience has a single win condition:

- The participant has **3 minutes** from the initial connection.
- They must complete **3 challenges** and submit all **3 flags**.
- Completing all three challenges in time unlocks the prize screen.
- The prize is a **Dedalus hoodie**.
- If time expires first, the terminal locks and the participant must refresh to try again.

## Organizer instruction manual

### Start the terminal

There is no build step and there are no package dependencies. From the project directory, start a local web server:

```
python3 -m http.server 8000
```

Keep that Terminal window open, then open the experience in a browser:

```
http://localhost:8000/
```

Opening `index.html` directly also works on most browsers, but the local server is recommended because it loads the logo and other assets consistently.

### Choose a mode

Mode selection is controlled only through the URL, so participants do not see difficulty buttons.

- **B / Medium is the default:** `http://localhost:8000/`
- **A / Easy:** `http://localhost:8000/?level=A`
- **B / Medium explicitly:** `http://localhost:8000/?level=B`
- **C / Hard:** `http://localhost:8000/?level=C`

The `level` value is case-insensitive. If the URL contains an unknown value, the app falls back to A; with no `level` parameter, it loads B.

### Reset for the next participant

Refresh the browser page before every attempt:

- macOS: `Command + R`
- Windows/Linux: `Ctrl + R`
- Or click the browser’s refresh button.

Refreshing clears the current flags, timer, command history, generated files, and machine state. The participant returns to the initial connection screen, and the three-minute timer will not begin until they run the connect command.

The terminal command `restart confirm` performs the same in-page reset without reloading the browser.

### Change modes

Edit the `level` value in the browser address bar and press Enter. For example, change `?level=B` to `?level=A`, or remove the query string entirely to return to the default B mode. Loading the new URL starts a fresh session automatically.

### Stop the local server

When the event is over, return to the Terminal window running the server and press `Ctrl + C`.

## How the puzzle works

- The terminal starts disconnected. Running `dedalus ssh dm-0197f2a9` connects to the provisioned machine.
- Once connected, `help` lists available commands (`ls`, `cd`, `cat`, `dedalus machines ...`, `reboot`, `submit`, etc.).
- Three flags are hidden behind Dedalus Machine differentiators:
  - waking the sleeping machine Igris (`dedalus machines wake --machine-id dm-igris`)
  - benchmarking `dm-mcqueen` across five forced cold boots, measured until a guest command succeeds (using the discoverable script in the starting directory in Set A)
  - compute billing dropping to zero while a machine sleeps (`dedalus machines sleep` / `usage` on `dm-han`)
- `submit dedalus{...}` banks a found flag, `flags` shows progress, and `unlock` reveals the prize screen once all three are banked.
- `hint` gives a conceptual nudge without giving away exact commands.
- Refreshing the page or running `restart confirm` wipes the whole session back to zero.

## Three challenge sets (A / B / C)

Difficulty selection is URL-only so participants do not see the organizer labels. B loads by default; use `?level=A`, `?level=B`, or `?level=C` to load a specific set. Reloading with a different level starts a fresh session (flags, machine states, filesystem, history).

- **Set A (easy)** — wake `dm-igris` for a direct persistence flag, run the visible McQueen benchmark script without needing a machine argument for a direct speed flag, and sleep `dm-han` for a direct zero-compute flag.
- **Set B (medium, the current experience)** — wake Igris and inspect its generated lifecycle log; inspect the McQueen script and run it with `dm-mcqueen` to receive the flag; inspect Han’s running usage, sleep it, and inspect usage again.

- **Set C (hard)** — evidence-chain versions of the same differentiators: players must verify the virtualization layer as well as cold-start speed, test real disk against a misleading competitor-style mount, and observe an autosleep phase change before zero compute billing yields its flag.

`CHALLENGE_SETS` in `index.html` is the single place all three sets' flags and hints live.

## Files

- `index.html` — the entire app: terminal design and JS shell/puzzle logic, all inline.
