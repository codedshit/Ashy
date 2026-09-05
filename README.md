# Site updates

This covers everything that changed in `index.html`: the World tab and its
3D globe are gone, and four things replaced/joined it — a comments
section, a new responsive nav, customizable link icons, and a random
per-visit theme.

---

## 1. Removed: World tab / 3D globe

The old "World" tab (spinning 3D globe, country leaderboard, visitor map)
has been fully removed — markup, styles, and script. The simple visit
counter (the eye icon chip and "total visits" stat on the Status tab)
still works exactly as before; only the country-level map/leaderboard is
gone.

---

## 2. Comments / Guestbook

A guestbook now sits below the profile card and tabs, on every visit
regardless of which tab is open. Visitors can post a message with an
optional name, and like or dislike any comment. Dislike counts are
tracked separately from likes, and once a comment hits **5 dislikes** it's
automatically removed.

**Important:** this is a static site with no server, so comments are
stored per-browser in `localStorage` (keys `ashy_comments_v1` and
`ashy_comment_votes_v1`). That means:
- Comments a visitor posts are only visible in *their own* browser — it's
  not a shared, global guestbook that everyone sees the same content in.
- Clearing site data/localStorage wipes them.
- To make it a real shared guestbook, swap the `loadComments` /
  `saveComments` functions (near the bottom of the `<script>` block) for
  calls to a real backend — Firebase, Supabase, a small API of your own,
  etc. — instead of `localStorage`.

To change the auto-removal threshold, edit `COMMENT_DISLIKE_LIMIT` near
the top of the comments code.

---

## 3. Responsive section nav

The Bio / Anime / Status / Audio tabs no longer live inside the profile
card. They're now a separate nav with a toggle button (top-left):

- **Mobile:** the toggle opens a sliding sidebar drawer from the left,
  with a dimmed backdrop. Tap the backdrop, or tap a tab, to close it.
- **Desktop (≥820px wide):** the same nav sits as a horizontal bar above
  the main card. The toggle button collapses/expands it instead of
  sliding a drawer — it's visible by default.

To add, remove, or reorder tabs, edit the buttons inside
`<nav class="site-nav" id="siteNav">` in the HTML — each button's
`data-tab="xxx"` must match a `<div class="tab-panel" id="tab-xxx">`
further down the page.

---

## 4. Custom link icons

Each entry in the `LINKS` array (search for `const LINKS = [` in the
script) can use either a FontAwesome icon class, or a custom image.

To use your own icon:
1. Drop the image file (PNG, JPG, JPEG, GIF, WEBP, or SVG) into the
   `icons/` folder that sits next to `index.html` in this repo.
2. Add or edit an entry in `LINKS`, pointing `img` at it:
   ```js
   { label: 'MyThing', href: 'https://example.com', img: 'icons/myicon.png', className: 'mything' }
   ```
3. That's it — no hosting, no extra setup. A full URL works too if you'd
   rather link an image hosted elsewhere.

If the path is ever wrong (typo, missing file), the button quietly falls
back to a generic link icon instead of showing a broken image.

---

## 5. Random theme every visit

Each time the page loads, it picks a random accent color paired with a
matching dark background tint from a fixed list of themes, and applies
it before the page paints (so there's no flash of the default look).
Refresh the page a few times to see it cycle through blues, teals,
greens, violets, corals, golds, pinks, and more — always dark, always
glassy.

The palette lives in the `<script>` block in `<head>`, in the `THEMES`
array (right after the "RANDOM THEME" comment). Each entry is:

```js
{ a: '#4A7CD6', bg: '#0a0a0c', bgs: '#050506' }
```

- `a` — the accent color for the visit
- `bg` / `bgs` — the two background shades (regular / strong)

Add, remove, or tweak entries to change what can come up; there's no
separate toggle or config for this, it just runs on every load.
