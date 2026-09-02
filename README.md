# NEON GRID

> **⚠️ Work in progress.** Playable and fun to wander, but the renderer is
> still being tuned — expect rough edges, especially in how facades read up
> close. Actively being worked on.

A walkable ASCII cyberpunk city that fits in one HTML file.

No engine, no libraries, no assets, no build step. Open the file and you're
standing on a street. Everything on screen — every tower, sign, pedestrian and
puddle — is a text character drawn to a `<canvas>`.

**[Play it →](https://wavy-hec.github.io/neon-grid/)**

![A first-person view down a rain-slicked street of neon-lit ASCII towers](docs/screenshot.png)

## Controls

| | |
|---|---|
| `W` `S` | walk |
| `A` `D` | strafe |
| `←` `→` | turn — or click the view for mouse-look |
| `↑` `↓` | look up / down |
| `Shift` | run |
| `E` | interact with anything that glows |
| `N` | generate a new city |
| `B` `R` `M` `F` | bloom · rain · music · fps |
| `-` `=` | resolution |
| `?` | help |

Find a **service lift** (`I`) beside a tall building, ride it, and look down.

Every city comes from the seed shown on the title card. Add `?seed=yourname`
to the URL for one that's only yours.

## How it works

A raycaster, the way *Wolfenstein 3D* did it, with the pixels swapped for
characters.

Each frame casts one ray per screen column and **marches every tile along it**,
painting front to back while pulling an occlusion ceiling down the screen. That
matters: stop at the first wall you hit and a thirty-storey tower standing
behind a four-storey block is simply never drawn, and the skyline has no layers.
Tiles shorter than the eye render their **roof** instead of their face, which is
what makes a rooftop somewhere you can stand rather than a hole in the world.

Some things that turned out to matter more than expected:

- **Never pick a glyph from brightness alone.** A floor row sits at one distance,
  so a brightness ramp paints the whole row as one repeated character. The floor
  reads its glyph from a world-space hash whose frequency scales with distance;
  the sky is stippled rather than ramped.
- **Light is baked once per city**, not splatted per frame. Roughly 1,400 lights
  fold into a single map at generation time.
- **Glyphs are pre-rendered per colour** into one sheet and blitted, with
  brightness carried on `globalAlpha` — one texture, so the GPU can batch.
- **Tone mapping is not optional.** Without a highlight rolloff, standing next
  to a sign clipped ~40% of the screen to flat white.
- The block and box-drawing characters are **drawn geometrically**, not typed,
  so building fills tile seamlessly whatever font the browser happens to have.

The soundtrack is generated live — walking bass, brushed ride, and a
muted-trumpet line over D dorian. Original, in that idiom; no samples.

## Running it

Clone and open `index.html`. That's the whole procedure — it's a `file://` page
with no dependencies.

```bash
git clone https://github.com/Wavy-Hec/neon-grid.git
cd neon-grid
start index.html      # macOS: open index.html
```

`window.__city` is exposed for poking at: `stats()`, `tp(x, y)`, `newCity()`,
and live `ents` / `lights`.

## Status

Work in progress. What's in and working:

- [x] First-person raycaster with multi-hit marching (layered skylines)
- [x] Procedural city — roads, blocks, towers, parks, a canal, districts
- [x] Rooftops you can ride a lift to and walk on
- [x] Collision, traffic that stops at junctions, crowds that gather
- [x] Baked lighting, canal reflections, animated neon
- [x] Live-generated modal-jazz soundtrack, spatial — it plays from THE BLUE ROOM, a jazz bar you can walk into in every city
- [x] Two-tier renderer: detailed raycast to 46 units, cheap far skyline out to 120
- [x] Smoothed mouse/key look (camera eases toward a target orientation)
- [x] Matrix-style boot sequence that constructs the view column by column
- [x] Traffic: lanes, per-junction signals, cars that brake for the car ahead and for you, turns at junctions
- [x] Pseudo-volumetric cars with headlights, taillights and headlight pools on the wet road
- [x] Cyclists on the kerb lane and flying craft in sky lanes over the rooftops
- [x] Shop interiors visible through the glass, with parallax
- [x] Auto Tour (T) and Empty City (N) modes
- [ ] Facade readability at close range — still tuning
- [ ] More interiors beyond the bar (shops, lobbies)
- [ ] Day/night cycle — built, currently held at night on purpose

## License

MIT
