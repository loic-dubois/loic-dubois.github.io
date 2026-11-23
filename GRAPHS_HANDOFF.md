# Handoff: graphs of minimal obstructions to untangling

This folder is separate from the obstructions viewer (`obstructions.html` and `obstructions.js`). That viewer shows *maps*, drawn on cut-open surfaces. This one shows *graphs*.

- `graphs.html`: a viewer that draws every graph listed in `graphs.js`. Open it directly from disk, keeping `graphs.js` in the same folder.
- `graphs.js`: the data. After its first two lines (`window.GRAPHS =`), it is plain JSON.
- `GRAPHS_HANDOFF.md`: this file.

## 1. What the list is

A map f : G → S from a graph to a compact orientable surface is *critical* if it cannot be untangled (made homotopic to an embedding) while every restriction to a proper subgraph can. A graph G is in the list when **some** critical map from G exists, on some surface. Graphs are given with degree-2 vertices suppressed, so every vertex is topological. Loops and parallel edges are allowed, and graphs may be disconnected.

The list currently has 33 graphs, of cycle rank 1 to 7 and with at most 8 vertices. Each graph comes from at least one of these families:

| Family | How the graphs were found | Status |
|---|---|---|
| trivial / single curve | the cycle (any non-simple class); K5 and K3,3 under a contractible map | complete |
| cyclic (annulus) | exhaustive search for critical maps to the annulus, ranks ≤ 8, plus Appendix D for rank 9 | complete: 17 graphs, rank ≤ 7 |
| embedded loop graph, surface N(L) | exhaustive search over spanning tree, letters and chord diagram, with k ≤ 4 loops | ranks 3–5 only; adds 7 graphs, and also gives K3,3 a non-trivial critical map |
| loop-graph family (λ cannot be untangled) | constructions by hand | partial: figure-eight, two disjoint cycles, theta, dumbbell, figure-eight ⊔ cycle, dumbbell ⊔ cycle. Theta ⊔ cycle is impossible. |

**What is not known.** The list is not known to be complete:

- The embedded-loop-graph family was searched only up to rank 5, and only on N(L). Capped surfaces were not searched.
- The loop-graph family is open for the shapes rank 2 ⊔ rank 2 and rank 3 ⊔ cycle, and for connected rank-3 shapes.
- The paper's general bound is cycle rank ≤ 37, far beyond what was searched.

## 2. The data in `graphs.js`

```
window.GRAPHS = {
  "description": "...",
  "graphs":     [ graph, ... ],   // graphs known to admit a critical map
  "candidates": [ graph, ... ]    // undecided, see below
}
```

Each `graph` has:

- `id`: `graph1`, `graph2`, … or `candidate1`, ….
- `vertices`: n. Vertices are numbered 0 … n−1.
- `edges`: a list of pairs `[u, v]`. A pair `[v, v]` is a loop, and repeated pairs are parallel edges.
- `rank`: the cycle rank, meaning edges − vertices + number of components.
- `families`: the families (as in the table above) in which a critical map from this graph was found. Only in `graphs`.
- `witnesses`: for each family, either an id from `obstructions.js` (`A…` for annulus maps, `G…` for maps on N(L)) or a short description of a critical map. Only in `graphs`.
- `pos`: the initial drawing positions, one `[x, y]` per vertex in [−1, 1]², with y pointing up. These positions are only a layout, not part of the mathematics.

`candidates` are graphs of cycle rank 4–5 that were left undecided. Each has maps on N(L) that passed every exact test, but some edge deletion could not be decided, and no map from the graph was either confirmed or excluded. They are not known to be obstructions.

## 3. The viewer

The viewer draws each graph as a static figure, with no text. Graphs from `graphs` come first, then a thin rule, then the `candidates`. Vertices are placed at `pos`, parallel edges are drawn as symmetric arcs, and loops sit in the widest free angle around their vertex.

To add or change graphs, edit `graphs.js`: copy an entry and give it new `vertices`, `edges` and `pos`. The viewer draws whatever the file lists.

## 4. How entries were checked

- **Annulus witnesses:** the Lemma D.2 untangling test, in two independent implementations that agree on ranks 3–6. The A8 case was also checked with the weak-embedding reduction.
- **N(L) witnesses:** the gadget realizability test of Lemma 1.5, run on every edge deletion. Deletions of a tree edge were settled with a re-rooted gadget or with exact certificates.
- **Loop-graph family:** hand arguments, using intersection numbers on the torus and the boundary curves of regular neighbourhoods.

More detail is in the obstructions handoff (`HANDOFF.md`) and in the project README.
