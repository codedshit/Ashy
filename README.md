# Ash — Bio Link Page

A single-file, no-build personal bio/link page (`index.html`) in the style of Discord bio-link
sites like guns.lol — glass cards, a live Discord presence dashboard, a Last.fm "now playing"
widget, a playlist player, synced Spotify lyrics, and a bunch of small motion/interaction polish.

Everything — HTML, CSS, and JS — lives in `index.html`. There is no build step, no bundler,
no `node_modules`. Open the file in a browser (or serve the folder) and it works.

## Quick start

1. Drop `index.html` in the root of your site, alongside your assets (see **Assets** below).
2. Edit the `CONFIG` block near the top of the `<script>` tag to point at your own
   Discord ID, Last.fm username, socials, etc. (see **Configuration** below).
3. Serve the folder with any static file server (or host it as-is on GitHub Pages,
   Netlify, Vercel, etc.). Some browsers block `fetch()`/autoplay on `file://` pages,
   so for local testing it's best to run a tiny local server, e.g.:
   ```bash
   python3 -m http.server 8080
   ```
   then open `http://localhost:8080`.

## Assets you need to provide

The page expects these files to exist relative to `index.html` (all are optional in the
sense that the page won't crash without them — images/video will just fail to load —
but it'll look broken):

| Path | Used for |
|---|---|
| `pfp.png` | Profile picture (main avatar + Discord presence avatar fallback) |
| `profiledeco.gif` | Animated ring/decoration worn on the avatar |
| `favicon/favicon-32.png`, `favicon/favicon.svg`, `favicon/apple-touch-icon.png` | Favicons (a live status-colored badge is drawn on top at runtime) |
| `videos/bg-desktop.mp4` | Background video, ≥769px wide viewports |
| `videos/bg-mobile.mp4` | Background video, ≤768px wide viewports |

Fonts and icons are loaded from Font Awesome's CDN (`cdnjs.cloudflare.com`) — no local
font files needed unless you change that.

## Configuration

All of the editable settings live in one place: the `CONFIG` block at the top of the
`<script>` tag, near the bottom of `index.html`. Nothing else in the file needs to be
touched for normal customization.

### Identity / socials
- `COORDS` — the coordinates shown in the Location chip (click-to-copy).
- `MY_TIMEZONE` — IANA timezone string used for the "their time" clock.
- Social links live directly in the HTML (`.social-row`), not in `CONFIG` — edit the
  `href` attributes on the Discord / GitHub / AniList buttons directly.

### Discord presence (Lanyard)
- `DISCORD_ID` — your Discord user ID. Presence is pulled live via
  [Lanyard](https://github.com/Phineas/lanyard) over WebSocket, with an HTTP fallback if
  the socket fails to connect. Your Discord account needs to have joined the
  [Lanyard Discord server](https://discord.gg/lanyard) for this to return data.
- `AVATAR_DECORATION_SRC` — path to the avatar ring/decoration asset.

### Last.fm ("now playing")
- `LFM_KEY` — a Last.fm API key ([get one here](https://www.last.fm/api/account/create)).
- `LFM_USER` — your Last.fm username.
- `LFM_DEFAULT_ART` — fallback cover art when a track has none.

### Playlist (Cloudinary-hosted audio)
- `CLOUDINARY_CLOUD_NAME` — your Cloudinary cloud name.
- `TRACKS` — array of `{ title, artist, id, cover }`, where `id`/`cover` are Cloudinary
  public IDs for the audio file and cover image.

### Background video
- `BG_VIDEO_SRC` / `BG_VIDEO_SRC_MOBILE` — desktop and mobile clips (leave the mobile one
  empty to reuse the desktop clip everywhere).
- `BG_VIDEO_OPACITY` — 0–1.
- `BG_VIDEO_BREAKPOINT` — px width where it switches between the two clips.

### Favicon
- `FAVICON_SRC` — static favicon path, only used if `FAVICON_STATUS_BADGE` is `false`.
- `FAVICON_STATUS_BADGE` — when `true` (default), a small "A" glyph favicon is drawn on
  a `<canvas>` at runtime with a colored dot reflecting your live Discord status.

### Visitor counter
- `VISITOR_NAMESPACE` / `VISITOR_KEY` — namespace/key used with the
  [Abacus](https://abacus.jasoncameron.dev) visit-counter API.

### Custom cursor & trail
- `CUSTOM_CURSOR_ENABLED` — replaces the system pointer with a ring + dot cursor that
  eases toward the pointer and grows over clickable elements.
- `CURSOR_TRAIL_ENABLED` — adds a fading sparkle trail that follows the pointer.
- `CURSOR_TRAIL_COLOR` — hex color for the trail, or `null` to use the site's
  `--accent-strong` CSS variable automatically.
- Both are automatically disabled on touch devices and when the visitor has
  "reduce motion" turned on at the OS level.

### Premium polish
- `PREMIUM_BADGE_ENABLED` / `PREMIUM_BADGE_TEXT` — shows a shimmering badge chip next to
  the display name.
- `CARD_SHEEN_SWEEP_ENABLED` — a slow light streak that periodically sweeps across each
  card, staggered so they don't all fire in sync.

### Synced lyrics
- `LYRICS_ENABLED` — when on, and Discord reports you're listening to Spotify, the page
  fetches time-synced lyrics from [LRCLIB](https://lrclib.net) (a free, open lyrics
  database — no API key needed) and highlights the current line as the track plays,
  using the same elapsed-time tracking as the Spotify progress bar. Falls back to plain
  (unsynced) lyrics if no synced version exists, and hides itself if nothing is found.

## Features

- **Glass card UI** with a 3D pointer-tilt effect and a cursor-following sheen on every
  card. The main profile card tilts noticeably more than the rest (`data-tilt-max="11"`
  vs. the default `6°`) so it reads as the focal point.
- **Scroll reveal** — cards and content blocks fade + rise into place as they enter the
  viewport (or the moment a tab is switched to), rather than a static page.
- **Live Discord presence** via Lanyard — status dot, custom status, current game/app
  activity, and a live Spotify now-playing card with a synced progress bar.
- **Synced Spotify lyrics** (see above).
- **Last.fm "now playing"/recent tracks/top artists** panel, polling on an interval.
- **Built-in audio player** for a Cloudinary-hosted playlist, with a canvas frequency
  visualizer driven by the Web Audio API.
- **Custom cursor + sparkle trail**, entrance/typewriter animations, a Konami-code easter
  egg, synthetic UI sound blips, a birthday countdown, session timer, and a
  copy-to-clipboard coordinates chip.
- Respects `prefers-reduced-motion` and coarse/touch pointers throughout — every motion
  effect (tilt, cursor, trail, sweep, entrance animation, reveal) checks these before
  running.

## Structure

Everything is inline in `index.html`, organized with numbered section comments:

- `<style>` — one stylesheet, sectioned (`/* 1. ... */`, `/* 2. ... */`, etc.) roughly in
  the order things appear on the page, ending with cursor/lyrics/premium-polish styles.
- `<body>` — entry overlay → background video → main layout (profile card + tabbed
  content: **Bio**, **Status**, **Audio**) → footer.
- `<script>` — `CONFIG` block first, then one `(function initX(){ ... })()` per feature,
  roughly in the order they appear in the page.

## Browser support notes

- Uses `IntersectionObserver`, the Web Audio API, `backdrop-filter`, and CSS custom
  properties — all standard in current evergreen browsers (Chrome, Firefox, Safari,
  Edge). No polyfills are included.
- Autoplay (background video, audio context) may be blocked by the browser until the
  visitor taps the entry overlay — this is expected and handled gracefully.
- `file://` pages can block `fetch()` calls (Discord/Last.fm/lyrics data) in some
  browsers; serve over `http://localhost` for local testing.

## Error handling

Every live-data feature (Discord presence, Last.fm, lyrics, visitor counter) fails
gracefully — if a network request fails, the corresponding widget shows a fallback state
instead of breaking the rest of the page. Top-level initialization calls are wrapped in a
small `safeRun()` helper for the same reason: one broken selector or missing element
can't take down every feature that loads after it.

## Credits / third-party services

- [Lanyard](https://github.com/Phineas/lanyard) — Discord presence
- [Last.fm API](https://www.last.fm/api) — scrobble history
- [LRCLIB](https://lrclib.net) — synced lyrics
- [Cloudinary](https://cloudinary.com) — playlist audio/art hosting
- [Abacus](https://abacus.jasoncameron.dev) — visitor counter
- [Font Awesome](https://fontawesome.com) — icons
