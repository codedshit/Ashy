# Ash — personal profile site

A single-file, glassmorphic personal "link in bio" page. Live Discord presence via Lanyard, Last.fm now-playing/recent tracks/weekly rotation, a Cloudinary-hosted shuffle music player with a live audio visualizer, a real global visitor counter, 3D tilt cards, and optional device-specific background video — all in one `index.html`, no build step, no backend.

## Features

- **Discord presence** via [Lanyard](https://github.com/Phineas/lanyard) WebSocket (with REST fallback) — live status dot, custom status, current activity with a live-updating elapsed timer, and a Spotify-style now-playing widget with equalizer + progress bar. Falls back to a terminal-style "idle" card when nothing's active.
- **Last.fm integration** — now-playing card, recently-played list, and this-week's-top-artists chips
- **Music player** — shuffled playlist streamed from Cloudinary, with a canvas audio visualizer reacting to playback
- **Visitor counter** — real global hit count via [Abacus](https://abacus.jasoncameron.dev) (free, no signup, no auth, no local/fake fallback)
- **Ultra-transparent glass cards** — neutral (no color tint), blurred/saturated panels that let the background show through
- **3D mouse-tilt** on every card (`requestAnimationFrame`-eased rotation + a cursor-tracking light sheen), plus an instant cursor glow
- **UI sound blips** (Web Audio API) on hover/click/tab-switch
- **Background video**, with separate desktop and mobile clips so `object-fit:cover` never crops off the important part of the frame on either device
- **Konami code easter egg**
- Local/their-time chips, typewriter tagline, avatar decoration ring, custom favicons with an optional live-status badge overlay

## Requirements

- Any static file host (GitHub Pages, Netlify, Vercel, Cloudflare Pages, or a plain web server) — it's a single HTML file
- A [Last.fm API key](https://www.last.fm/api/account/create) (free)
- A [Discord user ID](https://support.discord.com/hc/en-us/articles/206346498), sharing a server with the [Lanyard](https://github.com/Phineas/lanyard) bot (required for Lanyard to read your presence)
- A [Cloudinary](https://cloudinary.com) account (free tier) if you want to host your own tracks/cover art
- Font files (`Ngaco`, `JandaManateeSolid`, `JandaManateeBubble`) in a `fonts/` folder, icons in a `favicon/` folder, and a profile picture — see [Folder structure](#folder-structure)

## Quick start

1. Download `index.html` and open the `CONFIG` block near the bottom of the `<script>` tag.
2. Replace the constants below with your own values.
3. Add your `pfp.png`, `fonts/`, `favicon/`, and any other asset files/folders alongside `index.html`.
4. Upload everything to your static host.

## Configuration

All settings live in one place — the `CONFIG` block near the end of the file.

| Constant | What it does |
|---|---|
| `LFM_KEY` | Your Last.fm API key |
| `LFM_USER` | Your Last.fm username |
| `LFM_DEFAULT_ART` | Fallback album art URL when a track has none |
| `COORDS` | Coordinates shown as flavor text (not geolocated) |
| `MY_TIMEZONE` | IANA timezone string (e.g. `Europe/Helsinki`) used for the "their time" chip |
| `DISCORD_ID` | Your Discord user ID, used with Lanyard for live presence |
| `AVATAR_DECORATION_SRC` | Path/URL to the animated ring overlaid on your avatar |
| `CLOUDINARY_CLOUD_NAME` | Your Cloudinary cloud name |
| `TRACKS` | Array of `{ title, artist, id, cover }` — `id`/`cover` are Cloudinary public IDs for the audio and art |
| `BG_VIDEO_SRC` | Background video for desktop/wide screens (`≥` `BG_VIDEO_BREAKPOINT`) |
| `BG_VIDEO_SRC_MOBILE` | Background video for phones/narrow screens; leave empty to just reuse `BG_VIDEO_SRC` everywhere |
| `BG_VIDEO_OPACITY` | Opacity of the background video, `0`–`1` |
| `BG_VIDEO_BREAKPOINT` | Screen width (px) where it switches from the mobile clip to the desktop clip — default `820`, matches the site's layout breakpoint |
| `FAVICON_SRC` / `APPLE_TOUCH_ICON_SRC` | Optional overrides for favicon images |
| `FAVICON_STATUS_BADGE` | If `true`, overlays your live Discord status dot on the favicon |
| `VISITOR_NAMESPACE` / `VISITOR_KEY` | Namespace/key used for the Abacus hit counter — change these to get your own independent counter |
| `TAGLINES` | Array of strings the typewriter effect cycles through |

### Setting your profile picture

Add an image file named **`pfp.png`** next to `index.html`. It's used in two places: the main avatar on the profile card, and the small avatar in the Status tab (as a fallback if your live Discord avatar fails to load). Keep it roughly square — it's rendered as a circle with `object-fit:cover`, so non-square images get center-cropped.

### Adding a background video

```js
const BG_VIDEO_SRC = 'videos/bg-desktop.mp4';
const BG_VIDEO_SRC_MOBILE = 'videos/bg-mobile.mp4';
const BG_VIDEO_OPACITY = 0.5;
```

The page picks between them automatically based on screen width (desktop ≥820px, mobile below), and re-checks on resize/rotation so it stays correct if someone resizes their browser or flips their phone. This exists because a single video cropped with `object-fit:cover` looks very different on a wide desktop screen vs. a tall phone screen — a landscape (16:9) clip gets its sides heavily cropped on a phone.

- Recommended: **desktop** ~1920×1080 (16:9), **mobile** ~1080×1920 (9:16), each shot/cropped with the subject centered for its own aspect ratio.
- Keep it `.mp4` (H.264) for broad browser support, and compress it — it autoplays muted/looped on load, so a large file slows down first paint.
- Leave `BG_VIDEO_SRC_MOBILE` empty to fall back to a single video for every screen size.

### Adding/changing tracks

```js
const TRACKS = [
    { title: 'Song Name', artist: 'Artist', id: 'cloudinary_audio_public_id', cover: 'cloudinary_image_public_id' }
];
```

The player resolves audio from `.../video/upload/{id}.mp3` and cover art from `.../image/upload/{cover}.jpg` on your Cloudinary cloud — upload your MP3s and cover images there and grab their public IDs.

### Visitor counter

Uses [Abacus](https://abacus.jasoncameron.dev/docs), a free, no-signup, no-auth counting API. Each page load hits `GET /hit/{VISITOR_NAMESPACE}/{VISITOR_KEY}` and displays the real value it returns — no local storage, no fake numbers. If the request fails, the badge just shows "offline" instead of guessing.

## Folder structure

```
/
├── index.html
├── pfp.png              (your profile picture)
├── profiledeco.gif       (avatar decoration ring)
├── fonts/
│   ├── Ngaco.otf / Ngaco.ttf
│   ├── JandaManateeSolid.ttf
│   └── JandaManateeBubble.ttf
├── favicon/
│   ├── favicon-32.png
│   ├── favicon.svg
│   ├── apple-touch-icon.png
│   └── site.webmanifest
└── videos/               (optional, if self-hosting bg video)
    ├── bg-desktop.mp4
    └── bg-mobile.mp4
```

## Notes

- No build tools, frameworks, or dependencies to install — plain HTML/CSS/JS plus the Font Awesome CDN.
- Discord presence and Last.fm data are fetched client-side, so your Discord ID and Last.fm username/key are visible in the page source — this is normal for Lanyard/Last.fm setups but worth knowing.
- Card backgrounds are intentionally very transparent, neutral glass (no color tint) so a background video or the ambient page gradient shows through cleanly. Tune `--panel` / `--panel-strong` in the CSS `:root` block if you want them more or less see-through.
- The 3D tilt effect is mouse/pointer-driven; on touch devices there's no real "hover," so it won't do much there — that's expected, not a bug.
