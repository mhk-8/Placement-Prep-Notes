
# Deep Learning — Flashcards

---

## Questions

**Networks and backprop**
1. Why does a stack of linear layers collapse?
2. Write BP1–BP4.
3. `δ` at the output for softmax + cross-entropy.
4. Why is backprop cheap compared with finite differences?
5. Why not initialise weights to zero?
6. Xavier vs He — the variance and the reason for each.
7. What does the universal approximation theorem claim and not claim?
8. In a computational graph, what do `+` and `×` do on the backward pass, and a node used twice?
9. What limits batch size, and what does gradient checkpointing trade?
10. Reverse-mode vs forward-mode autodiff.

**Activations and gradients**
11. Max of the sigmoid derivative; consequence over 10 layers.
12. Four reasons ReLU won.
13. Dying ReLU: cause and three fixes.
14. When do you use softmax, sigmoid, K sigmoids, linear?
15. Derive the exponential vanishing/exploding behaviour.
16. Symptoms of vanishing vs exploding gradients.
17. Five fixes for the gradient problem.
18. One-line proof that residuals fix vanishing gradients.
19. BatchNorm vs LayerNorm.
20. What BN does at inference; the bug from forgetting `eval()`.
21. Modern explanation of why BN helps.
22. GroupNorm, InstanceNorm, RMSNorm — one use each.
23. Pre-LN vs post-LN.
24. Clip by norm vs by value.

**Training**
25. A correct training loop, and five bugs a careless one contains.
26. When AdamW, when SGD+momentum?
27. Bytes per parameter for Adam mixed-precision training; six ways to reduce it.
28. LR range test.
29. Cosine with warmup — why each part.
30. Linear scaling rule; gradient accumulation and the `/ACC` detail.
31. fp16 vs bf16; loss scaling.
32. Data vs tensor vs pipeline parallelism.
33. Why random search beats grid search.
34. The debugging protocol, in order.
35. Expected loss at initialisation for `K` balanced classes.

**CNNs**
36. Output-size formula, including dilation.
37. Parameter and FLOP formulas for a conv layer.
38. Receptive field of `L` stacked `K×K` layers; the VGG argument.
39. Local connectivity, weight sharing, equivariance — what each buys.
40. Equivariance vs invariance; which component gives which.
41. Three uses of a 1×1 convolution.
42. Depthwise separable cost ratio.
43. ResNet block and why it works.
44. Why global average pooling mattered.
45. U-Net skip connections.
46. One-stage vs two-stage detection; what NMS and mAP are.
47. Transfer-learning recipe for a small dataset.

**Sequences**
48. Vanilla RNN equations; what weight sharing over time buys.
49. Why BPTT is worse than feed-forward depth.
50. Six LSTM equations.
51. `∂c_t/∂c_{t−1}` and its significance; the forget-bias trick.
52. LSTM vs GRU.
53. The seq2seq bottleneck and attention's fix.
54. Teacher forcing and exposure bias.
55. RNN vs transformer: compute, path length, parallelism, memory.
56. Why linear-time sequence models are returning.

---

## Answers

1. `W₂(W₁x+b₁)+b₂ = W'x+b'` — a product of matrices is a matrix.

2. `δ^L = ∇_a L ⊙ g'(z^L)`; `δ^l = (W^{l+1ᵀ}δ^{l+1}) ⊙ g'(z^l)`; `∂L/∂W^l = δ^l a^{l−1ᵀ}`;
   `∂L/∂b^l = δ^l`.

3. `ŷ − y`.

4. Reverse mode computes the gradient with respect to *all* parameters in one backward sweep
   (~2× a forward pass); finite differences need two forward passes per parameter.

5. All units in a layer stay identical — symmetry is never broken. Biases may be zero.

6. Xavier `Var(w) = 2/(n_in+n_out)` keeps variance stable for symmetric activations; He
   `Var(w) = 2/n_in` compensates for ReLU discarding half the signal.

7. A one-hidden-layer net with a non-polynomial activation can approximate any continuous function
   on a compact set given enough width. It says nothing about how many units, whether training
   finds them, or generalisation.

8. `+` copies the incoming gradient to both parents; `×` sends each parent the other's value times
   the incoming gradient; a node used in two paths sums its incoming gradients.

9. Stored activations for the backward pass; checkpointing recomputes them, saving roughly `√L`
   memory for about 30% extra compute.

10. Reverse mode: cost independent of the number of parameters, proportional to the number of
    outputs — right for one scalar loss. Forward mode: cost proportional to the number of inputs.

11. `0.25`; `0.25¹⁰ ≈ 10⁻⁶` — the early layers receive essentially no signal.

12. Derivative exactly 1 on the positive side; no exponentials; sparse activations; no saturation
    for `z > 0`.

13. A unit whose pre-activation is negative for all inputs has zero gradient forever; caused by a
    too-high LR or large negative bias; fix with LeakyReLU/ELU/GELU, lower LR, He init, BN.

14. Softmax for mutually exclusive multiclass output; sigmoid for binary; `K` sigmoids for
    multi-label; linear for regression.

15. `δ^l = [Π W^{kᵀ}D^{k−1}] δ^L`, a product of `L−l` terms, so the norm behaves like `γ^{L−l}`.

16. Vanishing: loss plateaus, early-layer gradient norms ~1e−7, early weights unchanged.
    Exploding: loss spikes then NaN, gradient norms 1e3+.

17. ReLU-family activations; He/Xavier init; batch/layer norm; residual connections; gradient
    clipping.

18. `∂(x + F(x))/∂x = I + ∂F/∂x` — an identity path multiplied by 1.

19. BN normalises each channel over the batch (batch-size dependent, different at inference); LN
    normalises each sample over its features (batch-independent, identical at inference).

