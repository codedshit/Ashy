# Ash's site — how everything works

This is a single-file bio-link site (`index.html`). Almost everything you'd
want to change lives in one clearly-labeled `CONFIG` section near the top of
the `<script>` tag — open the file, search for the setting name, edit it,
save, push.

## The look, in one line

Dark, moody, fully transparent glass cards, smooth rounded corners everywhere,
one consistent warm-gold accent glow used for borders/hover/progress instead
of a different loud color per section — closer to a "guns.lol"-style profile
than a template-y dashboard.

## What changed in this full pass

- **Every card is now a true glass panel** — heavier blur (28px), almost no
  background tint, so the moody background glow reads straight through. Cards
  glow softly with the accent color on hover instead of just brightening.
- **Removed the cursor sparkle trail.** It ran a `requestAnimationFrame` loop
  forever on every pointer move for a purely decorative effect — real
  performance cost for very little payoff, so it's gone.
- **Redesigned the tab bar** — icons + labels, a thin animated underline
  under the active tab instead of a solid filled pill.
- **Redesigned panel headers** — the small eyebrow label is now a soft pill
  badge, and titles get a hairline divider under them.
- **Redesigned progress bars, tags/chips, anime tiles, and the music player**
  — thinner gradient-fill bars with a glow, pill-shaped tags, anime tiles with
  a cleaner hover lift, a bigger album cover and centered transport controls.
- **The "About Me" box** no longer looks like a bolted-on paper clipping — it's
  now a smooth glass panel with a soft accent glow border and a large quote
  mark, matching the rest of the site instead of standing apart from it.
- **Background glow simplified** to two soft accent-toned blooms instead of
  four different colored blobs, for a calmer, more "moody" feel.

- **No more Cloudinary.** Songs now play straight from files you keep in this
  repo — see [Music](#music) below.
- **Anime tab now has 4 switchable lists** — Watching, Paused, Dropped,
  Completed — instead of dumping everything into one big grid. Tap a pill to
  swap; only one list is ever shown at a time.
- **Fixed the flashing/flickering cards.** The old 3D mouse-tilt effect
  combined a live `rotate()` transform with the glass blur (`backdrop-filter`)
  on the same element — that combo is a known browser rendering bug and is
  what was causing the flashing on the edges of cards, on both phone and
  desktop. The tilt has been replaced with a lighter cursor-follow glow that
  doesn't rotate the card, so the blur never has to be re-rendered mid-motion.
- **Toned down the color palette.** The site kept its dark background but no
  longer assigns a different loud accent color to every tab/badge/chip — it's
  one consistent accent color throughout, with color only used where it's
  actually meaningful (Discord online/idle/dnd status dots, Spotify's brand
  green, etc).
- **Optional custom click sound**, off by default — see [Click sound](#click-sound-optional)
  below.

## Folder structure

```
your-repo/
├── index.html
├── README.md          ← this file
├── audio/              ← put your song files here
├── covers/              ← put your cover art here
├── sfx/                 ← optional — a custom click sound, if you want one
├── fonts/               ← already existed — custom fonts
├── videos/               ← already existed — background video clips
└── favicon/              ← already existed — tab icon
```

## Music

1. Drop your song files into `/audio` (mp3 is safest; wav/ogg/m4a also work
   in most browsers).
2. Drop matching cover art into `/covers` (jpg/png/webp).
3. Open `index.html`, find the `TRACKS` array (search for `const TRACKS`),
   and add one line per song:

   ```js
   const TRACKS = [
       { title: 'Song Name', artist: 'Artist Name', file: 'song-name.mp3', cover: 'song-name.jpg' },
   ];
   ```

   - `file` must exactly match the filename you put in `/audio`.
   - `cover` must exactly match the filename you put in `/covers`. Leave it
     as `''` if you don't have art for that track — it'll just fall back to
     the default placeholder image automatically.
4. Delete a track by deleting its line from the array (and its files, if you
   want to clean up).

That's the whole workflow — no accounts, no uploads to a third-party service,
no IDs to copy around. Everything lives in the repo.

## Anime tab

The Anime tab pulls straight from your AniList account (set by
`ANILIST_USERNAME` in the config) and shows four switchable lists:
**Watching, Paused, Dropped, Completed**. Only one is shown at a time — tap a
pill to swap between them, no page reload. If a list has more than 10 shows
in it, you'll get a random 10 with a shuffle button and a "see all" toggle to
expand the full list. Nothing needs to be edited here — it updates
automatically whenever you update your AniList profile.

## Click sound (optional)

By default, every button/tab/card plays a small synthesized "blip" sound —
no files needed, it's generated in the browser. If you'd rather use a real
sound clip:

1. Drop your sound file into `/sfx` (mp3/wav/ogg all work).
2. Open `index.html`, find this line near the top of the config:

   ```js
   const CLICK_SOUND_FILE = '';
   ```

   and point it at your file:

   ```js
   const CLICK_SOUND_FILE = 'sfx/click.mp3';
   ```

3. Save. Every existing click sound on the site now uses your file instead.
   Leave it blank (`''`) to keep the default and skip the `/sfx` folder
   entirely.

You can also tweak `CLICK_SOUND_VOLUME` (0 to 1) right below it.

## Other things worth knowing

- **Favicon** — replace the file at `favicon/favicon-32.png` with whatever
  image you want in the browser tab.
- **Avatar decoration** — set by `AVATAR_DECORATION_SRC` in the config; point
  it at any image/gif in the repo root, or set to `''` to turn it off.
- **Custom cursor** — set by `CUSTOM_CURSOR_IMAGE`; same idea.
- **Background video** — `BG_VIDEO_SRC` / `BG_VIDEO_SRC_MOBILE` in the
  config, plus the `<source>` tags near the top of `<body>`.
- **Quick action buttons** (on the Status tab) — edit the
  `STATUS_QUICK_ACTIONS` array in the config to add/remove/reorder them.
- **Discord presence, Last.fm stats, and synced lyrics** are all live and
  don't need any manual editing — they update automatically based on the
  `DISCORD_ID`, `LFM_USER`/`LFM_KEY`, and Spotify activity respectively.

## If something looks off

Open your browser's dev tools (F12) → Console tab. Nearly everything on this
site is wrapped so one broken integration (say, AniList being down) won't
take out the rest of the page — you'll just see an error logged for that one
piece instead of a blank site.
