# alanntl.github.io

Alan Ng's personal site. One self-contained `index.html`, no build step, no
framework, no CDN — served straight from the default branch by GitHub Pages at
**https://alanntl.github.io/**.

Built on the same pattern as the [AIWC site](https://github.com/marvi-groundwater/aiwc):
every section is a `<section class="panel">` that sits in the DOM at once, and a
small script toggles `hidden`, syncs `aria-selected`, rewrites `document.title`
and keeps `location.hash` in step so deep links work.

## Editing it

All content lives in the arrays at the top of the `<script>` block. The markup is
generated from them, so you never touch HTML to add an entry.

| Array        | Drives                                    |
| ------------ | ----------------------------------------- |
| `SECTIONS`   | The sidebar, the mobile menu and the strip |
| `PROJECTS`   | Projects panel                            |
| `WRITING`    | Writing panel                             |
| `RESEARCH`   | Research panel                            |
| `PRINCIPLES` | Work panel                                |
| `CONTACT`    | Contact panel                             |

An entry looks like this:

```js
{
  name: "Quickmini",
  year: "2026",
  status: "live",            // "live" gets the accent chip
  blurb: "One paragraph.",
  tags: ["ffmpeg.wasm"],
  links: [{label: "Use it", href: "https://…"}],   // first link titles the entry
}
```

Adding a section means adding one object to `SECTIONS` and one matching
`<section class="panel" data-panel="…">`. The nav, the mobile menu and the strip
all build themselves from `SECTIONS`, so they can't drift apart.

## The strip

The block of segments on the home page is the navigation. Each segment is a
section, and its width comes from `WEIGHTS` — the number of entries that section
holds — so the strip doubles as a readout of what's on the site. It's the
keep/skip timeline from Quickmini and Cutaway, reused as a menu.

Segments have a `min-width` so a short section never clips its own label, which
means the widths are ordered by weight rather than strictly to scale.

## Still to fill in

Search the file for `TODO` — these are the things that couldn't be sourced from
your own repositories:

- The "What I'm doing now" paragraph on the home panel (role, affiliation, city).
- Both `WRITING` entries are placeholders.
- Your role on the Australia India Water Centre entry in `RESEARCH`.
- The email in `CONTACT`, currently `you@example.com`.

## Running it

It's a single file, so opening `index.html` in a browser works. To check it the
way it'll be served:

```sh
npx serve .
```

## Checks worth repeating after an edit

- Both colour schemes — light and dark are peers here, not a theme bolted on.
- Mobile at 375px, where the rail is replaced by the menu bar and the strip
  scrolls horizontally.
- `prefers-reduced-motion`, which removes the playhead and every transition.
- Keyboard focus, which is visible on every control.
