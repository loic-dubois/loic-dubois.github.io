# Handoff: minimal obstructions to untangling

This folder contains:

- `obstructions.html`: a page that draws every obstruction listed in `obstructions.js`. A viewer can drag the vertices and the points on glued sides.
- `obstructions.js`: the list of obstructions, one entry per line. After the first two lines (`window.OBSTRUCTIONS =`) it is plain JSON.
- `HANDOFF.md`: this file.

## 1. What the obstructions are

Throughout, *G* is a graph and *S* is a compact orientable surface. A map f : G → S can be *untangled* if it is homotopic to an embedding. A map is *critical*, or a minimal obstruction, if it cannot be untangled but its restriction to every proper subgraph can. It is enough to check the subgraphs G − e. Graphs are given with degree-2 vertices suppressed, so every vertex is topological. Loops and parallel edges are allowed.

The theorem behind this work: every map that cannot be untangled contains a critical restriction with at most 72 topological vertices.

The file lists critical maps of two kinds.

**Cyclic maps (ids A1–A18).** The image of π1(G) is cyclic. By Lemma D.1, these are exactly the critical maps to the open annulus, and they are critical on every surface that has a simple non-contractible curve. The list is **complete**:

- ranks 3–7: 18 maps on 17 graphs;
- rank 8: none, found by an exhaustive search restricted, by Lemma D.8, to a 2-connected block plus at most two pendants;
- rank 9: none, by the case analysis of Appendix D (the bond-centre family and split graphs, all tested).

So cyclic critical maps have cycle rank at most 7, and at most 8 topological vertices. Two further cases are not in the file:

- a single cycle in a non-simple class;
- K5 and K3,3 under a contractible map.

**Maps with an embedded loop graph (ids G1–G61, only the certified ones are listed).**

- **Setup:** factor f along a spanning tree T, as λ ∘ μ. Here λ embeds a one-vertex loop graph L with k ≥ 2 loops, and the surface is N(L), its regular neighbourhood.
- **Certified:** each entry is critical on N(L). Lemma 1.5 shows it cannot be untangled. For every edge, deleting it gives either a realizable gadget or a re-rooted realizable gadget.
- **Search range:** ranks 3–5, with at most 4 loops.
- **Not included:** 44 more candidates, at ranks 4 and 5, where some deletion stayed undecided.
- **Caveat:** surfaces obtained by capping boundary components of N(L) were not searched.

**Not in the file: the loop-graph family.** These are maps where λ itself cannot be untangled, and they are the only source of disconnected critical graphs. Known so far:

- **Realized:** cycle, two disjoint cycles, figure-eight, theta, dumbbell, figure-eight ⊔ cycle, dumbbell ⊔ cycle.
- **Impossible:** theta ⊔ cycle.
- **Open:** the remaining shapes with total rank at most 4.

## 2. How `obstructions.js` encodes them

The file assigns a JSON array, with one object per obstruction, to `window.OBSTRUCTIONS`. It is a `.js` file rather than `.json` because browsers only let a page opened from disk load a neighbouring file as a script. With this format, opening `obstructions.html` directly works with no server and no file picker.

```json
{"id": "A8", "map": {...}, "drawing": {...}}
```

### 2.1 `map`: the combinatorial obstruction

All variants share these fields:

- `vertices`: n. Vertices are numbered 0 … n−1.
- `edges`: a list of pairs `[u, v]`. A pair `[v, v]` is a loop. Each edge is oriented from u to v, and the other fields refer to edges by their position in this list.
- `surface`: either `"annulus"` or a description of N(L), such as `"genus 0, 3 boundary components"`.

**Annulus maps** (`surface == "annulus"`) also have:

- `omega`: a list of integers, one per edge. `omega[i]` is the winding of edge i taken from u to v. That is, its algebraic number of crossings with a fixed arc joining the two boundary circles of the annulus. ω is a cocycle and only its cohomology class matters. Every cycle has winding −1, 0 or 1. A loop with `omega = ±1` goes once around the annulus.

**Maps on N(L)** also have:

