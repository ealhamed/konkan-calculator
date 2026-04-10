# كنكان Calculator (Konkan)

Standalone Konkan card-game score tracker PWA — sibling to sibeet and baloot calculators in the الديوانية suite.

## Stack
- Single-file PWA (`index.html`)
- PeerJS v1.5.4 for P2P multiplayer (host ↔ viewers, star topology)
- Firebase Realtime Database as scaling fallback when PeerJS broker is unavailable
- QRCode.js for room QR codes
- Service Worker (`sw.js`, cache `konkan-v3`) for offline caching
- Al Dewaniah visual identity (burgundy #722F37 / gold #C9A227 / navy #1A2744 / cream #F5F0E6, Cairo font)

## Commands
```bash
npx http-server . -p 8082    # Local dev server
```

## Live URL
https://ealhamed.github.io/konkan-calculator/

## Files
| File | What |
|------|------|
| `index.html` | Calculator app (UI + game logic + P2P + Firebase) |
| `sw.js` | Service worker (cache-first) |
| `manifest.json` | PWA manifest |
| `firebase.json` | Firebase deploy config (database-only) |
| `database.rules.json` | Time-gated public RTDB rules |
| `.firebaserc` | Default project: `konkan-calculator-al-dew` |
| `logo.png`, `icon-192.png`, `icon-512.png` | Branding |

## How It Works

### Game Rules
- Variable player count (4 default, up to 20, editable mid-game)
- Target score default 1000; first to reach target loses, lowest wins
- Each round a player **ends** the game in one of three modes:
  - **هند** (hand-out): ender gets −60. Everyone else scores card points in hand; unreported players assumed to hold a full set = 200 (×2 = 400 if ender went فل; halved to 100 if anyone at the table has started putting cards down فل)
  - **فل** (partial lay-down): ender gets −30. Others score 100 default, or actual card points
  - Standard round: per-player point entry
- Card weights: K/J/Q = 10, Ace = 1 or 10 depending on position

### Layout (Option D)
- Grid of player cards (name tap-to-rename, running total, progress bar)
- هند / فل toggles **inside** each player card (sibeet-style)
- Per-card numeric input (mobile-friendly `inputmode="numeric"`, no spinner arrows, dash placeholder)
- Bottom actions: تراجع (undo with haptic `[10,30,10]`) + لعبة جديدة
- No separate setup screen — app boots directly into the game with 4 default players

### Multiplayer (Room System)
- **PeerJS mode** (default): host generates 4-digit code, viewers scan QR or enter code; star-topology, ~8 viewer soft cap
- **Firebase mode** (auto-fallback): on PeerJS broker error, app silently switches to Firebase RTDB with `F-XXXX` code prefix, removing viewer cap
- **Viewers are read-only** — host's device is the single source of truth. No "allow editing" toggle (removed; the fallback makes per-session opt-in unnecessary)
- State sync via `getStatePayload()` broadcasting players/rounds/totals/target

### Firebase Project
- Project ID: `konkan-calculator-al-dew` (each calculator has its own Firebase project for isolated 100-concurrent quota)
- RTDB structure: `rooms/{code}/state`, `rooms/{code}/presence/{id}`, `rooms/{code}/messages/{pushId}`
- Rules: time-gated public read/write (`now < 1900000000000`)

### Shared Features (parity with sibeet/baloot)
- `appConfirm()` / `appPrompt()` custom dialogs
- `pushOverlay()` + popstate for back-button integration
- Auto-dim overlay after 15s idle
- Dark mode with SVG moon/sun toggle
- Haptic feedback (`navigator.vibrate`) on submit, toggles, undo
- iOS Safari bottom-strip fix (`html { background: var(--bg); min-height: 100% }`)
- `prefers-reduced-motion` respected, aria-labels, focus-visible

## Repos
- **konkan-calculator**: https://github.com/ealhamed/konkan-calculator
- **Siblings**: [sibeet-calculator](https://github.com/ealhamed/sibeet-calculator), [baloot-calculator](https://github.com/ealhamed/baloot-calculator)
