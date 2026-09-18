
# Computer Vision — Flashcards

---

## Questions

1. PyTorch tensor layout for images; two classic preprocessing bugs.
2. When is HSV preferable to RGB?
3. Four classical CV operations that still matter, and where.
4. The task taxonomy with head, loss and metric for each.
5. IoU definition; mAP@[.5:.95].
6. NMS algorithm and one failure mode.
7. Dice vs IoU; why Dice for medical segmentation.
8. Focal loss: formula and the problem it solves.
9. Five augmentation families; three cases where augmentation harms.
10. Why must boxes/masks be transformed with the image?
11. Transfer-learning strategy per data/domain quadrant.
12. Output-size formula for a convolution.
13. Parameter and FLOP formulas.
14. Receptive field of `L` stacked `K×K` layers.
15. 1×1 convolution uses.
16. Depthwise separable cost ratio.
17. Why residual connections make deep CNNs trainable.
18. Why global average pooling mattered.
19. U-Net skip connections.
20. One-stage vs two-stage detection.
21. ViT pipeline; ViT vs CNN inductive bias.
22. Swin, DeiT, ConvNeXt — one line each.
23. SimCLR / MoCo / BYOL / MAE — what each does.
24. CLIP training objective and zero-shot classification.
25. Diffusion vs GAN vs VAE.
26. Deployment shift: symptoms and fixes.

---

## Answers

1. `(N, C, H, W)` channels-first. Bugs: BGR/RGB swap when mixing OpenCV and PyTorch; forgetting the
   pretraining mean/std normalisation.

2. When the signal is colour-based and lighting varies — hue is comparatively invariant to
   brightness changes.

3. Morphology for cleaning masks; contours/connected components for counting objects; homography +
   RANSAC for rectification and stitching; keypoints (SIFT/ORB) for matching and SLAM.

4. Classification (GAP+linear, CE, accuracy/macro-F1); detection (box + class heads, CE + GIoU,
   mAP with NMS); semantic segmentation (per-pixel logits, CE/Dice, mIoU); instance segmentation
   (mask head, + mask loss, mask AP); keypoints (heatmaps, MSE, PCK); retrieval (embeddings,
   triplet/contrastive, recall@k).

5. `IoU = |A∩B|/|A∪B|`; mAP@[.5:.95] averages AP over IoU thresholds 0.5–0.95 in 0.05 steps and over
   classes.

6. Sort by confidence, keep the top box, suppress all boxes with IoU above a threshold, repeat, per
   class. It suppresses genuinely overlapping objects; Soft-NMS or DETR avoid this.

7. `Dice = 2|A∩B|/(|A|+|B|)`, the F1 of the mask; always ≥ IoU, and it handles small foreground
   regions better than per-pixel cross-entropy.

8. `FL = −(1−p_t)^γ log p_t`; it down-weights easy, abundant background anchors so hard examples
   dominate the gradient in dense detection.

9. Geometric, photometric, occlusion (erasing/cutout), mixing (mixup/CutMix), policy
   (RandAugment). Harmful when it breaks label semantics (flipping digits/text, laterality in
   medical images), when colour is the label, or when applied to validation data.

10. Because the label is spatial — an untransformed box no longer matches the transformed image.

11. Small+similar: freeze, train the head. Small+different: finetune the last blocks. Large+similar:
    finetune all. Large+different: finetune all or train from scratch. Almost no labels: CLIP
    zero-shot or a linear probe on DINOv2 features.

12. `O = ⌊(W − K + 2P)/S⌋ + 1`.

13. `params = K²·C_in·C_out + C_out`; `FLOPs ≈ 2·K²·C_in·C_out·H_out·W_out`.

14. `1 + L(K−1)` for stride 1.

15. Change channel count cheaply, add non-linearity without changing spatial size, mix channels.

16. `1/C_out + 1/K²` — roughly 8–9× cheaper for `K=3` and large `C_out`.

17. `∂(x+F(x))/∂x = I + ∂F/∂x` gives a gradient path multiplied by 1, and a block can represent the
    identity by driving `F → 0`.

18. It removed the enormous fully-connected head (most of VGG's parameters), cutting parameters and
    overfitting.

19. High-resolution encoder detail concatenated into the decoder so boundaries are precise.

20. Two-stage proposes regions then classifies (Faster R-CNN — accurate); one-stage predicts
    directly over dense anchors (YOLO/SSD/RetinaNet — fast, needs focal loss).

21. Split into 16×16 patches, linearly project, add [CLS] and positional embeddings, run a
    transformer encoder, classify from [CLS]. ViTs have almost no spatial inductive bias, so they
    need far more data but scale better.

22. Swin: shifted-window attention giving hierarchy and locality. DeiT: distillation enabling
    ImageNet-1k-only training. ConvNeXt: a CNN modernised with transformer-era recipes, matching
    ViTs.

23. SimCLR/MoCo: contrastive learning with augmented views and negatives. BYOL: contrastive-style
    learning without negatives. MAE: mask most patches and reconstruct, the vision analogue of
    BERT.

24. A contrastive loss aligning image and text embeddings over 400M pairs; classify zero-shot by
    comparing the image embedding with text prompts of each class.

25. Diffusion: iterative denoising, stable training, excellent coverage, slow sampling. GAN:
    adversarial minimax, sharp but unstable and prone to mode collapse. VAE: encoder–decoder with a
    KL term, stable but blurry.

26. Symptoms: good offline, poor in the field. Causes: lighting, blur, resolution, compression,
    viewpoint. Fixes: collect field data, augment for deployment conditions, match preprocessing
    exactly, monitor input drift.
