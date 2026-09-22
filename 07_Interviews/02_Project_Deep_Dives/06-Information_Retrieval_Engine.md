
# Project: Information Retrieval Engine (Vector Space Model)

> **Course:** CS6370 Natural Language Processing · **Instructor:** Prof. Sutanu Chakraborti
> **Apr 2026** · **Track:** ML / NLP / Search · **Priority: ⭐⭐⭐ for search, IR and RAG roles**

**Why this project is better than it looks:** on the surface it is classical IR, which sounds dated
in 2026. Two things make it genuinely strong. First, you benchmarked **six systems** and validated
the difference with a **Wilcoxon signed-rank test with an effect size** — statistical rigour that
almost no candidate brings to a course project. Second, everything in it is the **retrieval half of
RAG**, which is the most commonly asked ML system design topic right now.

⭐ **The reframe to use:** *"this is the retrieval stage of a RAG pipeline, before dense embeddings
existed."* That single sentence makes a classical project current.

---

## 1. The 20-second version

> "I built a TF-IDF vector space retrieval system on Cranfield and benchmarked six retrieval
> methods against it, improving MAP@10 by 22.8% with tuned BM25 and validating the difference with
> a Wilcoxon signed-rank test."

## 2. The 60-second version

> "The Cranfield collection is the classic IR benchmark — 1,400 aerodynamics abstracts and 225
> queries with relevance judgements. I built a TF-IDF vector space model with an inverted index and
> cosine-similarity ranking as the baseline, which reached MAP@10 of 0.2691.
>
> Then I diagnosed why it failed. The core problem with lexical retrieval is **vocabulary
> mismatch** — I found a case where a genuinely relevant document ranked 109th purely because it
> shared no surface terms with the query. So I implemented five alternatives to attack that from
> different angles: BM25 and BM25 with pseudo-relevance feedback for better term weighting, LSA for
> latent semantic matching, WordNet query expansion for synonymy, and two ESA variants.
>
> Tuned BM25 won at 0.3304 MAP@10, a 22.8% relative improvement. The part I'm most pleased with is
> that I didn't stop at the number — across 225 queries a Wilcoxon signed-rank test gave
> p = 3.3 × 10⁻¹⁶ with Cohen's d of 0.57, so the improvement is both significant and a moderate
> effect size, not noise."

---

## 3. The technical content ⭐⭐

### TF-IDF and the vector space model
```
tf(t,d)   = term frequency, often log-normalised as 1 + log(count)
idf(t)    = log( N / df(t) )          — rarer terms carry more information
tfidf     = tf · idf

Similarity = cosine(q, d) = (q · d) / (‖q‖ ‖d‖)
```
⭐ **Why cosine and not Euclidean:** cosine is length-normalised, so a long document is not
penalised for being long. (Note the connection: on *normalised* vectors, ranking by cosine and by
Euclidean distance is identical, since `‖u−v‖² = 2(1 − cos θ)` — which is why vector databases
normalise and use inner product.)

### The inverted index
```
term → posting list of (doc_id, tf, [positions])

Why: you only score documents that contain at least one query term, instead of all N.
Optimisations worth naming: skip pointers, compression (delta + variable-byte),
                            WAND / top-k early termination.
```

### BM25 — know the formula and what each part does ⭐⭐⭐
```
                                    tf · (k₁ + 1)
BM25(q,d) = Σ_{t∈q}  IDF(t) · ───────────────────────────────────
                               tf + k₁·(1 − b + b·(|d| / avgdl))

  k₁ ≈ 1.2-2.0 : TERM FREQUENCY SATURATION. A term appearing 20 times is not 20× more
                 relevant than once. TF-IDF is linear in tf; BM25 saturates. ⭐
  b  ≈ 0.75    : LENGTH NORMALISATION strength. b=1 fully normalises by document length,
                 b=0 not at all.
  IDF(t) = log( (N − df + 0.5) / (df + 0.5) + 1 )   — a smoothed variant
```
⭐ **The one-line reason BM25 beats TF-IDF:** *saturation and principled length normalisation.*
That is the whole answer, and it is what you tuned.

### The other systems
```
BM25 + PRF  : Pseudo-Relevance Feedback — assume the top-k results are relevant, extract their
              terms, expand the query, re-run. ⚠️ Fails badly via QUERY DRIFT when the initial
              top-k are wrong. ⭐ Knowing the failure mode matters more than knowing the method.

LSA         : Truncated SVD of the term-document matrix. Documents and queries are projected into
              a k-dimensional latent space where synonyms collapse together, attacking vocabulary
              mismatch directly. ⭐ This is literally PCA on text — connect it to
              ../../05_AI_ML/01_Math_Foundations/04-pca-derivation.md and to Eckart-Young.

WordNet expansion : add synonyms of query terms from a lexical database.
                    ⚠️ Adds noise through polysemy — "bank" pulls in both river and finance.

ESA (Explicit Semantic Analysis) : represent text as a vector over Wikipedia concepts rather
              than latent dimensions. More interpretable than LSA, heavier to compute.
```

### The metrics ⭐⭐
```
Precision@k, Recall@k
MAP    : mean over queries of Average Precision — AP averages precision at each relevant
         document's rank, so it rewards putting relevant documents EARLY
nDCG@k : DCG = Σ rel_i / log₂(i+1), normalised by the ideal ordering.
         Handles GRADED relevance and discounts lower positions. ⭐ the modern standard
MRR    : mean of 1/(rank of the first relevant result) — right when the user wants one answer
```

---

## 4. The statistical validation — your differentiator ⭐⭐⭐

Most candidates report "my method got a better number". You tested whether the difference is real.