20. Running mean/variance. Without `eval()`, inference uses batch statistics, so predictions depend
    on which other samples are in the batch.

21. It smooths the loss landscape and permits higher learning rates; the "internal covariate shift"
    explanation has been largely discredited.

22. GroupNorm: batch-independent normalisation for detection/diffusion with small batches.
    InstanceNorm: style transfer. RMSNorm: cheaper LayerNorm without mean subtraction, used in most
    modern LLMs.

23. Pre-LN (`x + Attn(LN(x))`) trains stably with little warmup and is the modern default; post-LN
    can reach slightly better quality but needs careful warmup.

24. By norm — it rescales the whole gradient, preserving direction. Clipping by value distorts the
    direction.

25. Loop: `zero_grad → forward → loss → backward → clip → step → scheduler`, with `model.train()`
    and a `no_grad` eval. Bugs: missing `zero_grad`, missing `eval()`, eval without `no_grad`,
    probabilities passed to a logits loss, scheduler stepped at the wrong granularity.

26. AdamW for transformers/NLP/sparse features and when tuning budget is limited; SGD+momentum for
    CNNs when you can tune and want the best final accuracy.

27. ~16 bytes/parameter (fp32 master weights, gradients, Adam `m` and `v`). Reduce with bf16,
    8-bit/Adafactor optimisers, ZeRO/FSDP, gradient checkpointing, LoRA, CPU offload.

28. Sweep the LR exponentially over a few hundred steps and plot loss vs LR; pick about an order of
    magnitude below the divergence point.

29. Warmup stabilises Adam's early moment estimates and large initial gradients; the cosine decay
    anneals to a flat minimum.

30. Batch ×`k` ⇒ LR ×`k` with warmup. Accumulation runs `ACC` micro-batches before stepping, and
    the loss must be divided by `ACC`.

31. fp16 has a narrow exponent range so gradients underflow — loss scaling multiplies the loss
    before backward and unscales after. bf16 keeps fp32's range, so no scaling is needed.

32. Data: replicate the model, split the batch, all-reduce gradients. Tensor: split individual
    weight matrices across devices. Pipeline: split layers across devices with micro-batching.

33. Most hyperparameters barely matter; random sampling explores the few that do at a finer
    resolution for the same budget.

34. Overfit 10 examples → check shapes/labels → check loss at init → LR range test → per-layer
    gradient norms and dead units → remove regularisation, then add it back → scale up.

35. `ln K`.

36. `O = ⌊(W − K + 2P)/S⌋ + 1`; with dilation replace `K` by `d(K−1)+1`.

37. `params = K²·C_in·C_out + C_out`; `FLOPs ≈ 2·K²·C_in·C_out·H_out·W_out`.

38. `RF = 1 + L(K−1)`; three 3×3 layers match a 7×7 receptive field with `27C²` vs `49C²`
    parameters and two extra non-linearities.

39. Local connectivity cuts parameters and matches image structure; weight sharing makes the
    parameter count independent of image size and reduces overfitting; together they give
    translation equivariance.

40. Convolution is equivariant (shifting the input shifts the output); pooling and global average
    pooling turn that into approximate invariance. It is not rotation- or scale-invariant.

41. Change the channel count cheaply (bottleneck), add non-linearity without changing spatial size,
    and mix channel information.

42. `1/C_out + 1/K²` — about 8–9× cheaper for `K=3`, `C_out=256`.

43. `y = ReLU(x + BN(conv(ReLU(BN(conv(x))))))`; the identity path keeps the gradient alive and lets
    a block represent the identity.

44. It replaced the huge fully-connected head (which held most of VGG's parameters), cutting
    parameters and overfitting.

45. High-resolution spatial detail from the encoder that pooling destroyed, restored to the
    decoder.

46. Two-stage proposes regions then classifies (Faster R-CNN, more accurate); one-stage predicts
    directly (YOLO/SSD/RetinaNet, faster). NMS removes duplicate overlapping boxes; mAP averages
    precision over recall levels and IoU thresholds.

47. Pretrained backbone, standard augmentation, normalise with the pretraining statistics, train
    the head first, then unfreeze with a much lower (discriminative) LR, AdamW with cosine and
    warmup, monitor per-class metrics.

48. `h_t = tanh(W_hh h_{t−1} + W_xh x_t + b)`; sharing `W` across time makes the parameter count
    independent of length and lets a pattern generalise across positions.

49. The same matrix `W_hh` is multiplied at every step, so there is no cancellation between
    different layers' Jacobians — the decay or growth is a clean power law.

50. `f, i, c̃, c, o, h` as in the topic file: three sigmoid gates, a tanh candidate, an additive cell
    update, and `h_t = o_t ⊙ tanh(c_t)`.

51. `f_t`; with gates near 1 the product `Πf_k` stays near 1, giving a gradient highway.
    Initialising the forget bias to +1 starts the gate open.

52. LSTM: 3 gates and a separate cell state, more parameters. GRU: 2 gates, no separate cell state,
    faster, often equal; prefer GRU with less data or compute.

53. A single fixed context vector must encode the whole source; attention lets the decoder attend
    over all encoder states at each step, removing the bottleneck.

54. Feeding the ground-truth previous token during training; at inference the model consumes its
    own outputs, a distribution it never saw — exposure bias.

55. RNN `O(nd²)` compute, `O(n)` path, sequential, `O(1)` inference state. Transformer `O(n²d)`
    compute, `O(1)` path, fully parallel, `O(n)` KV cache.

56. Attention is quadratic in sequence length; state-space models (S4, Mamba), RWKV and linear
    attention give near-linear cost for very long contexts.
