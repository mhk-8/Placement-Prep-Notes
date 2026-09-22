
# Project: Image Preprocessing Pipeline on GPU

> **Course:** CS6023 GPU Programming · **Instructor:** Prof. Rupesh Nasre · **Aug 2026**
> **Track:** SDE / Systems · **Priority: ⭐⭐**

**How to use this project:** it is your smallest GPU project, so do not lead with it. Its value is
as a **vehicle for CUDA fundamentals questions** — memory coalescing, thread indexing, host-device
transfer, correctness tolerance. If an interviewer wants to test whether you actually understand
CUDA rather than just having used it, this project is the cleanest ground to do it on.

⚠️ **Do not over-claim.** It is a well-executed course assignment, not research. Framing it as
"I built the preprocessing stage of an inference pipeline" is accurate and sufficient.

---

## 1. The 20-second version

> "I implemented the standard ImageNet preprocessing transform — grayscale, bilinear resize, center
> crop, normalise — entirely in CUDA C++, validated to within 1e-3 of the reference."

## 2. The 60-second version

> "Every image classifier runs the same four-stage transform before inference: convert or adjust
> the colour representation, resize, center-crop to the network's input size, and normalise by the
> channel mean and standard deviation. In a typical deployment that runs on the CPU and becomes the
> bottleneck, because the GPU is sitting idle waiting for it.
>
> I implemented all four stages as CUDA kernels. The interesting one is bilinear resize: you have
> to get the coordinate mapping right — I used align-corners semantics — because an off-by-half-pixel
> error produces output that looks plausible but doesn't match the reference implementation. The
> other three are one-thread-per-pixel kernels where the work is in the memory layout: row-major,
> channel-interleaved access so that consecutive threads touch consecutive addresses and the reads
> coalesce. I managed the full host-device pipeline and validated correctness to a 1e-3 tolerance
> against the CPU reference."

---

## 3. The four stages ⭐

| Stage | Operation | The subtlety |
|---|---|---|
| **Grayscale** | `Y = 0.299R + 0.587G + 0.114B` | Trivially parallel; the work is purely in coalesced access |
| **Bilinear resize** | Weighted average of the 4 nearest source pixels | **Coordinate mapping** — align-corners vs half-pixel (see §4) |
| **Center crop** | Copy a sub-rectangle | Index arithmetic; strided reads if done naively |
| **Normalise** | `(x − mean) / std` per channel | Channel-dependent constants; keep them in constant memory or registers |

---

## 4. Bilinear interpolation and the coordinate mapping ⭐⭐⭐

This is the part worth knowing precisely, because it is the part with a real correctness trap.

**The interpolation itself:** for an output pixel mapping to source coordinate `(x, y)` with
`x0 = ⌊x⌋`, `x1 = x0+1`, `dx = x − x0` (similarly for `y`):
```
value = (1−dy)·[ (1−dx)·I(x0,y0) + dx·I(x1,y0) ]
      +    dy ·[ (1−dx)·I(x0,y1) + dx·I(x1,y1) ]
```
Four texture reads, three linear interpolations.

**The coordinate mapping — where implementations disagree ⚠️⭐⭐:**
```
ALIGN-CORNERS = TRUE :   x_src = x_dst · (W_src − 1) / (W_dst − 1)
    The corner pixels of input and output map exactly onto each other.

ALIGN-CORNERS = FALSE (half-pixel centres):
                         x_src = (x_dst + 0.5) · (W_src / W_dst) − 0.5
    Pixel CENTRES are aligned; this is geometrically correct and is the OpenCV/PIL default.
```

⭐ **This is the single best follow-up to invite on this project.** The two conventions produce
visibly different results at small scales, PyTorch's `interpolate` exposes it as a flag precisely
because the ecosystem disagreed for years, and a subtle mismatch here is a classic source of
"my model works in training but not in deployment" bugs. Knowing *why* the flag exists is a strong
signal.

---

## 5. The CUDA engineering ⭐⭐

**Thread mapping:**
```cuda
int x = blockIdx.x * blockDim.x + threadIdx.x;
int y = blockIdx.y * blockDim.y + threadIdx.y;
if (x >= W || y >= H) return;              // ⚠️ the bounds guard is mandatory
int idx = (y * W + x) * C;                 // row-major, channel-interleaved
```

