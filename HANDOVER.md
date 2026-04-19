# Handover — My Routine

Read this alongside `CLAUDE.md`. `CLAUDE.md` holds durable rules that never change between sessions; this file holds the in-flight context a cold Claude would otherwise miss.

## 1. What we're building

**My Routine** — a single-file PWA (`index.html`) for Elena's skincare routine. Four views: stack-area, week-view, pantry-view, chat-view. Claudette chatbot via a Vercel proxy. Current release **v1.4** adds: Pantry cards show a days chip + expandable "⚠️ conflicts" chip per product, and the app is installable as an iOS PWA (splash, icons, safe-area polish).

## 2. Decisions locked in

- **Single-file architecture.** All HTML/CSS/JS lives in `index.html`. No bundler, no build step, no service worker (online-only).
- **`pwa/` is assets only** — manifest, icons, splash screens. No logic.
- **Pantry cards use Option A layout** — inline chips, inline `onclick` toggle. No new CSS classes or JS helpers; everything is inline in the `html+=` template inside `renderPantry()`.
- **Dr. Reju-All** is the active retinol-night moisturizer (Tue + Fri).
- **Dr. Althea** stays in the pantry as `unused` — NOT in `DEFAULT_PRODUCTS`, preserved via localStorage merge only.
- **Yoga modal** uses inline SVGs in the `SVGS` object near line 699. No GIFs planned — leave as-is.
- **Version comment** on line 2 of `index.html`, format `<!-- vX.Y | YYYY-MM-DD | summary -->`.
- **API key** stays in the Vercel proxy at `https://project-fvy9w.vercel.app/api/proxy`. Never embed.

## 3. Current state (verified this session)

- **Active dev branch:** `claude/skincare-routine-pwa-EiUzr` (local + remote).
- **v1.4 is merged to `main`** via PR #1, merge commit `26639ab`. GitHub Pages auto-rebuilt.
- **Live URL:** `https://aipixiealchemist12.github.io/my-routine/` (confirmed serving v1.4 — pantry shows the conflicts chip).
- **Claudette proxy:** `https://project-fvy9w.vercel.app/api/proxy` (Vercel hosts the proxy, not the app).
- The old branch name `claude/v1.4-implementation-cSn4V` no longer exists — it was merged and deleted.

## 4. Critical context a cold Claude would miss

- **Git push setup:** this sandbox fetches through a read-only local proxy; push goes direct to github.com via a PAT stored in `~/.git-credentials`. If push breaks in a future session, the PAT was probably rotated — ask Elena for a fresh one and run `printf 'https://AIPixieAlchemist12:TOKEN@github.com\n' > ~/.git-credentials && chmod 600 ~/.git-credentials`.
- **Sandbox network:** outbound `github.io` is not on the sandbox allowlist, so Claude can't `curl`/`WebFetch` the live site from here (returns `host_not_allowed`). This affects **only** in-sandbox verification; Elena's phone, `git push`/`pull`, and the Vercel proxy are all unaffected.
- **Secrets hygiene:** a prior transcript exposed two GitHub PATs (`ghp_…`) and an Anthropic key (`sk-ant-api03-…`). Elena should rotate them if she hasn't already. The Anthropic key likely powers the Vercel proxy — rotating it means the Vercel env var also needs updating or Claudette breaks. Full strings are **not reproduced here** by design.
- **Design adaptations vs. the original brief:**
  - Safe-area padding was moved from `.app` (class doesn't exist in `index.html`) to `body`.
  - Haptic JS selector list trimmed to selectors that exist: `.skip-btn, .tab-btn, .day-pill, .checkin-opt, .status-option-btn`. The brief's `.done-btn .ses-btn .skip-opt .chat-input-wrap` selectors do not exist in the codebase.
- **Design source of truth** for future UI work: the zip at `https://api.anthropic.com/v1/design/h/uD8YuAwMoIAATyxWrP1KQQ`, previously extracted to `/tmp/design-extract/my-skin-routine-design-system/`. ⚠️ `/tmp` is wiped between sandbox sessions — the extract is gone. Re-fetch the zip before doing UI work that references it.
- **Design status:** only the **iOS-native-polish subset** of the design system shipped in v1.4 (safe-area padding, haptic feedback selectors, icons, splash screens). The broader visual redesign from the zip (component styles, colors, layout) was **not** implemented — the app still uses the pre-design-system styles in `index.html`. The `project/preview/components-pantry.html` reference was consulted and deliberately *didn't* dictate how days/conflicts display, which is why Option A (inline chips) was approved for pantry — that decision stands even after a broader redesign.
- **Line anchors (drift as edits continue):** `DEFAULT_PRODUCTS` ~line 601, `loadProducts()` ~line 746, `renderPantry()` card template ~line 1491, `(function init(){` ~line 1721, `<style>` opens ~line 25, `SVGS` object ~line 699.

## 5. What comes next

All remaining tasks are off-codebase — they need Elena (or Vercel dashboard access).

### iPhone PWA install verification

1. Delete the old home-screen app.
2. Settings → Safari → Clear History and Website Data.
3. Open `https://aipixiealchemist12.github.io/my-routine/` in Safari → Share → Add to Home Screen.
4. Walk the v1.4 checklist:
   - Icon shows "MSC" (not a generic screenshot).
   - No white flash on launch; splash screen shows.
   - Status bar translucent over the espresso header.
   - Pull-to-refresh disabled.
   - Keyboard slides over chat input (doesn't push the whole layout up).
   - No blue tap flash; scale-press still works.
   - Edge swipe doesn't exit to Safari.
   - Bottom nav + chat send button not obscured by the home indicator.

### Device bar (v1.3) re-verification

- Was only tested on desktop Chrome before v1.4. Verify on iPhone after the install above.

### Visual redesign (from the design zip)

- Not yet implemented. The full design system at `https://api.anthropic.com/v1/design/h/uD8YuAwMoIAATyxWrP1KQQ` still needs to be applied to `index.html`. Scope is TBD — before diving in, confirm with Elena which views to redesign first (stack-area / week-view / pantry-view / chat-view) and whether she has fresh specs that supersede the zip.
- Reminder: surgical edits, one change at a time, respect the single-file rule.

### Claudette sanity check

- If the Anthropic key was rotated, update the Vercel project's env var (`project-fvy9w`) and redeploy. Confirm Claudette still replies in the chat view.

## 6. Open questions to pick up first

1. **Did the iPhone install checklist pass?** If not, which items failed — that tells us which splash media query or CSS fallback needs a fix.
2. **Is Claudette responding** after any Anthropic key rotation? If no, the Vercel proxy env var update is pending.
3. **Any new design specs from Elena** not in the zip? She was iterating before the last handover — check before further UI work.
4. **Visual redesign scope:** which views first, and does the original zip still represent Elena's intent? Nothing from the broader design system has been applied yet.

## 7. How to use this doc in a new session

Cold Claude auto-loads `CLAUDE.md` but not `HANDOVER.md`. Either:

- Tell the new session: "Read `HANDOVER.md` first, then proceed." — or —
- Paste the relevant section directly into the first message.

Keep `HANDOVER.md` trimmed to *current* in-flight context. Anything that becomes a durable rule should migrate into `CLAUDE.md` and be deleted from here.
