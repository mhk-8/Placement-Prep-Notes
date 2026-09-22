
# Project: Multi-Task Visual Perception Pipeline

> **Course:** DA6401 Introduction to Deep Learning · **Instructor:** Prof. Ganapathy Krishnamurthy
> **Mar 2026** · **Track:** ML/CV · **Priority: ⭐⭐⭐**

**Why this is a strong interview project:** multi-task learning with a shared backbone raises
genuinely interesting questions — loss balancing, task interference, gradient conflict — that most
candidates have never had to think about. It also gives you a CNN/segmentation project to pair with
the Transformer one, covering both halves of deep learning.

**The one thing to handle carefully ⚠️:** the localisation result (50% accuracy at IoU ≥ 0.75) is
weak next to the other two numbers. Own it first, explain why, and it becomes a strength. Leave it
to be discovered and it becomes an awkward moment.

---

## 1. The 20-second version

> "A single VGG11 encoder, built from scratch, feeding three heads on Oxford-IIIT Pet — 37-class
> breed classification, bounding-box localisation and pixel-wise segmentation — reaching 0.91
> macro-F1 and 0.87 macro-Dice."

## 2. The 60-second version

> "The idea was one shared backbone doing three jobs at once. I built a VGG11 encoder from scratch
> — with Batch Normalisation added throughout, since the original VGG predates it and doesn't train
> stably without it — and hung three heads off it: a classifier for 37 breeds, a bounding-box
> regressor, and a U-Net-style decoder with skip connections for pixel-wise segmentation.
>
> The interesting part is that the three tasks want different things from the encoder.
> Classification wants global, translation-invariant features; segmentation wants fine spatial
> detail, which is exactly what the pooling layers destroy — that's why the skip connections
> matter. So the shared representation is a compromise, and the loss weighting is the dial that
> decides which task the compromise favours.
>
> Classification reached 0.91 macro-F1 and segmentation 0.87 macro-Dice. Localisation was the weak
> one — 50% accuracy at an IoU threshold of 0.75, which is a strict threshold, and I used a custom
> IoU loss for the box regression. Box regression is the task that benefits least from a shared
> encoder here, and with more time I'd have either given it more capacity or used a proper
> detection head."

⭐ Notice that the weak number is stated **by you, with the reason, in the same breath** as the
strong ones.

---

## 3. The architecture ⭐⭐

```
                      Input image (3 × H × W)
                              │
        ┌─────────────────────▼─────────────────────┐
        │   VGG11 ENCODER (from scratch, + BatchNorm)│
        │   conv-conv-pool blocks, channels doubling │
        │   64 → 128 → 256 → 512 → 512               │
        └──┬──────────────┬─────────────────┬────────┘
           │ (skip conns) │                 │
           │              │                 │
           ▼              ▼                 ▼
   ┌────────────┐  ┌─────────────┐  ┌──────────────────┐
   │ CLASSIFIER │  │  BBOX HEAD  │  │  U-NET DECODER   │
   │ GAP → FC   │  │  FC → 4     │  │  upsample + skip │
   │ → 37 logits│  │  (x,y,w,h)  │  │  → per-pixel mask│
   └─────┬──────┘  └──────┬──────┘  └────────┬─────────┘
         │                │                   │
   cross-entropy       IoU loss          Dice / per-pixel CE
         └────────────────┼───────────────────┘
                          ▼
              L = λ₁·L_cls + λ₂·L_box + λ₃·L_seg      ⭐ the weighting is the whole problem
```

**Why the skip connections ⭐⭐:** the encoder's pooling layers progressively destroy spatial
resolution — which is exactly what classification *wants* (translation invariance) and what
segmentation *cannot afford*. Skip connections carry high-resolution feature maps from encoder
layers directly across to the matching decoder layers, restoring the boundary detail that pooling
threw away. Without them, segmentation masks are blobby and boundaries are wrong.

---

## 4. The genuinely interesting part: multi-task loss balancing ⭐⭐⭐

This is the discussion that makes the project memorable. Be ready to lead it.

