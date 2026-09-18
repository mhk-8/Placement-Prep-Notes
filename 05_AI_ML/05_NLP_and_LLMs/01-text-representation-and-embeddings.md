
# Text Representation and Embeddings ⭐⭐

> **Core idea in 3 lines**
> 1. Everything in NLP starts with turning text into vectors; the history of the field is the
>    history of better vectors.
> 2. Count-based representations (BoW, TF-IDF) are sparse, high-dimensional and ignore meaning;
>    learned embeddings are dense and encode similarity.
> 3. Static embeddings give one vector per word; contextual embeddings give one per occurrence —
>    that single distinction is the jump from word2vec to BERT.

---

## 1. Preprocessing

```
raw text → normalise (lowercase, unicode, strip accents) → tokenise → (stopwords, stemming/
lemmatisation) → numericalise → vectors
```

| Step | Notes |
|---|---|
| Tokenisation | word / subword / character (see §5) |
| Stopword removal | helps BoW/TF-IDF; **harmful for transformers** — "not" matters ⚠️ |
| Stemming | crude suffix chopping (`studies → studi`); fast, Porter stemmer |
| Lemmatisation | dictionary-based, POS-aware (`studies → study`); slower, correct |
| Lowercasing | loses named-entity signal; cased models often do better on NER |

⚠️ Modern practice: with a pretrained transformer you do **almost none** of this. Use the model's
own tokeniser and feed near-raw text. Classical preprocessing belongs to the TF-IDF era.

---

## 2. Count-based representations

**Bag of Words:** a vector of word counts; order is discarded entirely.

**TF-IDF ⭐⭐** — down-weights words that appear everywhere:

```
tf(t, d)   = count of t in d  (often normalised, or log(1 + count))
idf(t)     = log( N / (1 + df(t)) ) + 1          df = number of documents containing t
tfidf      = tf · idf
```

The intuition to state: a term is informative for a document if it is frequent **here** and rare
**elsewhere**. "the" has a high `tf` but an `idf` near zero, so it contributes nothing.

*Worked example.* `N = 1000` documents; "neural" appears in 10 of them and 5 times in this
document of 100 words.

```
tf  = 5/100 = 0.05
idf = log(1000/11) + 1 ≈ 4.51 + 1 = 5.51      (natural log)
tfidf ≈ 0.28
```

**n-grams** restore a little order (bigrams capture "not good"), at the cost of an exploding
vocabulary.

| ✅ | ⚠️ |
|---|---|
| simple, fast, interpretable | huge sparse vectors (vocabulary-sized) |
| a strong baseline — TF-IDF + linear SVM is still competitive on topic classification ⭐ | no notion of similarity: "car" and "automobile" are orthogonal |
| no training needed | no word order or syntax; out-of-vocabulary words are simply lost |

---

## 3. Static word embeddings ⭐⭐⭐

**The distributional hypothesis:** *"You shall know a word by the company it keeps"* (Firth). Words
appearing in similar contexts have similar meanings — this is the premise of every embedding
method.

### word2vec

Two training objectives:

```
CBOW:      predict the centre word from its context    (faster, better for frequent words)
Skip-gram: predict the context from the centre word    (better for rare words, small corpora) ⭐
```

```
 skip-gram:      context      centre      context
                  w_{t−2} w_{t−1} [ w_t ] w_{t+1} w_{t+2}
                      ▲       ▲      │       ▲       ▲
                      └───────┴──────┴───────┴───────┘
                        maximise  Σ log p(w_context | w_t)

 p(o | c) = exp(u_oᵀ v_c) / Σ_{w∈V} exp(u_wᵀ v_c)     ← softmax over the WHOLE vocabulary ⚠️
```

⚠️ That denominator costs `O(|V|)` per example with `|V| ≈ 10⁶`. Two fixes:

- **Negative sampling ⭐⭐** — replace the softmax with `k` binary logistic problems:

```
log σ(u_oᵀ v_c)  +  Σ_{j=1}^{k} E_{w_j ~ P_n} [ log σ(−u_{w_j}ᵀ v_c) ]
```

  i.e. push the true context pair's score up and `k` random pairs' scores down. `k ≈ 5–20` for
  small corpora, 2–5 for large ones. The noise distribution is `P_n(w) ∝ U(w)^{3/4}`, which
  up-samples rare words relative to their frequency.

- **Hierarchical softmax** — a Huffman tree over the vocabulary reduces the cost to `O(log|V|)`.

Also: **subsampling of frequent words**, discarding word `w` with probability `1 − √(t/f(w))`.

### GloVe

Factorises the global co-occurrence matrix instead of using local windows:

```
minimise  Σ_{i,j} f(X_ij) ( w_iᵀ w̃_j + b_i + b̃_j − log X_ij )²
```

Captures global corpus statistics directly; results are broadly comparable to word2vec.

### FastText ⭐

Represents a word as the sum of its character n-gram vectors. Consequences: it handles
**out-of-vocabulary** words (compose them from n-grams) and morphologically rich languages far
better. `running` shares n-grams with `run`, so the embeddings are related by construction.

### The famous property

