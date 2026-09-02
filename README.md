# Baseplate Nesting Planner

A single-page tool that works out the fewest [Gridfinity SnapClip](https://makerworld.com/en/models/1034973-gridfinity-base-snapclip-system)
baseplates that fill a drawer, and shows what extra plates buy you in stiffness.

**[Open the planner →](https://USERNAME.github.io/REPO/)**

Type the inside dimensions of a drawer or shelf. You get a scale drawing of the
nesting, a print list, an exact clip count, and a readout of the joints that
actually get fastened.

![screenshot](docs/screenshot.png)

## Why it exists

Filling a drawer with Gridfinity baseplates looks like a tiling problem and mostly
isn't. The tiling part is trivial. What matters is which arrangement holds together
once it is clipped, and that depends on where the clip slots physically are.

## What it knows

**The minimum plate count is fixed.** For a `W × H` grid from plates no larger than
`m`, the fewest plates is always `⌈W/m⌉ × ⌈H/m⌉`. Verified by exhaustive search for
every grid up to 12 × 12, with 1 × 1 plates excluded. You cannot beat it — and
staggering the joints costs no extra plate, because each course can split its width
differently and still use the same number of pieces.

**A contact of L units carries exactly L−1 clips.** Slots sit at the interior 42 mm
marks along a plate edge; the corners have none. A 4 × 3 plate therefore has 3 slots
on each long side and 2 on each short side.

**Every junction is a dead point.** A clip needs a slot on both sides, so both sides
must be one plate running past the point. At any junction — T or four-way — one side
changes, and no clip fits. A T is still the better joint, because the plate spanning
it carries material across; a four-way carries nothing.

**Stagger by two units, never by one.** A one-unit offset leaves two plates touching
over a single unit with a junction at each end: zero clips, joined by friction. Offset
by two and that contact takes one clip. This is the rule that decides most layouts.

**252 mm is the shallowest run that can avoid a through-seam.** At five units of depth
or less the run is one plate thick, so every joint across it reaches both edges. Swept
across widths of 8, 12 and 20 units: at 3–5 units deep a through-seam is unavoidable;
at 6 and above it never is.

**A 1 × 1 plate has no clip slots at all**, and a one-unit-wide run longer than a single
plate cannot be joined anywhere along its length. The tool refuses those rather than
drawing something unbuildable.

## The rigidity number

A weighted index of the defects, for ranking layouts of the *same* opening against each
other: 30% through-seams, 24% contacts left without a clip, 15% clip density along the
seams, 13% plates fastened on one side only, 10% four-way junctions, 8% one-unit slivers.

It is not a stiffness measurement, it is not comparable across different openings, and
it says nothing about how well any particular clip holds. The three option cards are the
useful output; the number just orders them.

## How the layouts are found

Two generators feed one scorer:

- **Courses.** Beam search over how each course splits its width, where a course may buy
  an extra plate to move its joints two or more units clear of the course below.
- **Randomised fill**, weighted against creating one-unit contacts and one-cell gaps.
- **A polish pass** that re-tiles a random window of a promising layout and keeps the
  result if it is stiffer. Restarts alone almost never find layouts that break
  through-seams in *both* directions — that needs something herringbone-like, and greedy
  left-to-right filling will not stumble into it.

"Stiffest" is ranked lexicographically — nothing unclipped, then fewest through-seams,
then fewest four-way junctions, then score, then fewest plates — not by the blended index.
An unclipped contact outranks a through-seam because those plates are genuinely not joined.

## Caveat worth reading

The clip rule above is the foundation of every number on the page, and it came from
reading the part geometry, not from measuring printed plates. If your plates behave
differently — if a clip does seat at a T-junction, say — every clip count here is wrong.
Please open an issue if you find that.

## Running it

One file, no build, no dependencies:

```
git clone https://github.com/USERNAME/REPO.git
open index.html
```

Or fork it and turn on GitHub Pages (Settings → Pages → deploy from `main`).

Plate sizes are set by the "largest plate you can print" dropdown, so it works for other
baseplate systems with the same 42 mm pitch — but the clip geometry is specific to
SnapClip, so the clip counts will not transfer.

## Credit

Baseplates and clips: **Gridfinity base SnapClip System** by MH-Design on MakerWorld.
This tool does not redistribute any of that geometry; it only plans layouts that use it.

Gridfinity itself was designed by [Zack Freedman](https://www.youtube.com/@ZackFreedman).

## License

MIT — see [LICENSE](LICENSE).
