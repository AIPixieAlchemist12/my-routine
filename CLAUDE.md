# My Routine — Claude quick reference

## Architecture
- **Single-file PWA.** All HTML, CSS, and JS live in `index.html`. Don't split it.
- `pwa/` holds static PWA assets only (manifest, icons, splash screens). Never put logic there.
- No build step, no bundler, no service worker (online-only by design).

## Version convention
Every release adds a comment on line 2 of `index.html`:
```
<!-- vX.Y | YYYY-MM-DD | short change summary -->
```
Bump it for every release. Current: v1.4.

## Edit rules
- Read the **entire** `index.html` before touching anything.
- Surgical edits only — no rewrites, no refactors, no drive-by cleanups.
- One change at a time. Verify it works before the next.
- Never add a sibling `<div>` to `stack-area`, `week-view`, `pantry-view`, or `chat-view` — the layout assumes exactly these four.

## Secrets
- API key stays in the Vercel proxy at `https://project-fvy9w.vercel.app/api/proxy`. Never embed a key in `index.html`.

## Deploy
- Push to `main` → GitHub Pages auto-rebuilds in ~60s.
- After every deploy, iPhone needs: delete the app → clear Safari cache (Settings → Safari → Clear History) → re-add to home screen. PWA caches aggressively.

## Current branch
- Active dev branch: `claude/v1.4-implementation-cSn4V`.

## Known quirks
- Yoga modal uses inline SVGs in the `SVGS` object near line 699. No GIFs are planned; leave as-is.
- Dr. Althea stays in the pantry as `unused` — don't remove it.
- Device bar (v1.3) works on desktop Chrome. iPhone test pending after v1.4 deploy.
