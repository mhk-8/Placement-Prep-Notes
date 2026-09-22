
# Project: Constraint-Based Points-to Analysis on GPU (M.Tech Project)

> **Guide:** Prof. V. Krishna Nandivada · **Started:** June 2026 · **Status:** Ongoing
> **Track:** SDE / Systems / Compilers · **Priority: ⭐⭐⭐ — this is your flagship**

**Why this is your strongest asset:** the intersection of **static analysis** and **GPU
parallelism** is genuinely rare. Most candidates have one or the other; almost none have both, and
almost nobody at campus level has parallelised a *compiler analysis*. Lead with this project for
systems, compiler, HPC and infrastructure roles.

**The corresponding risk:** it is deep and niche. If you cannot explain it to someone who has never
taken a compilers course, it becomes a liability rather than an asset. Section 2 below exists for
exactly that.

---

## 1. The 20-second version

> "My M.Tech project parallelises an Andersen-style points-to analysis for Java on NVIDIA GPUs,
> cutting analysis time by 78% over the traditional scheme."

## 2. The 60-second version — for a **non-compiler** interviewer ⭐⭐⭐

> "Points-to analysis answers a single question for a compiler: *for each pointer or reference in
> a program, which objects could it possibly point to?* Almost every serious optimisation and
> almost every static bug-finder needs that answer first — you cannot safely inline, or prove two
> references don't alias, or find a null dereference, without it.
>
> The problem is that it is expensive. The standard formulation turns the whole program into a set
> of set-inclusion constraints and then solves them to a fixpoint, and on real Java programs that
> can take minutes to hours.
>
> I'm parallelising it on a GPU. The analysis is a worklist fixpoint over a graph, which is
> irregular and data-dependent — exactly the workload GPUs are worst at — so most of the work is
> restructuring the data so the GPU can actually use its parallelism: points-to sets as
> device-resident bit-vectors, the dependence graph in CSR, one warp per node. The baseline scheme
> we build on, PInter, already cuts the constraint count by 96%, and the GPU mapping brings total
> analysis time down 78%."

⭐ Note what that version does: it defines the problem **in terms of why anyone cares** before
touching a single technical term, and it names the difficulty (irregular workload on a GPU) rather
than the machinery.

## 3. The 5-minute version — for a **systems/compiler** interviewer

Cover, in this order:
```
1. The constraint system (§4)
2. Why the fixpoint is the bottleneck (§5)
3. The GPU data-structure mapping (§6)
4. The three bottlenecks and their fixes (§7)
5. Validation and profiling (§8)
6. Current status and limitations (§10)
```

---

## 4. The technical core — the constraint system ⭐⭐⭐

Andersen's analysis is **inclusion-based** (also called subset-based). Every pointer-relevant
statement in the program becomes a set-inclusion constraint over points-to sets `pts(·)`:

| Statement form | Constraint | Name in our scheme |
|---|---|---|
| `a = new T()` | `{o} ⊆ pts(a)` | **Member** (base / address-of) |
| `a = b` | `pts(b) ⊆ pts(a)` | **Propagation** (simple / copy) |
| `a = b.f` | `∀ o ∈ pts(b) : pts(o.f) ⊆ pts(a)` | **Conditional** (complex load) |
| `a.f = b` | `∀ o ∈ pts(a) : pts(b) ⊆ pts(o.f)` | **Conditional** (complex store) |
| `x = p.m(y)` | resolve the target of `m` from `pts(p)`, then bind params/return | **FunctionCall** |

**Why loads and stores are the hard part ⭐⭐:** a copy constraint is a *fixed* edge — `b → a`,
known statically. A load or store constraint is **dynamic**: the set of edges it implies depends on
`pts(b)`, which is still being computed. So solving the constraints **adds new edges to the graph
while you are traversing it**. That self-modifying structure is the whole difficulty, on CPU and
far more so on GPU.

**Andersen vs Steensgaard — know this comparison ⭐⭐**

| | **Andersen (inclusion-based)** | **Steensgaard (unification-based)** |
|---|---|---|
| Constraint for `a = b` | `pts(b) ⊆ pts(a)` — one-directional | `pts(a) = pts(b)` — merges the two |
| Precision | Higher | Lower (over-merges) |
| Complexity | `O(n³)` worst case | Near-linear, `O(n·α(n))` via union-find |
| Used when | Precision matters (optimisation, bug-finding) | Scale matters more than precision |

Both are **flow-insensitive** (statement order ignored) and **context-insensitive** in their basic
form. Flow-sensitive and context-sensitive variants are far more precise and far more expensive —
know that the precision/cost axis exists, because it is a natural follow-up question.

---

## 5. Why the fixpoint is the bottleneck

