# App shell

## Files
`index.html` (head script, `#homeMode`, top bar, routing in `serino-ui`, theme code in `ui`, start-up at the end of `eroson-book`), `sw.js`, `manifest.webmanifest`, `icon.svg`, `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`.

## Purpose
The app is named Wicofia. This system covers the home page, moving between screens, the theme and installing or running offline.

## How it works
- **Home:** `#homeMode` has one card per character, plain links to `#phyou`, `#serino` and `#eroson`. `showHome` fills each card with real progress.
- **Routing:** the URL hash is the source of truth. `route` reads it, `hashchange` calls `setWho`, so the browser back button and reloads work. `setWho` stops any training that is not on the new screen. `setWho(route())` runs at the end of `eroson-book`, the last script, since it needs every script loaded.
- **Theme:** `data-theme` on `<html>` drives the colors; dark tokens live in `:root[data-theme="dark"]`. A head script sets it before first paint from `localStorage` (`wicofia-theme`), otherwise from the system. `setTheme` updates the attribute, `theme-color`, canvas tokens and heatmap, then fires `wicofia-theme`. The system setting is followed only until the user picks.
- **Service worker:** network first, cache as fallback, so updates arrive without a version bump. Registered only on http(s), so opening the file directly still works, just without offline use.
- **Icons:** `icon.svg` is the source and shows all three characters. The PNGs were rendered from it in Edge; the maskable one has no rounded corners and its art is scaled to 80%.

## Key functions
`setWho`, `showHome`, `route`, `setTheme`.

## Interactions
`setWho` calls `setRunning` from [ui.md](ui.md), `sSetRun`, `serinoReset` from [serino.md](serino.md), and `eSetRun`, `erosonReset` from [eroson.md](eroson.md).

## Change-safety notes
- Adding a file the app needs offline means adding it to `SHELL` in `sw.js` and bumping `CACHE`.
- Changing `icon.svg` means re-rendering the PNGs.
- The book keeps fixed paper colors in both themes.
- In `ui`, `history` is the chart's `let` and shadows `window.history`; use `window.history` if it is ever needed.
