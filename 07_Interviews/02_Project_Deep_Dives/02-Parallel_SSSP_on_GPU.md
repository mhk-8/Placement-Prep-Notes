
# Project: Parallel Single-Source Shortest Path on GPU

> **Course:** CS6023 GPU Programming · **Instructor:** Prof. Rupesh Nasre · **Sep 2026**
> **Track:** SDE / Systems / HPC · **Priority: ⭐⭐⭐**

**Why this is a strong interview asset:** shortest path is an algorithm every interviewer knows, so
they can follow you immediately — and the engineering underneath (Δ-stepping, worklist design,
memory bounds) is genuinely non-trivial. It is the easiest of your GPU projects to discuss with a
*non*-GPU interviewer, which makes it a good fallback when the flagship is too niche.

---

## 1. The 20-second version

> "I implemented Δ-stepping single-source shortest path in CUDA over CSR graphs, sustaining 2
> million vertices and 300 million edges on a single start-up allocation."

## 2. The 60-second version

> "Dijkstra is the textbook SSSP algorithm but it is inherently sequential — it processes one
> vertex at a time in priority order, so there is nothing to parallelise. Bellman-Ford is fully
> parallel but does far too much redundant work. Δ-stepping sits between them: it groups vertices
> into buckets of width Δ by tentative distance, and processes a whole bucket in parallel. You get
> Dijkstra-like work efficiency with Bellman-Ford-like parallelism, and Δ is the dial between them.
>
> I implemented it in CUDA C++ over CSR graphs with `atomicMin` for concurrent distance updates.
> The two pieces of real engineering were replacing the per-bucket arrays with a Near/Far two-queue
> worklist — which bounds queue memory at `O(|V|)` regardless of graph diameter — and an adaptive Δ
> mode that retunes the bucket width each round based on the actual distance distribution. It runs
> up to 20 source queries and sustains 2M vertices with 300M edges, about 2.6 GB, on one allocation
> at start-up."

---

## 3. Δ-stepping — the algorithm ⭐⭐⭐

```
Bucket i holds vertices with tentative distance in [i·Δ, (i+1)·Δ)

LIGHT edges : w ≤ Δ   — relaxing them may put the target back in the SAME bucket,
                        so the bucket must be re-processed until it drains (inner loop)
HEAVY edges : w > Δ   — relaxing them always moves the target to a LATER bucket,
                        so they are relaxed exactly ONCE, after the bucket settles

for each bucket i in increasing order:
    repeat:                             # light phase
        R ← current contents of bucket i
        empty bucket i
        relax all LIGHT edges out of R  (in parallel)
    until bucket i stays empty
    relax all HEAVY edges out of all vertices ever in bucket i   (in parallel, once)
```

**The Δ dial ⭐⭐ — know this:**
```
Δ → 0    : each bucket holds one distance value ⇒ behaves like DIJKSTRA
           work-efficient, almost no parallelism
Δ → ∞    : one bucket holds everything ⇒ behaves like BELLMAN-FORD
           maximally parallel, lots of redundant relaxation
Good Δ   : roughly the average edge weight, tuned so buckets are wide enough to
           fill the GPU but narrow enough to avoid re-relaxing the same vertex repeatedly
```

**Why the light/heavy split exists:** it is entirely about *settling*. A light edge can keep a
vertex inside the current bucket, so the bucket is not stable until light relaxation reaches a
fixpoint. A heavy edge always pushes past the bucket boundary, so it can be deferred to a single
pass — which saves re-doing it in every inner iteration.

---

## 4. Data structures and the CUDA mapping ⭐⭐

| Element | Representation |
|---|---|
| Graph | **CSR**: `row_offsets[V+1]`, `col_indices[E]`, `weights[E]` |
| Distances | `int/float dist[V]` in global memory, updated with `atomicMin` |
| Worklist | **Near/Far two-queue** (see §5) plus a **state mask** to block duplicate enqueues |
| Δ retuning | `thrust::sort_by_key` + `lower_bound` over the active distances |

**Why `atomicMin` ⭐:** multiple threads may relax edges into the same target vertex in the same
round. `atomicMin(&dist[v], newDist)` makes the update race-free without a lock, and it is
*idempotent and order-independent* — which is exactly the property that makes the algorithm safe to
parallelise at all. Min is commutative and associative, so any interleaving gives the same result.

