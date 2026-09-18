
# The ML Lifecycle and Deployment ⭐⭐

> **Core idea in 3 lines**
> 1. Training a model is a small fraction of an ML system; the rest is data, serving, monitoring
>    and the loop that connects them.
> 2. Deployment choices (batch vs online vs streaming) follow from latency and freshness
>    requirements, not from preference.
> 3. The questions that separate candidates are about *failure*: what breaks in production and how
>    you notice.

---

## 1. The lifecycle

```
 ┌──────────────┐
 │ 1. Problem   │  business metric → ML metric; is ML even needed? what is the baseline?
 └──────┬───────┘
        ▼
 ┌──────────────┐
 │ 2. Data      │  collection, labelling, validation, splits, feature store
 └──────┬───────┘
        ▼
 ┌──────────────┐
 │ 3. Modelling │  baseline → iterate → offline evaluation → error analysis
 └──────┬───────┘
        ▼
 ┌──────────────┐
 │ 4. Deploy    │  package, serve, shadow → canary → ramp
 └──────┬───────┘
        ▼
 ┌──────────────┐
 │ 5. Monitor   │  quality, drift, latency, cost, fairness
 └──────┬───────┘
        │
        └────────► retrain / rollback ──► back to 2
```

⭐ The line worth saying: *"the model is maybe 10% of the system; data pipelines, serving and
monitoring are the other 90%."* ("Hidden Technical Debt in Machine Learning Systems", Sculley et
al., is the canonical reference.)

**Frame the problem properly.** Before any modelling: what business metric improves, what is the
cost of each error type, what is the current baseline (often a rule), what latency and throughput
are required, and what would make this *not* an ML problem.

---

## 2. Deployment patterns ⭐⭐⭐

| Pattern | Latency | When | Example |
|---|---|---|---|
| **Batch / offline** | hours–days | predictions can be precomputed | nightly churn scores, weekly recommendations |
| **Online / real-time** | 10–500 ms | the input is only known at request time | fraud checks, search ranking, ads |
| **Streaming** | seconds | continuous events | anomaly detection on telemetry |
| **Edge / on-device** | ms | privacy, offline use, cost | phone camera, keyboard prediction |

**Batch is underrated.** If predictions can be precomputed and looked up, you avoid a serving
system entirely, get trivial scaling and simple debugging. Reach for online serving only when the
features genuinely are not known in advance. ⭐

**Release strategies ⭐⭐**

```
Shadow mode   : run the new model on live traffic, log predictions, serve the OLD one.
                Zero user risk; verifies the pipeline and latency.  ⭐ always do this first
Canary        : route 1% → 5% → 25% → 100%, watching guardrails at each step
A/B test      : split traffic, measure the business metric with statistics (see the stats folder)
Blue–green    : two full environments, switch over, roll back instantly
Multi-armed bandit : shift traffic to the winner during the experiment
```

Always have a **rollback plan** and a **kill switch** to a previous model, a simple rule, or a
default. "What is your rollback plan?" is a standard follow-up. ⭐

---

## 3. Serving architecture

```
 client ──► API gateway ──► feature fetch ──► model service ──► response
                │                │                 │
             auth, rate       online store      batching,
             limiting         (Redis) +         GPU/CPU pool,
                              request features  model registry
                                                    │
                                              prediction log ──► monitoring / training data
```

Key choices:

- **Model format:** a pickled sklearn object is fine internally, but prefer ONNX or TorchScript for
  portability and speed; TensorRT for GPU inference; GGUF/llama.cpp for CPU LLMs.
- **Server:** FastAPI for simple cases; TorchServe/Triton/KServe for scale; vLLM/TGI for LLMs.
- **Batching:** dynamic batching trades a few ms of latency for large throughput gains on GPUs.
- **Scaling:** horizontal replicas behind a load balancer; autoscale on queue depth, not CPU.
- **Caching:** cache predictions for repeated inputs; cache features aggressively.

⚠️ **Latency budget thinking:** a 100 ms budget might be 10 ms network + 30 ms feature fetch +
40 ms inference + 20 ms overhead. Model inference is often *not* the bottleneck — feature retrieval
is. Saying this shows production experience. ⭐⭐