```
vec("king") − vec("man") + vec("woman") ≈ vec("queen")
```

Linear analogies emerge because the objective encodes ratios of co-occurrence probabilities.
⚠️ Modern caution: the effect is weaker than the popular presentation suggests, and it depends on
excluding the query words from the nearest-neighbour search.

### Limitations of static embeddings ⚠️⭐⭐⭐

**One vector per word type**, so polysemy is impossible to represent: "bank" gets a single vector
averaging river-bank and money-bank. There is no word-order or sentence-level information, and the
embeddings inherit and amplify **social bias** from the corpus (the "man:computer_programmer ::
woman:homemaker" result). This limitation is exactly what contextual embeddings solve.

---

## 4. Contextual embeddings ⭐⭐⭐

ELMo (bi-LSTM) and then BERT (transformer) produce a **different vector for every occurrence** of a
word, computed from the whole sentence.

```
"I sat by the river bank."        bank → [0.2, −1.1, …]   (geography-ish region)
"I deposited cash at the bank."   bank → [1.7,  0.3, …]   (finance-ish region)
```

That is the single most important difference to state in an interview: **static = one vector per
word; contextual = one vector per token occurrence.** ⭐⭐⭐

### Getting a sentence embedding ⚠️

Do **not** use BERT's raw `[CLS]` token for similarity without finetuning — out of the box it
produces a poor metric space (all sentences are crowded into a narrow cone). Use:

- **Sentence-BERT / SimCSE** — a siamese network trained with a contrastive objective so cosine
  similarity is meaningful. ⭐
- Mean pooling over the token embeddings (better than raw `[CLS]`, still worse than SBERT).
- A dedicated embedding model (`text-embedding-*`, E5, BGE, GTE) for retrieval.

---

## 5. Tokenisation ⭐⭐⭐

| Level | Vocabulary | Problem |
|---|---|---|
| Word | 100k+ | OOV words; huge embedding table; poor morphology |
| Character | ~100 | very long sequences; no semantic units |
| **Subword** | 30k–100k | the right compromise — no OOV, reasonable length ⭐ |

### Byte-Pair Encoding (BPE)

```
1. start with characters (or bytes) as the vocabulary
2. count all adjacent symbol pairs in the corpus
3. merge the most frequent pair into a new symbol
4. repeat until the vocabulary reaches the target size
```

*Example.* From `low, lower, lowest, new, newest`: merge `e+s → es`, then `es+t → est`, then
`l+o → lo`, … Common words end up as single tokens and rare ones decompose into pieces.

**Variants:** WordPiece (BERT — merges the pair that most increases the likelihood, not the most
frequent), Unigram/SentencePiece (starts large and prunes, probabilistic), byte-level BPE (GPT —
operates on bytes, so **nothing is ever out of vocabulary**, including emoji and any language). ⭐

⚠️ **Tokenisation consequences interviewers ask about:**
- Rare words and names cost many tokens, so non-English text can cost 2–4× more tokens than
  English for the same content — directly affecting API cost and effective context length.
- Character-level tasks are hard for LLMs because they see tokens, not letters — this is the real
  reason models miscount the `r`s in "strawberry" or struggle with reversing strings. ⭐
- Numbers split inconsistently (`1234` may be one token or three), which harms arithmetic; newer
  tokenisers split digits individually to fix this.
- Rough rule of thumb: **1 token ≈ 4 characters ≈ 0.75 English words**.

---

## 6. Vector search — where embeddings get used ⭐⭐

```
documents → embedding model → vectors → index
query     → embedding model → vector  → nearest neighbours by cosine similarity
```

| Index | Idea | Trade-off |
|---|---|---|
| Flat (brute force) | compare with everything | exact, `O(N)` |
| IVF | cluster, then search a few clusters | fast, approximate |
| **HNSW** | navigable small-world graph | the usual default; fast and accurate, memory-hungry |
| PQ / OPQ | compress vectors to codes | huge memory savings, some accuracy loss |

Recall that on **normalised** vectors, ranking by cosine similarity and by Euclidean distance is
identical (`‖u−v‖² = 2(1 − cos θ)`) — which is why libraries normalise and use inner product.

**Hybrid search ⭐:** combine BM25 (a refined TF-IDF that is excellent at exact keyword and rare-term
matching) with dense embeddings (good at paraphrase and semantics), fusing the rankings with
reciprocal rank fusion. This is the standard production retrieval setup and a strong thing to
mention in an ML system design round.

---

## Recall questions

1. Write the TF-IDF formula and explain each term's purpose.
2. Skip-gram vs CBOW, and when each is preferred.
3. Why is the word2vec softmax intractable, and how does negative sampling fix it?
4. What does FastText add over word2vec?
5. State precisely the limitation of static embeddings that contextual models solve.
6. Why should you not use raw `[CLS]` for sentence similarity, and what should you use?
7. Describe BPE in four steps, and say what byte-level BPE guarantees.
8. Give three downstream consequences of tokenisation.
9. Why do cosine and Euclidean ranking agree on normalised vectors?
10. What is hybrid search, and why does it beat either method alone?
