
# Computer Vision — Solved OA Questions

18 questions: arithmetic, metrics, and design judgement.

---

**Q1.** Input `128×128×3`, conv `3×3`, 32 filters, stride 2, padding 1. Output shape and
parameters?

<details><summary>Answer</summary>

`O = (128 − 3 + 2)/2 + 1 = 63 + 1 = 64` ⇒ `64×64×32`.
Params `= 3·3·3·32 + 32 = 864 + 32 = 896`.
</details>

**Q2.** Predicted box `(10,10,50,50)`, ground truth `(30,30,70,70)` as `(x1,y1,x2,y2)`. IoU?

<details><summary>Answer</summary>

Intersection `x: [30,50]`, `y: [30,50]` ⇒ `20 × 20 = 400`.
Areas `40×40 = 1600` each; union `= 1600 + 1600 − 400 = 2800`.
`IoU = 400/2800 ≈ 0.143`.
</details>

**Q3.** What does mAP@[.5:.95] average over?

<details><summary>Answer</summary>

Average precision computed at IoU thresholds from 0.50 to 0.95 in steps of 0.05, then averaged over
those ten thresholds (and over classes). The COCO standard.
</details>

**Q4.** Why is NMS needed, and how does it work?

<details><summary>Answer</summary>

Detectors emit many overlapping boxes for one object. NMS sorts by confidence, keeps the highest,
suppresses every box with IoU above a threshold, and repeats — per class. It fails when two real
objects overlap heavily; Soft-NMS or DETR avoid this.
</details>

**Q5.** Predicted mask 1000 px, ground truth 1200 px, overlap 800 px. IoU and Dice?

<details><summary>Answer</summary>

Union `= 1000 + 1200 − 800 = 1400` ⇒ `IoU = 800/1400 ≈ 0.571`.
`Dice = 2(800)/(1000+1200) = 1600/2200 ≈ 0.727`. Dice is always ≥ IoU.
</details>

**Q6.** You horizontally flip images to augment a digit classifier. Problem?

<details><summary>Answer</summary>

Digits are not flip-invariant — a flipped 2 is not a 2, and 6/9 confusion is introduced. Flipping
breaks the label semantics.
</details>

**Q7.** 2 000 labelled images of industrial defects. Train from scratch or finetune?

<details><summary>Answer</summary>

Finetune a pretrained backbone: freeze most of it, train the head, then unfreeze the last blocks at
a low LR. 2 000 images are far too few to train a modern CNN from scratch. Add heavy augmentation
and consider a linear probe on DINOv2/CLIP features as a baseline.
</details>

**Q8.** A detector has high precision and low recall. What do you change?

<details><summary>Answer</summary>

Lower the confidence threshold (and possibly raise the NMS IoU threshold). If that trades away too
much precision, the model itself needs work: more data for the missed classes, better anchors or
resolution, and focal loss for foreground/background imbalance.
</details>

**Q9.** Why does one-stage detection use focal loss?

<details><summary>Answer</summary>

Dense detectors evaluate tens of thousands of anchors, almost all background, so the easy negatives
swamp the loss. Focal loss `−(1−p_t)^γ log p_t` down-weights easy examples so hard ones dominate the
gradient.
</details>

**Q10.** Global average pooling vs flatten + fully-connected: parameters for a `7×7×2048` feature
map into 1000 classes?

<details><summary>Answer</summary>

GAP: `2048 → 1000` ⇒ `2 049 000`.
Flatten: `7·7·2048 = 100 352 → 1000` ⇒ `100 353 000`. About 50× more, and far more prone to
overfitting.
</details>

**Q11.** Receptive field of five stacked `3×3` stride-1 convolutions?

<details><summary>Answer</summary>

`1 + 5(3−1) = 11`.
</details>

**Q12.** Training accuracy 98%, validation 62%, with 3 000 images. First three actions?

<details><summary>Answer</summary>

Stronger augmentation (RandAugment, mixup/CutMix, random erasing); freeze more of the backbone and
reduce the head size; add weight decay, dropout and early stopping. Also check for duplicate or
near-duplicate images across the split.
</details>

**Q13.** What do U-Net skip connections carry?

<details><summary>Answer</summary>

High-resolution spatial detail from the encoder that downsampling destroyed, concatenated into the
decoder so boundaries can be localised precisely.
</details>

**Q14.** Why does a ViT need more data than a ResNet?

<details><summary>Answer</summary>

It lacks the built-in locality and translation-equivariance priors, so those must be learned from
data. Large-scale pretraining, strong augmentation or distillation (DeiT) compensate.
</details>

**Q15.** How does CLIP do zero-shot classification?

<details><summary>Answer</summary>

Embed the image with the image encoder, embed prompts such as "a photo of a {class}" with the text
encoder, and take the class whose text embedding has the highest cosine similarity — no training on
the target classes.
</details>

**Q16.** Depthwise separable convolution, `K=3`, `C_in=128`, `C_out=128`. Multiplication saving?

<details><summary>Answer</summary>

Standard `= 9·128·128 = 147 456` per position; separable `= 9·128 + 128·128 = 1 152 + 16 384 = 17 536`.
Ratio `≈ 0.119` — about 8.4× cheaper.
</details>

**Q17.** Your model is excellent offline but poor on phone-camera photos in the field. Diagnosis?

<details><summary>Answer</summary>

Distribution shift: training images differ in lighting, blur, resolution, compression and
viewpoint. Fixes: collect field data, augment for the deployment conditions (blur, JPEG artefacts,
low light), match the preprocessing pipeline exactly, and monitor drift in production.
</details>

**Q18.** Count dark circular blobs on a uniform white background. CNN or OpenCV?

<details><summary>Answer</summary>

OpenCV: threshold (Otsu), morphological cleanup, connected components or contour detection. It is
deterministic, needs no labels, runs in milliseconds and is easy to debug. Using a CNN here is
over-engineering.
</details>

---

## Scoring

| Correct | Read as |
|---|---|
| 16–18 | Ready for CV rounds |
| 12–15 | Redo the arithmetic and metric questions |
| 8–11 | Reread `01-cv-fundamentals.md` and the CNN file in `04_Deep_Learning` |
| < 8 | Two focused sessions here |
