# Notice — PWA setup

## Files
- `index.html` — the app (now with inline "add" rows instead of popups, and update-prompt logic)
- `manifest.json` — makes it installable, and controls the bezel-free "standalone" look
- `sw.js` — service worker: caches the app and drives the auto-update prompt
- `icon-192.png` / `icon-512.png` — placeholder app icons (swap these for your own art, same filenames)

Upload all five files to the root of your GitHub repo (same folder, no subfolders — the paths inside `index.html`, `manifest.json`, and `sw.js` all assume that).

## How the update flow works
1. Every time you deploy, edit `sw.js` and bump the `VERSION` string at the top (`v1` → `v2`, etc.). This is the one required step — a service worker only checks for updates by comparing the *bytes* of `sw.js` byte-for-byte, so if you forget this, browsers may not notice anything changed.
2. When someone has the app open (or opens it again), the browser fetches the new `sw.js`, installs it in the background, and `index.html` shows a small "A new version is ready" toast with a **Refresh** button.
3. Tapping Refresh swaps in the new version and reloads — no app-store-style update needed.
4. The app also checks for updates automatically whenever it's reopened/foregrounded, and every 5 minutes while left open.

If you ever want updates to apply *immediately* with no prompt at all, that's possible too, but a silent reload can wipe someone's half-typed journal entry — the prompt exists to avoid that.

## Installing with no browser bezel (no URL bar)

This works today with just these files — no separate "app files" needed beyond what's here.

**Android (Chrome):** open the site → menu (⋮) → **Add to Home screen** / **Install app**. Because `manifest.json` has `"display": "standalone"`, it opens full-screen with no address bar, and shows your icon like a native app.

**iPhone/iPad (Safari):** open the site → Share icon → **Add to Home Screen**. The `apple-mobile-web-app-capable` meta tag already in `index.html` is what makes iOS launch it standalone instead of inside Safari.

**Desktop (Chrome/Edge):** an install icon (⊕) appears in the address bar; clicking it installs it as a windowed app with no browser chrome.

That's it — once installed this way, it behaves like a normal app icon and opens full-screen.

### If you actually want it in the Play Store / App Store
That's a different, bigger step than "no bezel" — it means wrapping the PWA as a native package:
- **Android:** [PWABuilder](https://www.pwabuilder.com) or Google's Bubblewrap can generate a Play Store–ready Trusted Web Activity from your hosted URL + manifest.
- **iOS:** PWABuilder can also generate an Xcode project, but you'd still need a Mac + Apple Developer account to submit it.

Not needed for the bezel-free home-screen install above — only mention it if you specifically want store distribution later.

## What changed in `index.html`
- **Tasks tab:** "+ New task" now expands into an inline text field (✓ / ✕ buttons) instead of a popup `prompt()`. New tasks are appended to the **bottom** of the list.
- **Task detail:** "+ Add sub-task" works the same way — inline field, appended to the bottom of that task's sub-tasks, stays open so you can add several in a row (Enter also submits; Esc/✕ closes it).
