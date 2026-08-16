# Ash — bio link site

A single-file (`index.html`) dark-themed bio/link page with a Discord presence
badge, Last.fm now-playing, a shuffled audio player, and a few extra bits
(favicon system, global visitor count, scroll progress bar, cursor glow,
share button, Konami-code easter egg).

Everything is configured near the top of the `<script>` block, in the
`CONFIG` section — no other part of the file needs to be touched for basic
setup.

## Fonts

Custom fonts are loaded via `@font-face` at the top of the `<style>` block:

- `Ngaco` → headings / display text
- `JandaManateeSolid` → labels, tags, UI accents
- `JandaManateeBubble` → body text

Drop the matching `.ttf`/`.otf` files into a `/fonts` folder next to
`index.html`:

```
/fonts/Ngaco.otf (or .ttf)
/fonts/JandaManateeSolid.ttf
/fonts/JandaManateeBubble.ttf
```

If a font file is missing, the browser just falls back to `cursive` — the
page won't break.

### Font ideas if you want to swap any of these out

- **Display / headline** — Bleeding Cowboys, Urban Jungle, 28 Days Later
  (grunge display faces, good replacements for `Ngaco` if you want something
  rougher)
- **Handwritten accent** — Homemade Apple, Shorelines Script (softer
  alternatives to Janda Manatee)
- **Condensed / stat labels** — Big Noodle Titling (sharp, reads well small —
  good for the loading-bar labels and tag chips)

All of these are free on [dafont.com](https://www.dafont.com) — check each
one's license before commercial use, personal-use bio pages are fine.

## Background video

```js
const BG_VIDEO_SRC = '';      // path or URL to an .mp4 — leave blank for the plain dark background
const BG_VIDEO_OPACITY = 0.5; // 0–1
```

Cards use a semi-transparent glass panel (`--panel` / `--panel-strong` in
`:root`) so the video shows through on both mobile and desktop. When a video
is set, panels automatically go a touch more transparent (`.has-bg-video`
rule).

## Favicons

```js
const FAVICON_SRC          = ''; // e.g. 'favicon/favicon-32.png' — blank keeps the generated glyph icon
const APPLE_TOUCH_ICON_SRC = ''; // e.g. 'favicon/apple-touch-icon.png'
const FAVICON_STATUS_BADGE = true; // paints your live Discord status color as a dot on the tab icon
```

Recommended: create a `/favicon` folder with:

```
/favicon/favicon-32.png
/favicon/favicon.svg
/favicon/apple-touch-icon.png
/favicon/site.webmanifest
```

The `<link>` tags for all of these already exist in `<head>` — just add the
files. If `FAVICON_SRC` is left blank, the page draws its own "A" icon on a
canvas so you always have something in the tab, and (if
`FAVICON_STATUS_BADGE` is on) overlays a small colored dot that updates live
with your Discord status.

## Discord presence (Lanyard)

```js
const DISCORD_ID = '1301191057656119386';
```

Pulls live status (online / idle / dnd / offline) and custom status text from
the [Lanyard API](https://github.com/Phineas/lanyard). Requires you to have
joined the Lanyard Discord server so it can track your presence — see their
README for setup.

## Avatar frame

```js
const AVATAR_FRAME_SRC = ''; // e.g. an animated PNG/GIF/WebP overlay
```

Optional decorative ring/frame that sits on top of the profile picture
(Discord-style). Leave blank to hide it.

## Playlist (Cloudinary)

```js
const CLOUDINARY_CLOUD_NAME = 'dgus0vior';
const TRACKS = [
    { title: 'Little Angel', artist: 'Onokami', id: 'mt7cjbv0eyk3tyq8epvw', cover: 'kiiennufvy3vgjjl1i2a' }
];
```

Each track needs its audio and cover art already uploaded to your Cloudinary
account:

- `id` → the public ID of the **audio** file (`video/upload/{id}.mp3`)
- `cover` → the public ID of the **cover image** (`image/upload/{cover}.jpg`)

Add as many tracks as you want — with more than one, they shuffle and
auto-advance.

## Last.fm

```js
const LFM_KEY  = '...';
const LFM_USER = 'Ashy-geek';
```

Shows now-playing, recently played, and this week's top artists via the
Last.fm API. Get a free API key at
<https://www.last.fm/api/account/create>.

## Global visitor counter

Uses [CountAPI](https://countapi.xyz) (free, no key required) to keep a
single shared counter across every visitor to the page:

```js
const VISITOR_NAMESPACE = 'ash-bio-site';
const VISITOR_KEY = 'visits';
```

Change `VISITOR_NAMESPACE` to something unique to you if you want your own
counter (CountAPI namespaces are shared globally, so a generic name could
collide with someone else's page). If the API is unreachable, the visitor
chip just hides itself instead of showing an error.

## Other things baked in

- **Scroll progress bar** — thin gradient bar along the top of the page
- **Cursor glow** — soft light that follows the mouse on desktop only (off
  on touch devices and when the OS "reduce motion" setting is on)
- **Share button** — uses the native share sheet on mobile, falls back to
  copying the URL to the clipboard
- **Konami code easter egg** — ↑ ↑ ↓ ↓ ← → ← → b a triggers a little
  sparkle burst
- **Birthday bar** — set your birthday in `initBirthday()`
  (`BDAY_MONTH`, `BDAY_DAY`). The bar starts full right after your birthday
  and gradually drains down to empty as the next one approaches, then
  resets back to full and starts again — it always reflects the real
  fraction of the year remaining, not a fixed guess.
- Everything respects `prefers-reduced-motion` — animations disable
  themselves automatically for visitors who have that OS setting on.

## File structure once you add your assets

```
/
├── index.html
├── /fonts
│   ├── Ngaco.otf
│   ├── JandaManateeSolid.ttf
│   └── JandaManateeBubble.ttf
├── /favicon
│   ├── favicon-32.png
│   ├── favicon.svg
│   ├── apple-touch-icon.png
│   └── site.webmanifest
├── pfp.jpg
```

No build step — it's a static file, just push it to your repo/host as-is.
