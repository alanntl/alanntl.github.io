# alanntl.github.io

Alan Ng's personal site — Research Fellow at Western Sydney University, applying
AI and data analytics to water science.

One self-contained `index.html`, no build step, no framework, no CDN, no external
resources of any kind. Served straight from the `alanntl` branch by GitHub Pages
at **https://alanntl.github.io/**.

## Art direction

The visual language is taken from the instruments of the subject's own field
rather than applied as decoration:

- **The streamline field** — the full-viewport canvas is a flow field. Particles
  advect through noise and leave hairline trails that accumulate like a plotter
  drawing. It is a picture of flow, which is what the research is about.
- **The hydrograph** — the navigation on the home page is a gauging-station
  trace. Stage height at each station is the number of entries in that section,
  so the chart is a real reading of the site. Every station is a link.
- **Serif display against monospace labels**, on a single dark ground. The page
  deliberately has no light counterpart: the field only reads against deep
  colour, so committing to one look is the design decision.

## Editing it

All content lives in the arrays at the top of the `<script>` block. The markup is
generated from them, so you never touch HTML to add an entry.

| Array        | Drives                                     |
| ------------ | ------------------------------------------ |
| `SECTIONS`   | The rail, the mobile menu and the hydrograph |
| `PROJECTS`   | Projects panel                             |
| `WRITING`    | Writing panel (currently empty)            |
| `RESEARCH`   | Research panel                             |
| `BACKGROUND` | The fields list on the Background panel     |
| `PRINCIPLES` | The working rules on the Background panel   |
| `CONTACT`    | Contact panel                              |

An entry looks like this:

```js
{
  name: "Quickmini",
  year: "2026",
  status: "live",            // "live" and "current" get the accent chip
  blurb: "One paragraph.",
  tags: ["ffmpeg.wasm"],
  links: [{label: "Use it", href: "https://…"}],   // first link titles the entry
}
```

Adding a section means adding one object to `SECTIONS` and one matching
`<section class="panel" data-panel="…">`. The rail, the mobile menu and the
hydrograph all build themselves from `SECTIONS`, so they can't drift apart.

## The flow field, if you ever tune it

Three numbers in the `FIELD` module govern whether it is visible at all, and they
only work together:

- `drift` — pixels advanced per frame. Below about 1px the segments are
  sub-pixel and antialiasing dissolves them. At 0.78 the field was invisible.
- the fade alpha in `frame()` — a trail decays to 1/e in roughly `1/alpha`
  frames. At `.034` the canvas was wiped in half a second and 0.36% of pixels
  were inked; at `.0022` it holds ~7s and reaches 8.5%.
- `COUNT()` — particle budget, scaled to viewport area and capped.

`prime()` traces the streamlines once, synchronously, at load (~25ms). Without it
the animated loop needs about 7 seconds to build a visible drawing, so the first
paint would be a blank black page. It is also what reduced-motion visitors keep
as their single static frame.

**Verifying any of this in a background tab does not work.** A hidden tab
freezes `requestAnimationFrame` *and* CSS transitions, so the canvas stays empty
and `getComputedStyle` returns the pre-transition value. Both look exactly like
bugs. Check `document.hidden` before investigating.

## Still to fill in

- **Employers and dates.** `BACKGROUND` lists the fields you've worked in, not
  specific roles — LinkedIn's experience section wasn't in what I had.
- **`WRITING` is empty** and shows an honest empty state. Add an object and the
  panel fills itself.
- **No email.** Contact lists LinkedIn and GitHub only. Add an address to
  `CONTACT` when you've decided which one you want public.
- **`matchesQuery`** is still plain substring matching, which is why searching
  `git agents` finds nothing while `agents git` works.

## Running it

It's a single file, so opening `index.html` in a browser works:

```sh
npx serve .
```

## Checks worth repeating after an edit

- The flow field is actually inked — sample the canvas, don't trust a screenshot
  taken in a background tab.
- Mobile at 375px, where the rail is replaced by the menu bar.
- `prefers-reduced-motion`, which keeps the primed field and stops the loop.
- Keyboard focus, including the hydrograph stations, which are focusable and
  respond to Enter and Space.
