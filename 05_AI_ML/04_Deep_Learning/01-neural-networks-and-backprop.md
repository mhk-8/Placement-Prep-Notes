
# Neural Networks and Backpropagation ⭐⭐⭐

> **Core idea in 3 lines**
> 1. A neural network is a composition of affine maps and non-linearities; without the
>    non-linearity the whole stack collapses to a single linear map.
> 2. Backpropagation is the chain rule applied in reverse topological order, reusing shared
>    sub-expressions — that reuse is why it costs the same as one forward pass.
> 3. Being able to derive the four backprop equations and write a two-layer network from scratch
>    is the single highest-yield deep-learning interview skill.

---

## 1. The forward pass

For layers `l = 1 … L`:

```
z^{(l)} = W^{(l)} a^{(l−1)} + b^{(l)}        pre-activation
a^{(l)} = g( z^{(l)} )                        activation
a^{(0)} = x ,   ŷ = a^{(L)}
```

Shapes (with a batch of `B` rows, row-major as in PyTorch): `X` is `(B, d_in)`,
`W` is `(d_out, d_in)`, `Z = X Wᵀ + b` is `(B, d_out)`.

⚠️ **Shape debugging is half of practical deep learning.** Write the shape next to every line.

### 📐 Why the non-linearity is essential

Two linear layers: `W₂(W₁x + b₁) + b₂ = (W₂W₁)x + (W₂b₁ + b₂) = W'x + b'`.
A product of matrices is a matrix, so **any depth of purely linear layers is exactly one linear
layer**. All the representational power comes from `g`. ∎ ⭐⭐⭐

### Universal approximation

A feed-forward network with one hidden layer and a non-polynomial activation can approximate any
continuous function on a compact set to arbitrary accuracy — given **enough width**.

⚠️ What it does *not* say: how many units (possibly exponential), that training will find those
weights, or that the result generalises. Depth is what makes the required width manageable: some
functions need exponentially many units at depth 2 but polynomially many at depth `k`. This nuance
is what interviewers are listening for. ⭐

---

## 2. 📐 Backpropagation — the derivation

Define the error signal at layer `l`:

```
δ^{(l)} = ∂L / ∂z^{(l)}
```

**(BP1) Output layer.** By the chain rule through `a^{(L)} = g(z^{(L)})`:

```
δ^{(L)} = ∇_a L ⊙ g'(z^{(L)})
```

For softmax + cross-entropy (or sigmoid + BCE) this collapses to the clean form derived in the
maths folder:

```
δ^{(L)} = ŷ − y            ⭐⭐⭐
```

**(BP2) Recursion.** `z^{(l+1)} = W^{(l+1)} g(z^{(l)}) + b^{(l+1)}`, so

```
∂z^{(l+1)}_k / ∂z^{(l)}_j = W^{(l+1)}_{kj} · g'(z^{(l)}_j)
```

and summing over the `k` outputs that `z_j` feeds:

```
δ^{(l)} = ( W^{(l+1)ᵀ} δ^{(l+1)} ) ⊙ g'( z^{(l)} )
```

**(BP3), (BP4) Parameter gradients.** Since `z^{(l)} = W^{(l)}a^{(l−1)} + b^{(l)}`:

```
∂L/∂W^{(l)} = δ^{(l)} (a^{(l−1)})ᵀ
∂L/∂b^{(l)} = δ^{(l)}
```

∎ These four equations are the whole algorithm. Memorise them; they are asked verbatim.

```
FORWARD  ────────────────────────────────────────────────►
  x ──[W₁,b₁]──► z₁ ──g──► a₁ ──[W₂,b₂]──► z₂ ──g──► a₂ ──► L
                 │          │               │        │
  ◄──────────────┴──────────┴───────────────┴────────┴───── BACKWARD
        δ₁ = (W₂ᵀδ₂)⊙g'(z₁)              δ₂ = ∇_a L ⊙ g'(z₂)
        ∂L/∂W₁ = δ₁ a₀ᵀ                  ∂L/∂W₂ = δ₂ a₁ᵀ
```

**Cost.** One backward pass costs about 2× a forward pass, so ~3× forward in total, and it needs
the stored activations — which is why memory, not compute, usually limits batch size. ⭐
(Gradient checkpointing trades compute for memory by recomputing activations.)

---

## 3. Why not numerical gradients?

```
finite difference:  ∂L/∂θᵢ ≈ (L(θ + εeᵢ) − L(θ − εeᵢ)) / 2ε
```

Needs **two forward passes per parameter** — `O(P)` forwards for `P` parameters, versus `O(1)` for
backprop. It is only used for **gradient checking** a hand-written layer (with `ε ≈ 1e−5` and
double precision, comparing relative error `< 1e−7`).

---

## 4. Weight initialisation ⭐⭐⭐

**Why not zeros:** every unit in a layer computes the same thing and receives the same gradient, so
they stay identical forever — the **symmetry-breaking** problem. Zero init works only for biases.

**Why not large random values:** activations saturate (sigmoid/tanh) or explode.

📐 **Xavier / Glorot.** To keep the variance of activations constant across layers with a
symmetric activation, we want `Var(z) = Var(x)`. With `z = Σ_{i=1}^{n_in} wᵢxᵢ` and independence,

```
Var(z) = n_in · Var(w) · Var(x)      ⇒   Var(w) = 1/n_in
```

