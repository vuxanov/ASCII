# Issue log

## 2025-03-23 — GIF decode failed / “GIF library failed to load”

**Issue:** Opening the app locally, animated GIFs failed with `GIF decode failed: Error: GIF library failed to load.` Console also showed Tailwind’s “cdn.tailwindcss.com should not be used in production” message.

**Cause:** The page loaded `https://cdn.jsdelivr.net/npm/gifuct-js@2.1.2/dist/gifuct-js.min.js`, but the published `gifuct-js` package has no `dist/` folder (only `lib/`). The script never loaded, so `parseGIF` / `decompressFrames` were missing.

**Resolution:** Removed the broken script tag and load `gifuct-js` via `import('https://esm.sh/gifuct-js@2.1.2')` when decoding a GIF (cached promise so it only fetches once). The Tailwind CDN line is unrelated to the GIF failure; it is a dev warning and does not stop styles from applying.

**Note:** If `import()` from a CDN fails (e.g. strict offline or unusual `file://` behavior), serve the folder over HTTP (`python3 -m http.server`) so the page can fetch the module reliably.