```
L_total = λ₁·L_cls + λ₂·L_box + λ₃·L_seg
```

**The problems:**
```
1. DIFFERENT SCALES. Cross-entropy is O(1-4), Dice loss is O(0-1), an unnormalised box
   regression loss can be O(100s). Without weighting, one task's gradient drowns the others.

2. DIFFERENT CONVERGENCE RATES. Classification typically converges much faster than
   segmentation, so a fixed weighting is wrong at different points in training.

3. GRADIENT CONFLICT / NEGATIVE TRANSFER. The tasks can pull the shared encoder in opposing
   directions. When they do, multi-task learning performs WORSE than separate models. ⚠️
```

**Approaches worth naming (this is the "what would you do differently" answer) ⭐⭐:**

| Method | Idea |
|---|---|
| Manual / grid-searched weights | What you did. Simple, works, doesn't adapt |
| **Uncertainty weighting** (Kendall et al.) | Learn `λ_i = 1/(2σ_i²)` with a `log σ_i` regulariser — the network learns each task's noise level and down-weights the noisy ones ⭐ |
| **GradNorm** | Normalise gradient magnitudes across tasks so no task dominates |
| **PCGrad** | When two tasks' gradients conflict (negative dot product), project one onto the other's normal plane |
| Task-specific adapters | Give each task private capacity so the shared trunk carries only what is genuinely shared |

⭐ **Uncertainty weighting is the single best thing to name**, because it reframes the
hyperparameter as something learnable and it has a clean probabilistic justification: the weight
becomes the inverse variance of each task's likelihood.

---

## 5. Metrics — know exactly what each one means ⭐⭐

```
MACRO-F1 (classification)
   Per-class F1, then an UNWEIGHTED mean across the 37 classes.
   ⭐ Macro, not micro, because Oxford-IIIT Pet is roughly balanced (~200 images per breed)
     and every breed should count equally. Micro-F1 would equal accuracy here.

DICE (segmentation)
   Dice = 2|A∩B| / (|A|+|B|)  — the F1 of the mask.
   Always ≥ IoU, and preferred over per-pixel cross-entropy when the foreground is small,
   because CE is dominated by the easy background pixels.
   Relationship: Dice = 2·IoU/(1+IoU)

IoU (localisation)
   IoU = |A∩B| / |A∪B|.  "50% at IoU ≥ 0.75" means half the predicted boxes overlap the
   ground truth by at least 75%.
   ⚠️ 0.75 is a STRICT threshold. At the more common 0.5 the number would be substantially
     higher. Saying this is not an excuse — it is the correct context for the number. ⭐
```

---

## 6. Handling the weak localisation number ⭐⭐⭐

Prepare this answer verbatim. It is the question you will get.

> "Localisation was the weakest of the three, and I think there are three reasons. First, the
> threshold: 0.75 IoU is strict, and the same model looks much better at 0.5 — but I reported the
> strict number because it is the more honest one. Second, box regression benefits least from a
> shared encoder in this setup; classification and segmentation both want semantic features, while
> regression wants precise spatial extent, and it got the least capacity of the three heads.
> Third, a single global box head is a weak design compared with anchor-based or anchor-free
> detection heads — a proper detection head with multi-scale features would be the right fix.
>
> If I redid it, I'd try uncertainty weighting so the box task isn't starved by the loss balance,
> and I'd use a feature-pyramid-style multi-scale head rather than regressing from the final
> feature map."

⭐ Three specific reasons and two specific fixes. That is what turns a weak result into evidence of
judgement.

---

## 7. Anticipated follow-ups ⭐⭐⭐

<details><summary>"Why multi-task learning at all? Why not three separate models?"</summary>

Three arguments: **efficiency** (one backbone, one forward pass — matters enormously at inference),
**regularisation** (the auxiliary tasks constrain the shared representation and can reduce
overfitting, especially with limited data), and **shared structure** (all three tasks genuinely need
to know "where is the animal and what does it look like").