Doing the same for the backward pass gives `Var(w) = 1/n_out`; Glorot averages them:

```
Var(w) = 2/(n_in + n_out)      →  U(−√(6/(n_in+n_out)), +√(6/(n_in+n_out)))
```

📐 **He / Kaiming (for ReLU).** ReLU zeroes half the inputs, so it halves the variance:
`Var(ReLU(z)) ≈ ½Var(z)`. Compensate with a factor of 2:

```
Var(w) = 2/n_in          ← the default for any ReLU network ⭐⭐⭐
```

| Activation | Init |
|---|---|
| tanh / sigmoid | Xavier/Glorot |
| ReLU / LeakyReLU / GELU | He/Kaiming |
| SELU | LeCun normal (`1/n_in`) |
| Transformers | usually `N(0, 0.02)` plus residual-depth scaling (e.g. `1/√(2L)`) |

---

## 5. A network from scratch (write this fluently)

```python
import numpy as np

def relu(z):  return np.maximum(0, z)
def drelu(z): return (z > 0).astype(float)

def softmax(z):
    z = z - z.max(axis=1, keepdims=True)        # log-sum-exp stability
    e = np.exp(z)
    return e / e.sum(axis=1, keepdims=True)

class TwoLayerNet:
    def __init__(self, d_in, d_hid, d_out, seed=0):
        rng = np.random.default_rng(seed)
        self.W1 = rng.normal(0, np.sqrt(2 / d_in), (d_in, d_hid))   # He init
        self.b1 = np.zeros(d_hid)
        self.W2 = rng.normal(0, np.sqrt(2 / d_hid), (d_hid, d_out))
        self.b2 = np.zeros(d_out)

    def forward(self, X):
        self.X  = X
        self.Z1 = X @ self.W1 + self.b1
        self.A1 = relu(self.Z1)
        self.Z2 = self.A1 @ self.W2 + self.b2
        self.P  = softmax(self.Z2)
        return self.P

    def loss(self, Y):                 # Y one-hot, shape (B, d_out)
        B = Y.shape[0]
        return -np.sum(Y * np.log(self.P + 1e-12)) / B

    def backward(self, Y, lr=0.1):
        B = Y.shape[0]
        dZ2 = (self.P - Y) / B                      # softmax + CE  ⭐
        dW2 = self.A1.T @ dZ2
        db2 = dZ2.sum(axis=0)
        dA1 = dZ2 @ self.W2.T
        dZ1 = dA1 * drelu(self.Z1)                  # BP2
        dW1 = self.X.T @ dZ1
        db1 = dZ1.sum(axis=0)
        for p, g in ((self.W1, dW1), (self.b1, db1),
                     (self.W2, dW2), (self.b2, db2)):
            p -= lr * g
```

⚠️ Details that get marked: log-sum-exp in the softmax, dividing by the batch size exactly once,
He init, and `dZ2 = P − Y` rather than differentiating softmax and cross-entropy separately.

---

## 6. Autodiff: how frameworks do it ⭐

```
reverse mode (backprop):  cost ≈ O(1) forward passes for ONE scalar output w.r.t. ALL inputs
                          → ideal for ML (millions of parameters, one loss)
forward mode:             cost ∝ number of INPUTS, gives all outputs w.r.t. one input
                          → ideal for few inputs, many outputs (e.g. Jacobian-vector products)
```

A framework builds a DAG of operations, each node storing a local `backward` rule, then walks it in
reverse topological order accumulating gradients. PyTorch builds this graph **dynamically** each
forward pass (define-by-run); TF1 built it statically. `torch.compile`/XLA reintroduce static graph
optimisation (kernel fusion) on top of dynamic semantics.

⚠️ Common PyTorch traps: forgetting `optimizer.zero_grad()` (gradients accumulate by default);
calling `.backward()` twice without `retain_graph`; doing evaluation without `torch.no_grad()`
(wastes memory building a graph); forgetting `model.eval()` so dropout and BN stay in training mode.

---

## 7. Computational graph worked by hand (interviewers do ask)

`f = (a + b)·c`, at `a=2, b=3, c=4`:

```
        a=2 ──┐
              ├──(+)── s=5 ──┐
        b=3 ──┘              ├──(×)── f=20
                       c=4 ──┘

backward with  ∂f/∂f = 1:
  ∂f/∂s = c = 4        ∂f/∂c = s = 5
  ∂f/∂a = ∂f/∂s · 1 = 4
  ∂f/∂b = ∂f/∂s · 1 = 4
```

The `+` node **copies** the gradient to both parents; the `×` node sends each parent the other's
value; a node used twice **sums** the gradients from both paths (multivariable chain rule).

---

## Recall questions

1. Prove that stacking linear layers without an activation gains nothing.
2. Write BP1–BP4 from memory and derive BP2.
3. What is `δ` at the output for softmax + cross-entropy, and why is it so simple?
4. Why is backprop `O(1)` forward passes rather than `O(P)`?
5. Why can't you initialise all weights to zero? Can biases be zero?
6. Derive He initialisation, including why the factor is 2 for ReLU.
7. What does the universal approximation theorem say — and what does it not say?
8. In a computation graph, what do the `+` and `×` nodes do on the backward pass?
9. Why does memory, not compute, limit batch size, and what is gradient checkpointing?
10. Name four common PyTorch training bugs.