```
worklist ← all nodes with initial points-to sets
while worklist is not empty:
    n ← pop(worklist)
    for each outgoing edge n → m:
        if pts(n) ⊄ pts(m):
            pts(m) ← pts(m) ∪ pts(n)
            push(m onto worklist)
    resolve any complex constraints newly enabled by pts(n)   ← may ADD edges
```

Characteristics that make it painful:
- **Irregular, data-dependent traversal** — no static access pattern to optimise for.
- **Monotone growth** — sets only grow, so the same node is reprocessed many times.
- **Load imbalance** — a handful of nodes accumulate enormous points-to sets while most stay tiny.
- **Dynamic graph** — complex constraints add edges mid-flight.

⭐ Say this line: *"It's a monotone dataflow fixpoint over a graph that rewrites itself while you
traverse it."* That single sentence tells a systems interviewer you understand the shape of the
problem, not just the code.

---

## 6. The GPU mapping ⭐⭐⭐

| Element | Representation | Why |
|---|---|---|
| **Points-to sets** | Device-resident **bit-vectors** | Set union becomes bitwise OR — perfectly parallel, no pointer chasing, constant-size per node |
| **Dependence graph** | **CSR** (`row_offsets[V+1]`, `col_indices[E]`) | Compact, coalesced neighbour access, the standard GPU graph format |
| **Work assignment** | **One warp per node** | The 32 lanes cooperatively OR 32 words of the bit-vector at a time; neighbours are read coalesced |
| **Set union** | Shared-memory staging + warp-level primitives | Avoids repeated global-memory round trips |
| **Frontier** | Compacted array, rebuilt each round | Keeps warps busy instead of iterating over dead nodes |

**Why warp-per-node rather than thread-per-node ⭐⭐:**
```
THREAD-PER-NODE : each thread walks its own neighbour list.
                  → neighbour lists differ wildly in length ⇒ severe warp divergence
                  → each thread's memory accesses are scattered ⇒ uncoalesced

WARP-PER-NODE   : 32 lanes cooperate on ONE node's work.
                  → lanes read CONSECUTIVE words of the bit-vector ⇒ perfectly coalesced
                  → lanes read CONSECUTIVE entries of the CSR neighbour list ⇒ coalesced
                  → divergence within the warp is minimal; imbalance moves to the warp level,
                    where frontier compaction can rebalance it
```

**Bit-vector vs sorted-list representation** — a likely follow-up:
```
BIT-VECTOR : union = bitwise OR, O(N/64) words, branch-free, ideal for GPU.
             ⚠️ Wastes memory when sets are SPARSE (most nodes point to few objects).
SORTED LIST: compact for sparse sets, but union = merge, which is sequential and divergent.
HYBRID     : bit-vector for dense nodes, list for sparse ones — the natural extension,
             and a good answer to "what would you do next?"
```

---

## 7. The three bottlenecks and their fixes ⭐⭐⭐

This is the heart of the 5-minute version, and the part that shows engineering rather than
implementation.

| Bottleneck | Cause | Fix applied |
|---|---|---|
| **Uncoalesced access** | Data-dependent graph traversal; neighbours scattered in memory | CSR layout + warp-per-node so lanes read consecutive addresses |
| **Warp divergence** | Points-to sets grow at very different rates ⇒ unequal work per node | Frontier compaction using warp-level primitives (`__ballot_sync` + `__popc` for the prefix offset), so only active nodes occupy warps |
| **Atomic contention** | Many edges propagate into the same target set ⇒ serialised atomics on one address | **Per-block privatisation**: accumulate into a shared-memory copy first, merge to global once per block |

⭐ **The general pattern to name:** *"privatise, then merge"* — the same idea as a per-thread
histogram or a per-block reduction. Saying that you recognised it as an instance of a standard
pattern is worth more than describing the fix.

---

## 8. Validation and profiling ⭐⭐

```
CORRECTNESS : validated against the Soot implementation on the DaCapo benchmark suite.
              The points-to sets must match exactly — this is a SOUNDNESS-critical analysis,
              so "mostly right" is not a result. ⭐
PERFORMANCE : profiled with Nsight Compute.
              Metrics that matter here: achieved occupancy, global memory throughput and
              the L2 hit rate, warp execution efficiency (the divergence measure), and
              atomic throughput.
```

⚠️ **Be ready for "how do you know it's correct?"** For an analysis that a compiler will act on,
this is the first question a senior compiler engineer asks. The answer is: differential testing
against a trusted reference implementation (Soot) on a standard suite (DaCapo), comparing the full
points-to relation, not a summary statistic.

---

## 9. Anticipated follow-ups ⭐⭐⭐

<details><summary>"What is points-to analysis used for, concretely?"</summary>

