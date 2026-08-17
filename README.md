# Ash — bio link site

A single-file (`index.html`) dark-themed glassmorphic bio/link page with a
Discord presence badge, an animated avatar decoration/frame system, a
full-card "profile effect" overlay, Last.fm now-playing, a shuffled audio
player, and a few extra bits (favicon system, global visitor count, scroll
progress bar, cursor glow, share button, Konami-code easter egg).

Everything is configured near the top of the `<script>` block, in the
`CONFIG` section — no other part of the file needs to be touched for basic
setup.

> **Changed from the original version:** the profile card no longer spins
> like a coin — it sits still and fades in once. The avatar decoration is
> now precisely centered on the pfp instead of the outer avatar box. Cards
> are noticeably more transparent so a background video reads through them
> clearly. There's a new full-card "profile effect" layer, and the avatar
> decoration/frame system was split into two independent, clearly-named
> slots. Full details in the sections below.

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

Cards are genuinely transparent glass panels (`--panel` / `--panel-strong`
in `:root`, plus `backdrop-filter: blur(...) saturate(...)`), so the video
shows through **on every card**, not just the space around them — that's
true on both mobile and desktop. When a video is set, panels automatically
drop to an even more transparent mix (the `.has-bg-video` rule tightens
`--panel`/`--panel-strong` further via `color-mix`).

If you want the glass even clearer or more opaque, tune these two tokens in
`:root`:

```css
--panel:        rgba(20,19,22,.16);  /* base panel fill */
--panel-strong: rgba(20,19,22,.34);  /* used for chips, buttons, toasts */
--panel-blur:   18px;                /* backdrop blur amount */
```

A subtle diagonal light-gradient (`.card::before`) is layered on every card
so the glass still reads as a distinct surface even with no video behind
it — you don't need to touch this unless you want to remove the sheen.

## Avatar decoration, avatar frame & profile effect

There are three independent, layered visual slots, all configured together
near the top of the `CONFIG` section. None of them require touching the
markup — everything is driven from `<script>`.

### 1. Avatar decoration

The animated ring/badge worn directly on the profile picture — the classic
"Discord avatar decoration" look (this is what `profiledeco.gif` is).

```js
const AVATAR_DECORATION_SRC = 'profiledeco.gif'; // png / gif / webp — blank hides it
const AVATAR_DECORATION_SCALE = 1.46;            // see "aligning decoration art" below
```

### 2. Avatar frame

A **second, independent** overlay that sits on top of the decoration. Use
this if you want a decoration *and* a separate frame graphic at the same
time (for example, a seasonal frame layered over a permanent decoration).
Leave the source blank if you only want the decoration — it's disabled by
default.

```js
const AVATAR_FRAME_SRC = '';   // png / gif / webp — blank hides it
const AVATAR_FRAME_SCALE = 1.2;
```

### Aligning decoration/frame art to the pfp

Both overlays are centered exactly on the profile picture's own center
(`translate(-50%,-50%)`), so they can never drift off to one side — that
part is automatic. The one thing you may need to adjust per image is the
**scale**: most decoration/frame art (including Discord-style rings) is
drawn on a square canvas with a transparent "hole" in the middle that's
noticeably smaller than the canvas itself, so the artwork has to be scaled
*up* until that hole lines up with the visible pfp circle.

To tune it for your own image:

1. Set the source (`AVATAR_DECORATION_SRC` or `AVATAR_FRAME_SRC`).
2. Open the page and look at how the ring sits on the pfp.
3. Nudge the matching `_SCALE` value up or down in small steps (`1.30` →
   `1.55` is a typical range) until the hole matches the pfp edge.

`profiledeco.gif` is already tuned at `1.46`.

### 3. Profile effect

A full-card animated overlay, similar to Discord's newer "profile effects"
(auras, sparkles, weather that play across the whole profile popup, not
just the avatar). It's clipped automatically to the card's rounded corners
and rendered *behind* all the text/buttons so it never gets in the way.

```js
const PROFILE_EFFECT = {
    mode: 'particles',            // 'particles' | 'image' | 'none'
    src: '',                      // used when mode is 'image'
    particleColor: '181,137,90',  // r,g,b for particle mode
    particleCount: 22
};
```

- **`particles`** (default) — a lightweight built-in field of drifting,
  twinkling glow-dots rendered on a `<canvas>`. No asset needed; just tweak
  `particleColor` (an `"r,g,b"` string) and `particleCount`. Automatically
  disabled if the visitor's OS has "reduce motion" turned on.
- **`image`** — point `src` at your own png / apng / gif / webp loop sized
  to cover the card (it's stretched with `object-fit: cover` and blended
  with `mix-blend-mode: screen`, so dark backgrounds in the asset disappear
  and only the bright/glowing parts show).
- **`none`** — disables the layer entirely.

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

- **Profile card entrance** — the profile card and content column fade/rise
  into place once on load (`profileCardIn`). It no longer spins.
- **Ambient avatar halo** — a soft, slow-pulsing glow sits behind the
  avatar stack (`.avatar-halo`) — purely CSS, no config needed.
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
- Everything respects `prefers-reduced-motion` — animations (including the
  particle-mode profile effect) disable themselves automatically for
  visitors who have that OS setting on.

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
├── profiledeco.gif        (avatar decoration — see AVATAR_DECORATION_SRC)
```

No build step — it's a static file, just push it to your repo/host as-is.