---

## 4. Training–serving skew ⭐⭐⭐

The most common production ML bug: the features at serving time differ from those at training time.

| Cause | Fix |
|---|---|
| Feature logic reimplemented in two languages/codebases | **one** shared transformation library, or a feature store |
| Training on batch-aggregated data, serving on real-time data | compute training features from the same pipeline that serves them |
| Time-travel: training features include data that would not exist at prediction time | point-in-time-correct joins ⚠️ |
| Different preprocessing (scaler fit on all data, different tokeniser version) | package preprocessing *inside* the model artefact |
| Missing-value handling differs | explicit, versioned imputation rules |

**Detection:** log the serving features and periodically re-score a sample offline; the predictions
must match. A "skew test" in CI that runs the same row through both paths is the strongest answer.
⭐⭐

---

## 5. Feature stores ⭐

```
                 ┌──────────────────────┐
  raw data ────► │ transformation (one   │ ──► offline store (warehouse) ──► training
                 │ definition, one code) │      point-in-time correct joins
                 └──────────┬───────────┘
                            └──────────► online store (Redis/DynamoDB) ──► serving
```

They exist to solve exactly three problems: feature reuse across teams, training–serving
consistency, and point-in-time correctness. ⚠️ They add real operational complexity — a small team
with one model does not need one, and saying so is good judgement.

---

## 6. Retraining ⭐

| Trigger | Notes |
|---|---|
| Scheduled (daily/weekly/monthly) | simple, predictable; may retrain unnecessarily |
| Performance-based | retrain when a monitored metric crosses a threshold; needs labels |
| Drift-based | retrain when input distributions shift; works without labels ⭐ |
| Data-volume-based | retrain after N new labelled examples |

Always: retrain **automatically**, validate against the current production model on a fixed
evaluation set, and promote only if it wins (a "champion–challenger" gate). Never auto-deploy a
model that has not beaten the incumbent. ⭐

⚠️ **Feedback loops** — the model's own predictions influence the data it later trains on. A
recommender that shows only what it already scores highly never learns about the rest of the
catalogue; a fraud model that blocks transactions never sees their outcomes. Mitigations:
exploration (ε-greedy, bandits), holdout traffic served by a different policy, and propensity
weighting. This is a favourite senior-level question. ⭐⭐⭐

---

## 7. Infrastructure vocabulary you should hold

```
Containers      Docker; pin versions, including CUDA
Orchestration   Kubernetes; KServe/Seldon for model serving
Pipelines       Airflow, Dagster, Prefect, Kubeflow, Metaflow
Tracking        MLflow, Weights & Biases — experiments, params, metrics, artefacts
Registry        versioned models with stage tags (staging/production) and lineage
Data versioning DVC, LakeFS, Delta Lake / Iceberg time travel
CI/CD for ML    test data schemas, run the training pipeline, evaluate, gate on metrics,
                skew test, deploy; "CT" = continuous training
IaC             Terraform
```

⚠️ **Reproducibility needs four things pinned:** code (git SHA), data (version/snapshot),
configuration (hyperparameters), and environment (image digest, library and CUDA versions). Seeds
alone are not enough.

---

## 8. Cost ⭐

```
training : GPU-hours × instance price; use spot instances with checkpointing,
           mixed precision, and stop unpromising runs early (ASHA)
serving  : usually the dominant long-run cost — right-size instances, autoscale to zero
           for spiky traffic, quantise, distil, cache, batch
data     : storage and egress; lifecycle policies on logs
labelling: often the largest line item — use active learning, weak supervision,
           and pre-labelling with a large model then human review ⭐
```

---

## Recall questions

1. Draw the ML lifecycle and say what fraction of it is modelling.
2. Compare batch, online, streaming and edge deployment with a use case each.
3. Why deploy in shadow mode first, and what comes after?
4. Give five causes of training–serving skew and how you would detect it in CI.
5. What three problems does a feature store solve, and when is it overkill?
6. Name four retraining triggers and the promotion gate you would use.
7. What is a feedback loop, give an example, and name two mitigations.
8. What four things must be pinned for reproducibility?
9. Where does the latency budget actually go in a typical online system?
10. What is your rollback plan?