```
WILCOXON SIGNED-RANK TEST
   A PAIRED, NON-PARAMETRIC test. Paired because the same 225 queries are run through both
   systems; non-parametric because per-query AP scores are not normally distributed —
   they are bounded in [0,1] and heavily skewed. ⭐ A paired t-test would assume normality
   that does not hold.

   Result: p = 3.3 × 10⁻¹⁶  — the improvement is essentially certainly not chance.

COHEN'S d = 0.57
   The EFFECT SIZE. With 225 queries, even a trivial difference can reach significance,
   so p alone is not enough. d ≈ 0.2 small, 0.5 medium, 0.8 large → 0.57 is a moderate,
   practically meaningful effect. ⭐⭐
```

⭐ **Say this out loud in the interview:** *"p tells you the effect is real; d tells you whether
it's worth anything. With 225 samples you need both."* That sentence alone separates you from
almost every other candidate discussing a benchmark.

---

## 5. The reframe for modern roles ⭐⭐⭐

Have this ready for any RAG, search, or LLM-infrastructure interview:

> "Everything in this project is the retrieval half of a RAG pipeline. BM25 is still the sparse
> half of production hybrid retrieval — it beats dense embeddings on exact identifiers, product
> codes and rare terms, which is exactly where embeddings are weakest. LSA is the ancestor of dense
> embeddings: both project text into a lower-dimensional semantic space; LSA does it with a
> truncated SVD, modern systems do it with a trained bi-encoder. And the vocabulary-mismatch
> failure I diagnosed is precisely the problem dense retrieval was invented to solve.
>
> If I rebuilt it today: dense embeddings plus BM25 fused with reciprocal rank fusion, then a
> cross-encoder reranker over the top 100. The evaluation methodology — MAP, nDCG, and the
> significance testing — would stay exactly the same, because that part doesn't age."

⭐ That last sentence is the strongest thing you can say about this project.

---

## 6. Anticipated follow-ups ⭐⭐

<details><summary>"Why did you use a non-parametric test?"</summary>

Per-query average precision is bounded in [0,1], heavily skewed, and often has mass at 0 — it is
not normally distributed, so the t-test's assumption fails. Wilcoxon signed-rank only assumes the
differences are symmetric about the median, and it is paired, which exploits the fact that the same
queries are run through both systems and removes the between-query variance. That pairing is what
gives it power.
</details>

<details><summary>"BM25 vs dense embeddings — which is better?"</summary>

Neither alone. BM25 wins on exact matches, rare terms, identifiers and out-of-domain data; dense
embeddings win on paraphrase, synonymy and intent. Production systems use both and fuse the
rankings, usually with reciprocal rank fusion `Σ 1/(k + rank_i)`, then rerank with a cross-encoder.
⭐ The bi-encoder / cross-encoder distinction is the key architectural point: bi-encoders are
indexable and fast; cross-encoders score the pair jointly and are far more accurate but `O(N)` per
query — hence retrieve-then-rerank.
</details>

<details><summary>"What exactly is vocabulary mismatch, and what is your example?"</summary>

The query and a relevant document express the same concept in different words, so lexical overlap
is zero and the document scores zero regardless of relevance. **Your concrete case: a relevant
document ranked 109th on zero lexical overlap.** ⭐ Use that specific example — a diagnosed failure
case is far more convincing than the abstract definition.
</details>

<details><summary>"Why did BM25 beat LSA?"</summary>

Honest answer: Cranfield is small (1,400 documents) and highly domain-specific. LSA needs enough
data to estimate a meaningful latent space, and with a narrow technical vocabulary there is limited
synonymy to exploit. BM25's gains come from better term weighting, which works at any scale. On a
larger, more lexically diverse corpus the balance would likely shift.
</details>

<details><summary>"What is MAP, precisely?"</summary>

For one query, Average Precision is the mean of the precision values computed at each rank where a
relevant document appears. MAP is that averaged over queries. The key property: it rewards ranking
relevant documents *early*, which plain precision@k does not.
</details>

<details><summary>"How would you scale this to millions of documents?"</summary>

The inverted index already gives sublinear scoring. Beyond that: index compression (delta encoding
plus variable-byte), skip pointers, WAND or block-max WAND for top-k early termination, and
sharding with a scatter-gather merge. For the dense side, an ANN index — HNSW for accuracy, IVF-PQ
when memory is the constraint.
</details>

---

## 7. Limitations to state proactively

```
- Cranfield is tiny (1,400 docs) and single-domain; conclusions do not transfer to web-scale
  or to lexically diverse corpora.
- No neural retrieval at all — no dense bi-encoder baseline, which is the obvious gap in 2026.
- Hyperparameters were cross-validated, but on 225 queries the tuning itself has variance.
- No efficiency measurement — everything was measured on quality, nothing on latency or
  index size, which is half of what matters in a real search system. ⭐
```

---

## 8. The 30-second refresh

```
□ Cranfield: 1,400 docs, 225 queries, relevance judgements
□ TF-IDF + inverted index + cosine → MAP@10 = 0.2691 baseline
□ Diagnosed failure: vocabulary mismatch (relevant doc at rank 109, zero overlap)
□ Six systems: VSM, BM25, BM25+PRF, LSA, WordNet expansion, 2× ESA
□ BM25 wins because of TF SATURATION (k₁) and LENGTH NORMALISATION (b) → 0.3304 (+22.8%)
□ Wilcoxon signed-rank (paired, non-parametric, AP is not normal): p = 3.3e-16
□ Cohen's d = 0.57 — moderate effect; p says real, d says worth it ⭐
□ The reframe: this is the retrieval half of RAG; BM25 is still the sparse half of hybrid search
```