**The state mask ⚠️:** without it, a vertex whose distance improves twice in one round gets pushed
onto the queue twice, and the duplicates multiply across rounds. A per-vertex flag ("already in the
queue this round") set atomically with `atomicCAS`/`atomicExch` prevents the blow-up.

---

## 5. The Near/Far worklist — the key engineering decision ⭐⭐⭐

**The problem with literal buckets:** a textbook Δ-stepping implementation allocates an array per
bucket. The number of buckets is `⌈maxDist/Δ⌉`, which scales with the **graph diameter** — unknown
in advance, and potentially huge on a road network or a long path graph. You either over-allocate
massively or reallocate mid-run.

**The fix:**
```
NEAR queue : vertices whose tentative distance falls in the CURRENT Δ window
FAR  queue : everything beyond it

Each round:  process NEAR to a fixpoint (light edges), then heavy edges,
             then SPLIT the FAR queue: anything now inside the next window moves to NEAR.

Memory: O(|V|) total, INDEPENDENT of the number of buckets or the graph diameter.  ⭐
```

⭐ **This is the answer to "what was the most interesting engineering decision?"** It is a clean
memory-bound argument, it is easy to state, and it shows you thought about the *shape* of the input
rather than just implementing the paper.

**Adaptive Δ ⭐:** rather than a fixed Δ, each round sorts the active tentative distances
(`thrust::sort_by_key`) and picks the window boundary so that roughly a target number of vertices
(`K = 65,536`) are active — enough to saturate the GPU without over-widening the bucket.
`lower_bound` is used for the boundary so that **equal distances never split across the boundary**,
which would otherwise cause a vertex to be processed in two different rounds.

---

## 6. Memory and scale ⭐

```
2M vertices, 300M edges, ≈ 2.6 GB:
  col_indices : 300M × 4 B  = 1.2 GB
  weights     : 300M × 4 B  = 1.2 GB
  row_offsets : 2M × 4 B    = 8 MB
  dist, masks, queues       : a few × 2M × 4 B ≈ 50 MB
                              ────────
                              ≈ 2.6 GB
```

**One start-up allocation ⭐:** all buffers are sized from `V` and `E` and allocated once, before
the first query. `cudaMalloc` is expensive and synchronising; doing it per round or per query would
show up immediately in the profile. Since up to 20 source queries reuse the same graph, the
allocation amortises across all of them.

---

## 7. Anticipated follow-ups ⭐⭐⭐

<details><summary>"Why not just parallelise Dijkstra?"</summary>

Dijkstra's correctness depends on extracting the globally minimum tentative distance each step —
that is a strict sequential dependency. You can parallelise the *relaxation* of one vertex's
neighbours, but the outer loop remains serial, so speedup is bounded by the average degree. Δ-
stepping relaxes the requirement from "the single minimum" to "everything within Δ of the minimum",
which is what creates the parallel frontier.
</details>

<details><summary>"How do you choose Δ?"</summary>

Rule of thumb from the literature: roughly `1/d` where `d` is the maximum degree, or on the order of
the average edge weight. In practice it depends on the weight distribution, which is why I
implemented the adaptive mode — it retunes each round to keep about 65k vertices active, which is
the size that saturates the GPU on the hardware I used.
</details>

<details><summary>"What is the work/depth complexity?"</summary>

Δ-stepping is `O(n + m + d·L)` expected work for random edge weights, where `d` is the maximum
degree and `L` the maximum shortest-path weight — so it is work-efficient up to the re-relaxation
term. The depth is `O((L/Δ)·log n)` in expectation. The honest framing in an interview: it is
work-efficient for a good Δ and degrades toward Bellman-Ford's redundancy as Δ grows.
</details>

<details><summary>"Does it handle negative weights?"</summary>

No — like Dijkstra, the bucket ordering assumes non-negative weights. A negative edge could
decrease a distance after its bucket has been finalised. Bellman-Ford handles negatives; the GPU
analogue would be a fully parallel relax-all-edges loop, which is simpler but does far more work.
</details>

<details><summary>"What did the profile say the bottleneck was?"</summary>

Be specific and honest. The realistic answer for this workload: memory-bound, dominated by the
irregular `col_indices` reads and `atomicMin` traffic, with warp efficiency dropping when degree
distribution is skewed. The frontier size per round is the key knob — too small and the GPU is
idle, too large and re-relaxation wastes work.
</details>

<details><summary>"Why 20 source queries?"</summary>

It amortises the graph load and the allocation, and it is a realistic usage pattern — batch SSSP
from multiple sources is what you actually need for things like betweenness centrality or
multi-source routing. It also exposes whether the per-query state is correctly reset, which is a
real class of bug.
</details>

<details><summary>"How would you extend this?"</summary>

Multi-GPU partitioning (with halo exchange at the partition boundary), a direction-optimising
push/pull switch as in BFS, or bucket fusion. For very large graphs, out-of-core streaming of the
CSR. Pick one and say why it is the next thing, not a list.
</details>

---

## 8. Limitations to state proactively

```
- Non-negative weights only, by construction.
- Δ tuning is workload-dependent; the adaptive mode helps but is a heuristic, not a guarantee.
- Single-GPU; graphs beyond device memory are not handled.
- Benchmarked on synthetic/standard graphs — behaviour on very high-diameter road networks
  (where the frontier is narrow and the GPU starves) is the known weak case. ⭐
```

⭐ That last point is the honest, knowledgeable limitation: **Δ-stepping on GPUs is weakest exactly
where the frontier is narrow**, which is road networks. Naming it shows you understand when your
own solution is the wrong tool.

---

## 9. The 30-second refresh

```
□ Δ-stepping = Dijkstra ↔ Bellman-Ford dial; light (w ≤ Δ, loop to fixpoint) vs heavy (once)
□ CSR + atomicMin (commutative, order-independent ⇒ safe to parallelise)
□ Near/Far two-queue ⇒ O(|V|) memory independent of diameter  ← the key decision
□ State mask blocks duplicate enqueues
□ Adaptive Δ via thrust::sort_by_key + lower_bound, target 65,536 active vertices
□ 2M vertices / 300M edges / ~2.6 GB, one start-up allocation, 20 source queries
□ Limitation to volunteer: non-negative weights; weak on high-diameter graphs
```
