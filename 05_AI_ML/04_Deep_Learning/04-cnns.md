
# Convolutional Neural Networks ⭐⭐

> **Core idea in 3 lines**
> 1. A convolution is a linear layer with two priors baked in: locality (each output sees a small
>    patch) and weight sharing (the same filter everywhere), which slashes the parameter count.
> 2. Those priors give translation equivariance, which is exactly right for images.
> 3. The interview questions are arithmetic (output size, parameter count, receptive field) plus
>    "why does ResNet work" — both fully answerable from first principles.

---

## 1. The convolution operation

```
output(i, j) = Σ_c Σ_u Σ_v  input(c, i·s + u, j·s + v) · kernel(c, u, v)  +  b
```

```
 input 5×5            kernel 3×3          output 3×3  (stride 1, no padding)
 ┌─────────────┐      ┌───────┐           ┌───────┐
 │ · · · · ·   │      │ w w w │           │ o o o │
 │ · ┌─────┐ · │  ⊛   │ w w w │    =      │ o o o │
 │ · │ 3×3 │ · │      │ w w w │           │ o o o │
 │ · └─────┘ · │      └───────┘           └───────┘
 │ · · · · ·   │      slides over every position,
 └─────────────┘      SAME weights everywhere  ← weight sharing
```

### 📐 Output size formula ⭐⭐⭐ (memorise)

```
O = floor( (W − K + 2P) / S ) + 1
```

`W` input size, `K` kernel, `P` padding, `S` stride.

- **"same" padding** with `S = 1` needs `P = (K−1)/2` — hence odd kernels (3, 5, 7) so padding is
  an integer and symmetric.
- With **dilation** `d`, the effective kernel is `K_eff = d(K−1) + 1`, so
  `O = floor((W − K_eff + 2P)/S) + 1`.