**Why row-major, channel-interleaved (HWC) ⭐:**
```
HWC (interleaved) : consecutive threads read consecutive bytes  ⇒ COALESCED
CHW (planar)      : better for convolution kernels, but for per-pixel transforms it means
                    three separate strided streams

For this pipeline HWC wins, because every stage is per-pixel. For the convolution that
follows, CHW usually wins — which is exactly why real pipelines transpose at the boundary.
```
⭐ Saying that last sentence — that the right layout **depends on the consumer** — is what
distinguishes understanding from recitation.

**Grid/block sizing:** 2-D blocks of 16×16 or 32×8 (a multiple of the 32-thread warp, so no lanes
are wasted), grid sized with the standard ceiling `(W + bx - 1) / bx`.

**Host-device pipeline:** allocate → `cudaMemcpy` H2D → launch → `cudaMemcpy` D2H → free. For a
real deployment, pinned (page-locked) host memory plus streams would overlap transfer with compute —
worth naming as the obvious next step.

---

## 6. Validation ⭐

```
Compared against the CPU/reference implementation, element-wise, with a 1e-3 tolerance.
```
⚠️ **Why a tolerance rather than exact equality:** floating-point operations are not associative,
and the GPU may use fused multiply-add (FMA) and different rounding than the CPU. Bit-exact
agreement is not achievable and not the right target; a tolerance appropriate to the downstream
consumer is. For preprocessing feeding a classifier, 1e-3 is far below the noise floor of the model
itself.

---

## 7. Anticipated follow-ups ⭐⭐

<details><summary>"What is memory coalescing and why does it matter here?"</summary>

When the 32 threads of a warp access consecutive, aligned addresses, the hardware services them in
a small number of wide memory transactions (e.g. one 128-byte transaction). Scattered accesses
require one transaction each, wasting most of the bandwidth. These kernels are entirely
memory-bound — there is almost no arithmetic — so coalescing *is* the performance.
</details>

<details><summary>"Would this actually be faster than the CPU version?"</summary>

Honest answer: **for a single image, probably not** — the H2D and D2H transfers dominate and the
kernel work is trivial. It wins for **batches**, and it wins most when the image is already on the
device (as it is in a real inference pipeline, where the next stage is the network itself). The
real motivation is avoiding the round trip, not the raw kernel speed. Saying this shows you
understand where the cost actually is. ⭐
</details>

<details><summary>"Could you fuse the four stages into one kernel?"</summary>

Yes, and you should — that is the right optimisation. Fusing eliminates three round trips to global
memory. The reason to write them separately first is correctness and testability: validate each
stage against the reference, then fuse. Naming the "correct first, fuse second" discipline is
better than claiming you fused from the start.
</details>

<details><summary>"Why not use texture memory / NPP / DALI?"</summary>

Texture memory gives hardware bilinear interpolation for free and would be the production choice.
NVIDIA's NPP and DALI already do exactly this pipeline. The point of the assignment was to
implement the mechanism, not to use the library — but knowing the library exists and what it does
better is the right thing to say.
</details>

<details><summary>"What is the difference between align-corners true and false?"</summary>

See §4 — this is the question you want. Have the two formulas ready.
</details>

---

## 8. Limitations to state proactively

```
- Single-image, synchronous; no stream overlap, no pinned memory, no batching.
- Stages are not fused, so there are unnecessary global-memory round trips.
- Uses plain global memory rather than texture memory, which offers hardware bilinear filtering.
- Production pipelines (DALI, NPP) already solve this better; the value here is understanding
  the mechanism.
```

---

## 9. The 30-second refresh

```
□ Four stages: grayscale → bilinear resize → center crop → normalise
□ Bilinear = 4 reads, 3 lerps; align-corners vs half-pixel coordinate mapping ⭐
□ HWC row-major interleaved ⇒ coalesced for per-pixel work; CHW for convolutions
□ 2-D thread indexing with a bounds guard; 16×16 blocks
□ 1e-3 tolerance because FP is non-associative and FMA differs
□ Honest framing: wins for batches and on-device data, not for one image
```
