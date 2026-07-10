# speakeasy-terminal-access

Spec: [🕹️ Speakeasy Terminal Entry Experience Spec](https://app.notion.com/p/Speakeasy-Terminal-Entry-Experience-Spec-3967871fd43c800ea7a2e5f2be7c5a11)

A single-page, interactive retro-terminal puzzle for the DEXM Speakeasy (Dedalus Startup School afterparty). It renders a CRT-style monitor in the browser and drops the player into a simulated shell where they explore a fake filesystem, wake and sleep machines, and bank flags to unlock the party details (password, location, time).

## Running it

No build step or dependencies. Just open the file in a browser:

```
open index.html
```

or serve it locally:

```
python3 -m http.server 8000
```

and visit `http://localhost:8000`.

## How the puzzle works

- The terminal starts disconnected. Running `dedalus ssh dm-0197f2a9` connects to the provisioned machine.
- Once connected, `help` lists available commands (`ls`, `cd`, `cat`, `dedalus machines ...`, `reboot`, `submit`, etc.).
- Three flags are hidden behind three different properties of a real Dedalus Machine (vs. a locked-down sandbox):
  - waking a sleeping machine (`dedalus machines wake --machine-id dm-cellar`)
  - persistent storage surviving a `reboot`
  - compute billing dropping to zero while a machine sleeps (`dedalus machines sleep` / `usage` on `dm-kiln`)
- `submit dedalus{...}` banks a found flag, `flags` shows progress, and `unlock` reveals the door password/location/time once all three are banked.
- `hint` gives a conceptual nudge without giving away exact commands.
- The red button under the screen (or `restart confirm`) wipes the whole session back to zero.

## Files

- `index.html` — the entire app: SVG terminal chassis, CRT overlay, and the JS shell/puzzle logic, all inline.
