# alanntl.github.io

Alan Ng's personal site — Research Fellow at Western Sydney University, applying
AI and data analytics to water science.

One self-contained `index.html`, no build step, no framework, no CDN, no external
resources of any kind. Served straight from the `alanntl` branch by GitHub Pages
at **https://alanntl.github.io/**.

## Two editions, one document

The same DOM, the same content arrays, two complete visual systems scoped by
`html[data-style]`:

- **CATCHMENT** — the animated night piece: dark ground, serif, the streamline
  field, the overture, film grain.
- **STUDIO** — the daylight cut: white ground, display-size grotesque, one
  electric blue, silky motion — masked type entrances, scroll reveals, and
  buttons that lean toward the cursor. Award-site energy, zero web fonts.

Which edition a visit opens with is a draw made in `<head>` before first paint.
The odds are learned **per browser** from dwell time (visible seconds, capped at
600 s/session, sub-second fragments ignored):

```
p(catchment) = mean dwell catchment / (mean catchment + mean studio)
clamped to [0.25, 0.75] — a fresh browser is exactly 50:50
```

The floor binds only when one edition holds attention more than 3× longer, and
it is what keeps the estimate alive: both editions keep being sampled, so no
early lucky streak can lock the loser out. Everything lives in `localStorage`
(`edition-bandit-v1`) — there is no server, so each browser learns its own odds
rather than pooling across visitors.

Practical bits: `?edition=catchment|studio` forces one; the draw is pinned per
tab (`sessionStorage`) so reloads mid-read don't reshuffle; a visible toggle
with the live odds sits in the rail/menu — a site whose thesis is *can you
trust it* should not hide that it is running an experiment on the reader. The
probability rule lives in `pCatchment()` in the EDITIONS module if you ever
want to reshape it (softmax with a temperature, or a win-rate Beta posterior,
are the two obvious alternatives).

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

## The flow field

**It is a real incompressible flow, not an angle map.** The usual generative-art
shortcut is to take a noise value and use it directly as a heading. That is not a
physical field — it has arbitrary divergence, so particles pile into sinks and
evacuate sources, and re-seeding quietly hides it. Measured mean `|div|` was 1.5%
of local speed.

Instead `psi(x,y)` is a **stream function** and the velocity is its perpendicular
gradient:

```
u =  ∂ψ/∂y        v = -∂ψ/∂x
```

which is divergence-free identically, since `∂²ψ/∂x∂y − ∂²ψ/∂y∂x = 0`. That is
the condition for incompressible 2D flow, and it is why the level sets of ψ *are*
the streamlines — the drawing is a genuine streamline plot. Measured mean `|div|`
is now `3e-7`, five orders of magnitude better.

The mouse is a **Rankine vortex** superposed on it — irrotational `K/(2πr)`
outside a 34px core, solid-body rotation inside so there is no singularity. A
point vortex is itself divergence-free, so the combined field stays
incompressible (measured `1.9e-5`). Its reach comes from the `1/r` decay, not
from a tuned cutoff radius.

Numbers that matter, all measured rather than guessed:

- `GAIN` — maps `grad(ψ)` to px/frame. At 940 the median speed was 0.92px, under
  the 1.1px visibility floor, so most particles clamped and the speed variation
  vanished. **2050** puts the median at 2.0px, p90 at 3.6.
- `CIRC` — vortex circulation. 2600 gives 6.9px/frame at r=60 and 3.45px at
  r=120, against the 2.0px median base flow.
- the fade alpha in `frame()` — a trail decays to 1/e in roughly `1/alpha`
  frames. At `.034` the canvas wiped in half a second and 0.36% of pixels were
  inked; at `.0022` it holds ~7s and settles at ~8%.
- `COUNT()` — particle budget, scaled to viewport area and capped.

Step length follows the field's own magnitude rather than being constant, which
is what makes the vortex legible; it is floored at 1.1px (sub-pixel segments
dissolve under antialiasing) and capped at 9px so the vortex core cannot fling a
particle across the canvas in one frame.

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
