
# Computer Vision Fundamentals ⭐⭐

> **Core idea in 3 lines**
> 1. An image is a tensor of pixel intensities; every classical technique is a local operation on
>    that tensor, and a CNN learns those operations instead of hand-coding them.
> 2. The task taxonomy — classification, detection, segmentation — determines the output head, the
>    loss and the metric.
> 3. Augmentation and preprocessing matter more than architecture for most practical CV problems.

---

## 1. Images as tensors

```
grayscale : (H, W)            values 0–255 (uint8) or 0–1 (float)
colour    : (H, W, 3)         RGB;  OpenCV reads BGR ⚠️
PyTorch   : (C, H, W)         channels-first ⚠️  (TensorFlow uses channels-last)
batch     : (N, C, H, W)
```

⚠️ Two perennial bugs: BGR/RGB channel swapping when mixing OpenCV with PyTorch, and forgetting to
normalise with the pretraining dataset's mean/std (ImageNet:
`mean = [0.485, 0.456, 0.406]`, `std = [0.229, 0.224, 0.225]`).

**Colour spaces:** RGB (display), HSV (hue/saturation/value — robust to lighting changes, good for
colour-based segmentation), grayscale (many classical algorithms), LAB (perceptually uniform).

---

## 2. Classical operations still worth knowing

| Operation | What it does | Where it survives |
|---|---|---|
| Convolution / kernels | blur (Gaussian), sharpen, edge (Sobel, Laplacian) | preprocessing, the intuition behind CNNs |
| Thresholding (Otsu, adaptive) | binarise | document/OCR pipelines |
| Morphology (erode, dilate, open, close) | clean up binary masks | post-processing segmentation masks ⭐ |
| Canny edges | gradient + NMS + hysteresis | contour finding |
| Contours / connected components | object extraction from masks | counting objects, blob analysis |
| Hough transform | detect lines and circles | lane detection, document deskewing |
| SIFT / ORB keypoints | scale/rotation-invariant features + matching | image stitching, SLAM, template matching |
| Homography + RANSAC | fit a projective transform robustly | panoramas, document rectification ⭐ |
| Histogram equalisation / CLAHE | contrast enhancement | medical and low-light imaging |

⭐ Practical judgement worth showing: if the task is "count the dark blobs on a uniform
background", classical thresholding plus connected components is faster, deterministic and needs no
labels. Reaching for a CNN when OpenCV suffices is a mark against you.

---

## 3. The task taxonomy ⭐⭐⭐

```
 CLASSIFICATION          "what is in this image?"             → one label
 ┌───────────┐
 │   [cat]   │           head: GAP → linear → softmax;  loss: cross-entropy
 └───────────┘           metric: top-1/top-5 accuracy, macro-F1

 LOCALISATION            "where is the one object?"           → one box
 DETECTION               "what and where are all objects?"    → N boxes + classes
 ┌───────────┐
 │ ┌───┐ ┌─┐ │           loss: classification + box regression (smooth-L1 / GIoU)
 │ │cat│ │d│ │           metric: mAP@[.5:.95];  post-process: NMS
 │ └───┘ └─┘ │
 └───────────┘

 SEMANTIC SEGMENTATION   "class of every pixel"               → (H,W) label map
 INSTANCE SEGMENTATION   "…and which object instance"         → per-object masks
 PANOPTIC                semantic + instance combined
 ┌───────────┐
 │ ▓▓▓░░░░▓▓ │           loss: per-pixel CE, Dice, focal;  metric: mIoU, Dice
 └───────────┘

 KEYPOINTS / POSE        joint coordinates → heatmap regression
 DEPTH / FLOW            dense regression
 RETRIEVAL / VERIFICATION  embedding + metric learning (triplet, contrastive, ArcFace)
```

**IoU** — the quantity behind detection and segmentation metrics:

```
IoU = area(A ∩ B) / area(A ∪ B)
```

A prediction counts as a true positive if IoU with a ground-truth box exceeds a threshold
(commonly 0.5). **mAP@[.5:.95]** averages average precision over IoU thresholds 0.5 to 0.95 in
steps of 0.05 — the COCO standard. ⭐

**Non-max suppression:** sort boxes by score; keep the top one; delete every box with IoU above a
threshold against it; repeat. ⚠️ It is a per-class operation, and it fails on heavily overlapping
objects — hence Soft-NMS and set-based detectors like DETR that need no NMS at all.

**Dice vs IoU:** `Dice = 2|A∩B|/(|A|+|B|)` is the F1 of the mask, always ≥ IoU, and is the usual
loss in medical segmentation because it handles small foregrounds better than per-pixel
cross-entropy. ⭐

---

## 4. Data augmentation ⭐⭐⭐

Usually the highest-value knob in a practical CV project.

