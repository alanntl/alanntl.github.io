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
p(catchment) = P(true catchment mean dwell > true parchment mean dwell)
clamped to [0.10, 0.90] — a fresh browser is exactly 50:50
```

The model is Bayesian (conjugate-normal flavour): each edition carries a
belief over this visitor's true mean dwell — the posterior mean shrinks
toward a neutral 45 s prior with the weight of one visit, and the posterior
variance falls as visits accumulate, so *confidence* moves the odds, not
just the ratio. Updates are deliberately tempered while samples are few —
the comparison carries √(n/(n+5)) of its weight, and the variance gets its
own prior so a handful of tidy-looking visits can't fake certainty (one 2:1
visit ⇒ p≈0.57; eight consistent ones ⇒ 0.90; an unvisited edition holds
the draw at 50:50). The 10% floor keeps the losing edition sampled forever. Sessions contribute one
Welford sample each; everything lives in `localStorage`
(`edition-bandit-v3`) — no server, each browser learns alone. The
**Experiment** section of the site explains this to visitors and renders
their own state live: the draw, the two posterior curves, the ledger, and
a real reset button.

Practical bits: `?edition=catchment|studio` forces one; the draw is pinned per
tab (`sessionStorage`) so reloads mid-read don't reshuffle; a visible toggle
with the live odds sits in the rail/menu — a site whose thesis is *can you
trust it* should not hide that it is running an experiment on the reader. The
probability rule lives in `bayesP()`/`postOf()` near the EDITIONS module.

## Art direction

The visual language is taken from the instruments of the subject's own field
rather than applied as decoration:

- **The streamline field** — the full-viewport canvas is a flow field. Particles
  advect through noise and leave hairline trails that accumulate like a plotter
  drawing. It is a picture of flow, which is what the research is about.
- **The hydrograph** — the navigation on the home page is a gauging-station
  trace. Stage height at each station is the number of entries in that section,
  so the chart is a real reading of the site. Every station is a link.
- **Serif display against monospace labels** on deep ground in catchment;
  display grotesque against the same mono in studio. The field only reads
  against deep colour, which is why it belongs to catchment alone.

## Editing it — the CMS

**https://alanntl.github.io/admin/** is a [Sveltia CMS](https://github.com/sveltia/sveltia-cms)
editor (v0.178.0, vendored at `admin/sveltia-cms.js` — no CDN). Each section
of the site is its own page in the editor — Structure, Home, Projects,
Writing, Research, Background, Contact — backed by one JSON each under
`content/`, so every save is one small commit. The `pages-poke` workflow
requests a Pages build on every push, so saves go live in a minute or two.

**Structure page:** rename, reorder or hide sections (nav, menu and the
hydrograph all follow), and edit the three headline lines. Rows naming a
built-in panel or a Custom page's filename appear; anything else is ignored,
so a typo can't make a dead button.

**Instant preview — the exact page, while you type:** keep
**https://alanntl.github.io/?preview** open beside the editor (the editor's
Live Site button opens it). It renders the real site — field, editions,
photo — and every 2.5s it lays the editor's own unsaved draft (autosaved to
IndexedDB on this origin) over the saved content, so the page updates as
you type, before you press Save. The badge reads "draft (unsaved)" until
you save, then the same view becomes the committed one.

**New pages:** Custom pages collection → Add page (title, intro, blocks) →
Save. Then Structure → add a row with the page's filename as the id — it
appears in the nav and on the hydrograph, weighted by block count. Tick
Hidden to keep it link-only. A hidden worked example lives at `#example`.

Signing in, two ways, both serverless:

- **Personal access token** — create a fine-grained PAT scoped to only this
  repo with *Contents: read and write*, and paste it into the CMS sign-in.
  Nobody else can edit: the token is yours and stays in your browser.
- **Work with local repository** — in a Chromium browser, pick your local
  clone; edits write straight to disk and you commit/push yourself. No token.

The arrays inside `index.html` remain as an offline fallback — the deployed
site renders from the JSONs under `content/`, so don't hand-edit the arrays
expecting the live site to change.

## The content schema

`content/` holds one JSON per page: `structure`, `home`, `projects`,
`writing`, `research`, `background`, `contact`. Entries in
Projects / Writing / Research look like:

```json
{
  "name": "Quickmini",
  "year": "2026",
  "status": "live",
  "blurb": "One paragraph.",
  "tags": ["ffmpeg.wasm"],
  "links": [{"label": "Use it", "href": "https://…"}]
}
```

`status` of `live` or `current` gets the accent chip; the first link becomes
the title link. Background rows are `{field, when, blurb, current}`, working
rules are `{name, blurb}`, contact rows are `{label, detail, href}`. The CMS
enforces all of this, so the JSON only needs hand-editing if you prefer git.

Custom pages live at `content/pages/<slug>.json` as `{title, lede, blocks}`.
Only a new *built-in* list section (like Projects) is a code change now.

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