*Example.* `224×224` input, `K=7`, `S=2`, `P=3` (ResNet's stem):
`O = (224 − 7 + 6)/2 + 1 = 223/2 + 1 = 111 + 1 = 112`. ✅

### 📐 Parameter and FLOP counts ⭐⭐⭐

```
params  = (K · K · C_in) · C_out  +  C_out            (the +C_out is the bias)
FLOPs   ≈ 2 · K · K · C_in · C_out · H_out · W_out     (multiply-add counted as 2)
```

*Example.* `C_in=64`, `C_out=128`, `K=3` ⇒ `3·3·64·128 + 128 = 73 728 + 128 = 73 856` parameters.
A fully-connected layer between two `56×56×64` maps would need `(56·56·64)² ≈ 4×10¹⁰` — six orders
of magnitude more. That contrast is the answer to "why convolutions?" ⭐

### 📐 Receptive field

Stacking `L` layers of kernel `K` with stride 1:

```
RF = 1 + L·(K − 1)
```

So three 3×3 layers have the same `7×7` receptive field as one 7×7 layer, but use
`3·(9C²) = 27C²` parameters instead of `49C²`, with two extra non-linearities. **That is the VGG
argument**, and it is a standard question. ⭐⭐

With strides, the receptive field grows multiplicatively:
`RF_l = RF_{l−1} + (K_l − 1)·Π_{i<l} S_i`.

---

## 2. Why convolutions, precisely ⭐⭐⭐

| Property | Consequence |
|---|---|
| **Local connectivity** | each output depends on a small patch ⇒ far fewer parameters, matching the local structure of images |
| **Weight sharing** | the same feature detector is applied everywhere ⇒ parameters independent of image size, and far less overfitting |
| **Translation equivariance** | shifting the input shifts the feature map: `f(shift(x)) = shift(f(x))` |
| **Hierarchy** | stacked layers compose edges → textures → parts → objects |

⚠️ **Equivariance, not invariance.** Convolution is *equivariant* to translation; pooling (and
global average pooling at the end) is what converts that into approximate *invariance*. Convolution
is **not** invariant to rotation or scale — that is what augmentation is for. Getting this
distinction right is a strong signal. ⭐⭐

---

## 3. Pooling and the modern alternatives

```
Max pooling 2×2, stride 2   → halves H and W, keeps the strongest activation,
                              small translation invariance, no parameters
Average pooling             → smoother
Global average pooling      → (C,H,W) → (C,); replaces the giant FC head ⭐
Strided convolution         → learnable downsampling; modern nets often use this instead of pooling
```

GAP is a big deal historically: AlexNet/VGG spent most of their parameters on the final
fully-connected layers; replacing them with GAP (NiN, ResNet) removed ~90% of the parameters and
reduced overfitting.

---

## 4. Architecture evolution — what each one contributed ⭐⭐

| Model | Year | Contribution |
|---|---|---|
| LeNet-5 | 1998 | the conv–pool–FC template |
| **AlexNet** | 2012 | ReLU, dropout, GPU training, augmentation; won ImageNet by a huge margin |
| **VGG** | 2014 | uniform 3×3 stacks; depth matters; the "two 3×3 = one 5×5" argument |
| **Inception/GoogLeNet** | 2014 | parallel multi-scale branches; **1×1 convolutions** as cheap channel-mixing/bottleneck |
| **ResNet** | 2015 | residual connections ⇒ 152 layers trainable; the single most important idea ⭐⭐⭐ |
| DenseNet | 2016 | concatenate all previous feature maps; feature reuse |
| MobileNet | 2017 | **depthwise separable** convolutions for edge devices |
| SENet | 2017 | squeeze-and-excitation channel attention |
| EfficientNet | 2019 | compound scaling of depth/width/resolution |
| ConvNeXt | 2022 | a CNN modernised with transformer-era recipes, matching ViTs |
| ViT / Swin | 2020–21 | transformers for vision (see the NLP folder for attention) |

### 1×1 convolution ⭐⭐

It has no spatial extent, so it is a per-pixel linear map across channels. Uses: change the channel
count cheaply (bottlenecks), add a non-linearity without touching spatial resolution, and mix
channel information. In a ResNet bottleneck, `1×1 (reduce) → 3×3 → 1×1 (expand)` makes a 3×3
convolution on 256 channels affordable.

### 📐 Depthwise separable convolution (MobileNet)

```
standard    : K·K·C_in·C_out                multiplications per output position
depthwise   : K·K·C_in       (one filter per input channel, no channel mixing)
pointwise   : 1·1·C_in·C_out (1×1 conv mixes channels)
ratio = (K·K·C_in + C_in·C_out) / (K·K·C_in·C_out) = 1/C_out + 1/K²
```

For `K = 3`, `C_out = 256`: `≈ 1/256 + 1/9 ≈ 0.115` — about **8–9× cheaper**. ⭐

### ResNet block

```
      x ────────────────────┐ identity (or 1×1 conv if the shape changes)
      │                     │
  [3×3 conv → BN → ReLU]    │
      │                     │
  [3×3 conv → BN]           │
      │                     │
      +◄────────────────────┘
      │
    ReLU
```

Why it works: `∂y/∂x = I + ∂F/∂x` guarantees a gradient path multiplied by 1 (proof in
`02-activations-and-gradient-problems.md`), and a block can learn the identity by driving `F → 0`,
so extra depth cannot hurt. ⭐⭐⭐

---

## 5. Beyond classification ⭐

```
Detection:   two-stage (R-CNN → Fast → Faster R-CNN with a Region Proposal Network)
             one-stage (YOLO, SSD, RetinaNet with focal loss for foreground/background imbalance)
             anchor-free / set-based (DETR, uses a transformer and Hungarian matching)
             metric: mAP@IoU;  post-processing: non-max suppression (NMS)
Segmentation: semantic (U-Net's encoder–decoder with skip connections; FCN; DeepLab's atrous conv)
              instance (Mask R-CNN = Faster R-CNN + a mask head + RoIAlign)
              metric: IoU / Dice
Others:      pose estimation (heatmaps), super-resolution, style transfer, face verification
             (triplet/contrastive losses)
```

**U-Net's skip connections** carry high-resolution spatial detail from the encoder to the decoder,
which pooling destroyed — the standard answer for why it dominates medical imaging.

---

## 6. Practical recipe for a vision task ⭐⭐

```
1. Start from a pretrained backbone (ImageNet, or CLIP/DINOv2 features).
2. Augment: random resized crop, horizontal flip, colour jitter; then RandAugment,
   mixup/CutMix if you need more.  ⚠️ Do NOT flip when orientation carries meaning
   (digits, text, some medical views).
3. Normalise with the pretraining dataset's mean/std.
4. Freeze the backbone and train the head first; then unfreeze with a much lower LR
   (discriminative LRs: ~10× smaller for early layers).
5. AdamW or SGD+momentum, cosine schedule with warmup, label smoothing 0.1.
6. Track per-class metrics and a confusion matrix, not just top-1.
7. Test-time augmentation and EMA weights for a final free improvement.
```

⚠️ Transfer learning notes: with a small dataset, freeze more; with a large one, finetune
everything. If the target domain is very unlike ImageNet (satellite, ultrasound), earlier layers
transfer but later ones may not — consider finetuning from an earlier block.

---

## Recall questions

1. Give the output-size formula and apply it to `224`, `K=7`, `S=2`, `P=3`.
2. Count the parameters of a `3×3, 64→128` convolution, and compare with the fully-connected
   alternative.
3. Why does VGG use stacks of 3×3 instead of one 7×7?
4. Distinguish equivariance from invariance, and say which component provides which.
5. What are three uses of a 1×1 convolution?
6. Derive the cost ratio of a depthwise separable convolution.
7. Draw a ResNet block and explain in one line why it trains at 152 layers.
8. What do U-Net skip connections preserve?
9. Why did global average pooling matter historically?
10. Write a transfer-learning recipe for 5 000 labelled images.