The honest counter: it only works when the tasks are related. When they conflict, you get negative
transfer and three separate models win. Naming both sides is the complete answer. ⭐
</details>

<details><summary>"Why build VGG11 from scratch instead of using a pretrained ResNet?"</summary>

Because the assignment was about understanding the architecture. For the task itself, an ImageNet-
pretrained ResNet or EfficientNet backbone would be substantially better — Oxford-IIIT Pet has only
about 7,400 images, which is far too few to train a backbone from scratch competitively. Say that
plainly; it is the correct engineering judgement and it does not diminish the exercise.
</details>

<details><summary>"Why did you add BatchNorm? VGG doesn't have it."</summary>

VGG predates BatchNorm (2014 vs 2015) and is notoriously hard to train from scratch without it —
the original was trained by initialising a shallow version and growing it. BN allows much higher
learning rates and makes the network far less sensitive to initialisation.

⭐ **Worth adding:** the original "internal covariate shift" explanation has been largely
discredited; the accepted explanation (Santurkar et al.) is that BN smooths the loss landscape.
Knowing that distinction is a genuine signal.
</details>

<details><summary>"Dice loss vs cross-entropy for segmentation — why?"</summary>

Per-pixel cross-entropy is dominated by the background when the foreground is a small fraction of
the image, so the model can score well by predicting "background" nearly everywhere. Dice loss
directly optimises the overlap and is scale-invariant to the foreground size. In practice a
weighted sum of the two often works best — CE gives stable gradients early, Dice shapes the
boundary.
</details>

<details><summary>"How does the U-Net decoder actually upsample?"</summary>

Either transposed convolution (learned upsampling, but prone to checkerboard artefacts) or bilinear
upsampling followed by a regular convolution (the more common modern choice, no artefacts). At each
decoder level the upsampled map is **concatenated** with the matching encoder feature map — U-Net
concatenates rather than adds, which is the difference from a residual connection — and then passed
through convolutions.
</details>

<details><summary>"What is macro vs micro F1, and why did you choose macro?"</summary>

Macro is the unweighted mean of per-class F1, so every class counts equally; micro pools all TP/FP/FN
and is dominated by frequent classes (and equals accuracy in single-label multiclass). Macro is
right here because all 37 breeds matter equally and the dataset is roughly balanced.
</details>

<details><summary>"How would you deploy this?"</summary>

Export to ONNX or TorchScript, quantise to int8 if latency matters, and note that the
single-backbone design is exactly what makes deployment cheap — one forward pass serves three
outputs. The preprocessing is the same transform as my CUDA pipeline project, which is a nice
connection to draw. ⭐
</details>

---

## 8. Limitations to state proactively

```
- Trained from scratch on ~7.4k images; a pretrained backbone would beat this comfortably.
- Localisation is weak (50% @ IoU 0.75) — see §6 for the honest diagnosis.
- Loss weights were tuned manually; uncertainty weighting or GradNorm is the principled fix.
- No ablation isolating how much each task helped or hurt the others — which is the
  experiment that would actually justify the multi-task claim. ⭐
- Single dataset, single domain; no test of transfer.
```

⭐ That fourth point is the sharpest one to volunteer: *"I never ran the ablation that would prove
multi-task learning helped here"* is exactly the kind of self-aware statement senior interviewers
remember favourably.

---

## 9. The 30-second refresh

```
□ VGG11 from scratch + BatchNorm → three heads: 37-class, bbox, segmentation
□ U-Net decoder with skip connections, because pooling destroys the spatial detail
  segmentation needs
□ L = λ₁L_cls + λ₂L_box + λ₃L_seg — scales differ, convergence rates differ,
  gradients can conflict (negative transfer)
□ Better: uncertainty weighting (Kendall), GradNorm, PCGrad
□ 0.91 macro-F1 · 0.87 macro-Dice · 50% loc @ IoU≥0.75 (strict threshold — own it)
□ Dice = 2|A∩B|/(|A|+|B|) = 2·IoU/(1+IoU); preferred when the foreground is small
□ Limitation to volunteer: no multi-task ablation
```