- `chord_diagram`: a list of k pairs `[p, q]` of positions in 0 … 2k−1. The positions are the 2k half-loop ends around the single vertex of L, in cyclic order. Pair j says that loop j leaves at position p and comes back at position q. The surface N(L) is the orientable ribbon graph built from this rotation. Non-crossing chords give a planar surface, for example a pair of pants when k = 2. Crossing chords add genus.
- `labels`: one entry per edge, each either `0` or `[j, s]`:
  - `0`: the edge stays in the disk around the vertex of L. The edges labelled 0 contain the spanning tree T.
  - `[j, s]` with s = +1: the edge, taken from u to v, leaves u, enters the band of loop j at its end p, follows the loop once, comes out at end q, and goes to v. With s = −1 it does the same in the reverse direction, entering at q and leaving at p.

### 2.2 `drawing`: what the page draws

The drawing shows the surface cut open along one arc across each band (its cocore), which turns it into a polygon. Coordinates are in the plane with y pointing up. The polygon is inscribed in the unit circle.

- `sides`: a list of matched sides. Entry i has these fields:
  - `A`, `B`: the endpoints of a glued side.
  - `C`: the next corner. The segment B → C is a boundary side of the surface, drawn plain.
  - `loop`: which loop j this side belongs to. The page draws `loop + 1` arrowheads on it.
  - `end`: 0 if this side is the end p of loop j, and 1 if it is the end q.
  - `partner`: the index of the matched side with the same `loop`.

  The polygon is traced A₀ → B₀ → C₀ = A₁ → B₁ → …, counterclockwise.
- **Gluing rule.** The point A + t(B − A) on side i is identified with the point A′ + (1 − t)(B′ − A′) on its partner side. The gluing reverses orientation along the side, so the surface is orientable. Arrowheads point from A to B when `end = 0` and from B to A when `end = 1`. The two sides of a pair then have matching arrows, as in Figure 1 of the paper.
- `verts`: the positions of the graph's vertices, with index i matching vertex i of `map`.
- `edges`: the drawn segments. Each one is `{"a": endpoint, "b": endpoint}`, where an endpoint is either:
  - `{"v": i}`: vertex i; or
  - `{"s": i, "t": x}`: the point at parameter x on matched side i.

  Each segment is drawn straight. Segments with the same two endpoints are drawn as symmetric arcs.

**How drawn segments relate to graph edges:**

- A graph edge with label `0`, or ω = 0, is one segment between two vertices.
- A graph edge that goes once through a band appears as two segments. One goes from u to a point (i, t) on one side of the band. The other goes from v to the glued point (partner, 1 − t) on the other side.
- So the drawing has one segment per edge that stays in the disk and two per edge that goes through a band. Endpoints on glued sides always come in glued pairs.
- An annulus map is drawn as a square whose left and right sides are glued. An edge with winding ±1 appears as two segments that meet the two sides at the same height. A loop at a vertex appears as a line through the vertex from one glued side to the other.

Each drawing has exactly one crossing, and one crossing is the minimum, since the map cannot be untangled. The layout came from a crossing-free realization of G − e, for one edge e (Lemma 1.5). The edge e was then added back, crossing a single other edge, and the vertices were moved so all edges are straight.

## 3. Adding or changing entries

- **Effect on the page:** the page draws whatever `obstructions.js` contains. Entries with an `id` starting with `A` go in the first grid, all others in the second.
- **Required fields:** a new entry needs a `drawing`. The `map` block is documentation only; the page does not read it.
- **Opening locally:** open `obstructions.html` directly, from disk or from a server. Keep `obstructions.js` in the same folder. To get the list as plain JSON, drop the first two lines and the final `;`.
- **Saved edits:** a viewer's edits are stored in that viewer's browser, keyed by id, as `obst2-<id>`. The file itself never changes.

## 4. Provenance and checks

- **Annulus maps:**
  - two independent implementations of the Lemma D.2 test (Python and C), which agree on ranks 3–6;
  - rank 7 run in C only;
  - A8 also confirmed with the weak-embedding reduction.
- **Maps on N(L):**
  - an exhaustive enumeration over (graph, spanning tree, letters, chord diagram), in C;
  - certificates for the deletions it left undecided, in Python;
  - the realizability test was cross-checked against the annulus results. There is no second independent implementation for k ≥ 2, so treat these entries as computer-certified, not proved by hand.
- The project document `claude/obstructions/README.md` gives the full catalogue and methods, including the undecided candidates and the open loop-family cases.