| Level | Examples |
|---|---|
| Geometric | random resized crop, horizontal flip, rotation, scale, translation, shear, perspective |
| Photometric | brightness/contrast/saturation/hue jitter, grayscale, blur, noise, JPEG artefacts |
| Occlusion | random erasing, cutout |
| Mixing | **mixup** (convex combination of images *and* labels), **CutMix** (paste a patch and mix labels proportionally) |
| Policy | RandAugment, AutoAugment, TrivialAugment |
| Test-time | TTA — average predictions over flips/crops ⭐ |

⚠️ **Augmentations must respect the task's invariances.**
- Do not horizontally flip digits, text, or medical views where laterality matters (left vs right
  lung).
- In detection and segmentation the **boxes and masks must be transformed with the image** — use
  Albumentations, which does this for you.
- Do not augment the validation/test set (except deliberate TTA).
- Colour jitter is wrong when colour *is* the label (e.g. grading ripeness, skin lesions).

---

## 5. Transfer learning recipe ⭐⭐

```
1. Pick a backbone: ResNet-50 / EfficientNet / ConvNeXt / ViT; or a self-supervised
   backbone (DINOv2) or CLIP image encoder when labels are scarce ⭐
2. Replace the head with your number of classes.
3. Freeze the backbone, train the head for a few epochs (fast, stable).
4. Unfreeze and finetune everything at a much lower LR; use discriminative LRs
   (earlier layers ~10× lower).
5. Augment; normalise with the pretraining statistics; use cosine LR with warmup.
6. Monitor per-class metrics and a confusion matrix; inspect the worst examples by loss.
```

| Situation | Strategy |
|---|---|
| Small data, similar domain | freeze the backbone, train the head only |
| Small data, different domain | finetune the last blocks; consider a smaller model |
| Large data, similar domain | finetune everything |
| Large data, very different domain | finetune everything, or train from scratch |
| Almost no labels | CLIP zero-shot, or linear probe on DINOv2/CLIP features ⭐ |

---

## 6. Vision transformers ⭐⭐

```
image (224×224×3)
    │  split into 16×16 patches → 196 patches
    ▼
 linear projection of each flattened patch  → 196 tokens of dim d
    │  + [CLS] token, + positional embeddings
    ▼
 standard transformer encoder × L
    │
    ▼
 [CLS] representation → MLP head → classes
```

**ViT vs CNN ⭐⭐**

| | CNN | ViT |
|---|---|---|
| Inductive bias | locality + translation equivariance, built in | almost none — must be learned |
| Data appetite | works on modest datasets | needs huge data or strong augmentation/distillation (DeiT) ⚠️ |
| Receptive field | grows with depth | global from layer 1 |
| Scaling | saturates earlier | scales better with data and parameters |

Variants: **Swin** (windowed attention with shifted windows — reintroduces locality and gives a
hierarchical, detection-friendly backbone), **DeiT** (distillation makes ViT trainable on
ImageNet-1k alone), **ConvNeXt** (a CNN modernised to match ViTs, showing much of the gain came from
training recipes).

**Self-supervised vision:** SimCLR/MoCo (contrastive: augmented views of the same image attract,
others repel), BYOL/DINO (no negatives), MAE (mask 75% of patches and reconstruct — the vision
analogue of BERT). **CLIP** trains image and text encoders with a contrastive loss over 400M pairs,
giving zero-shot classification by comparing an image embedding against text prompts. ⭐⭐

---

## 7. Multimodal and generative ⭐

```
CLIP           : joint image–text embedding space; zero-shot classification, retrieval
BLIP-2 / LLaVA : a vision encoder feeding projected tokens into an LLM → visual QA, captioning
SAM            : promptable "segment anything" — a foundation model for masks
Diffusion       : learn to denoise; sample by iteratively removing predicted noise
                 (DDPM/DDIM, Stable Diffusion's latent diffusion, ControlNet for conditioning)
GANs           : generator vs discriminator minimax; sharp but unstable (mode collapse) ⚠️
VAEs           : encoder–decoder with a KL term; stable but blurry (reverse-KL mode-seeking)
```

**Why diffusion replaced GANs:** a stable, likelihood-like training objective (denoising
regression) instead of an adversarial minimax, far better mode coverage, and easy conditioning — at
the cost of many sampling steps (mitigated by DDIM, distillation and consistency models). ⭐

---

## Recall questions

1. What tensor layout does PyTorch use, and name two classic preprocessing bugs.
2. Give the task taxonomy with the head, loss and metric for each.
3. Define IoU, mAP@[.5:.95] and NMS, and state one NMS failure mode.
4. Dice vs IoU — why is Dice preferred in medical segmentation?
5. Name five augmentation families and three situations where augmentation is harmful.
6. Give a transfer-learning strategy for each of the four data/domain quadrants.
7. Describe the ViT pipeline from image to logits.
8. Why do ViTs need more data than CNNs, and what fixes that?
9. What does CLIP train, and how does zero-shot classification work?
10. Why did diffusion models displace GANs?