Dead-code elimination, inlining and devirtualisation (if `pts(p)` has one element, the virtual call
is monomorphic and can be inlined), escape analysis and stack allocation, null-dereference and
taint analysis in static bug-finders, race detection, and IDE features like "find all references".
Give two or three, not a list.
</details>

<details><summary>"Why is this O(n³)?"</summary>

With `n` variables, each points-to set can hold `O(n)` objects, and in the worst case propagation
happens along `O(n²)` edges, each moving `O(n)` elements. The cubic bound is why the constraint
reduction in PInter (96% fewer constraints) matters so much — reducing `n` in the constraint system
has a super-linear payoff.
</details>

<details><summary>"Why a GPU? Isn't this a bad fit?"</summary>

Honest answer, and the right one: **it is a bad fit on the surface**, and that is what makes it
interesting. The analysis is irregular and data-dependent, which is the opposite of the regular,
dense workload GPUs are designed for. But it has two properties that rescue it: the core operation
(set union) is a bitwise OR, which is perfectly data-parallel, and the frontier is wide — thousands
of nodes are typically active at once. The work is in restructuring the data so those two
properties dominate.
</details>

<details><summary>"How do you handle the dynamic edges from complex constraints?"</summary>

New edges are generated on-device and appended during the round, then the graph is rebuilt/compacted
between rounds. The alternative — round-tripping to the host to rebuild CSR — would dominate the
runtime, so keeping generation on-device is essential.
</details>

<details><summary>"How do you know the fixpoint has terminated?"</summary>

The sets are monotone (they only grow) and bounded (by the number of allocation sites), so the
fixpoint is guaranteed to terminate. Detection: a device-side flag set whenever any set changes in
a round; when a full round completes with no change, the frontier is empty and the analysis is done.
</details>

<details><summary>"What is the speedup, and against what baseline?"</summary>

Quote it precisely and scope it: the constraint reduction is 96% and total analysis time is down
78% relative to the traditional scheme. Be clear which part comes from the PInter formulation and
which from the GPU mapping — conflating the two is the sort of imprecision that gets caught.
</details>

<details><summary>"Have you considered a multi-core CPU implementation instead?"</summary>

Yes — and it is the right comparison to acknowledge. CPU parallelisation of Andersen's analysis is
well-studied and handles irregularity better. The GPU case is interesting because of the bit-vector
union throughput and the very wide frontier; the honest position is that the GPU wins on the
propagation-heavy phases and the CPU is more robust on the irregular ones, which is why a hybrid is
a plausible next step.
</details>

<details><summary>"What is PInter and what is CC'26?"</summary>

PInter is the scheme this work builds on, published at CC (International Conference on Compiler
Construction) 2026. ⚠️ **Know the paper's core contribution in one sentence** — parameterised
constraints with interleaved generation and solving, which is what produces the 96% constraint
reduction. If you list a paper on your resume, you will be asked about it.
</details>

---

## 10. Limitations to state proactively ⭐⭐

```
- Flow- and context-insensitive, like Andersen's baseline. Precision is bounded by that,
  and adding context sensitivity would multiply the constraint count substantially.
- Bit-vectors waste memory on sparse points-to sets; a hybrid dense/sparse representation
  is the obvious improvement and is not implemented yet.
- Validated on DaCapo; behaviour on programs with very different shape (extremely deep
  call graphs, heavy reflection) is untested. Reflection in particular is a known soundness
  hole for any Java points-to analysis. ⭐
- The work is ongoing, so the current numbers are for the phases implemented so far.
```

⭐ **Reflection is worth mentioning unprompted.** Every serious Java static-analysis person knows
it is the soundness hole, and naming it signals you understand the field's real problems.

---

## 11. Explaining your specific contribution ⚠️

Because this builds on published work with a guide, be scrupulous about attribution:
```
✅ "PInter is Prof. Nandivada's group's work — the parameterised constraint formulation and the
    96% reduction come from that. My contribution is the GPU parallelisation: the data-structure
    mapping, the kernel design, and the bottleneck work on coalescing, divergence and atomics."
❌ "I reduced constraint count by 96% and analysis time by 78%."
```
Over-claiming shared work is the fastest way to lose a senior interviewer's trust, and the honest
version is *more* impressive because the GPU mapping is substantial on its own.

---

## 12. The 30-second refresh before an interview

```
□ Andersen = inclusion-based, O(n³), flow- and context-insensitive
□ Four constraint types: Member, Propagation, Conditional, FunctionCall
□ Bit-vectors + CSR + warp-per-node
□ Three bottlenecks: coalescing, divergence, atomic contention → CSR, frontier compaction,
  per-block privatisation
□ Validated against Soot on DaCapo; profiled with Nsight Compute
□ 96% constraint reduction (PInter), 78% total time reduction
□ Limitation to volunteer: flow/context insensitivity, and reflection
```
