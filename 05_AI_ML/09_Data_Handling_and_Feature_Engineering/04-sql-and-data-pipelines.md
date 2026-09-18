
# SQL and Data Pipelines for ML ⭐⭐

> **Core idea in 3 lines**
> 1. Most ML data work is SQL; window functions and correct joins are the difference between a
>    clean dataset and a leaked one.
> 2. Pipelines must be idempotent, backfillable and testable — data engineering discipline applied
>    to ML.
> 3. Interviewers use SQL to test precision of thinking; a wrong join is a wrong model.

(See also `02_Core_CS/04_DBMS` and `03_Languages/SQL` for the general SQL material.)

---

## 1. The SQL patterns that matter for ML ⭐⭐⭐

### Window functions — the most important tool

```sql
SELECT
  user_id,
  txn_ts,
  amount,
  -- rolling aggregate over a time window ENDING BEFORE the current row
  AVG(amount) OVER (
      PARTITION BY user_id ORDER BY txn_ts
      RANGE BETWEEN INTERVAL '30 days' PRECEDING AND INTERVAL '1 second' PRECEDING
  ) AS avg_amount_30d,
  COUNT(*) OVER (
      PARTITION BY user_id ORDER BY txn_ts
      ROWS BETWEEN 10 PRECEDING AND 1 PRECEDING
  ) AS txn_count_prev10,
  LAG(txn_ts) OVER (PARTITION BY user_id ORDER BY txn_ts) AS prev_txn_ts,
  ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY txn_ts DESC) AS recency_rank
FROM transactions;
```

⚠️⭐⭐⭐ The `AND ... 1 second PRECEDING` (or `1 PRECEDING`) is the point-in-time guard. The default
`ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` **includes the current row**, which leaks the
row's own value into its feature. Getting this right is exactly what a SQL-for-ML question tests.

### As-of join (point-in-time feature join)

```sql
-- for each label event, take the most recent feature snapshot STRICTLY BEFORE it
SELECT l.entity_id, l.label_ts, l.label, f.*
FROM labels l
LEFT JOIN LATERAL (
    SELECT *
    FROM features f
    WHERE f.entity_id = l.entity_id
      AND f.valid_from < l.label_ts          -- strictly before ⭐
    ORDER BY f.valid_from DESC
    LIMIT 1
) f ON TRUE;
```

### Deduplication, keeping the latest version

```sql
SELECT * FROM (
  SELECT *, ROW_NUMBER() OVER (PARTITION BY entity_id ORDER BY updated_at DESC) rn
  FROM raw
) t WHERE rn = 1;
```

### Building a labelled training table

```sql
WITH cohort AS (                       -- who is eligible, as of the prediction date
  SELECT user_id, DATE '2026-06-01' AS asof FROM users WHERE signup_dt < DATE '2026-06-01'
),
feat AS (                              -- features from BEFORE asof
  SELECT c.user_id, c.asof,
         COUNT(*) FILTER (WHERE e.ts >= c.asof - INTERVAL '30 days') AS events_30d,
         MAX(e.ts) AS last_event_ts
  FROM cohort c LEFT JOIN events e
    ON e.user_id = c.user_id AND e.ts < c.asof          -- ⭐ the cutoff
  GROUP BY 1, 2
),
lab AS (                               -- label from AFTER asof
  SELECT c.user_id,
         MAX(CASE WHEN s.cancel_ts BETWEEN c.asof AND c.asof + INTERVAL '30 days'
                  THEN 1 ELSE 0 END) AS churned
  FROM cohort c LEFT JOIN subscriptions s USING (user_id)
  GROUP BY 1
)
SELECT f.*, l.churned FROM feat f JOIN lab l USING (user_id);
```

⭐ Being able to write this three-CTE shape — cohort, features before, label after — is a very
strong signal in a data-heavy interview.

### Other patterns worth fluency

```sql
-- stratified sample
SELECT * FROM (SELECT *, ROW_NUMBER() OVER (PARTITION BY label ORDER BY random()) rn FROM t)
WHERE rn <= 10000;

-- histogram / binning
SELECT WIDTH_BUCKET(amount, 0, 10000, 20) AS bucket, COUNT(*) FROM t GROUP BY 1 ORDER BY 1;

-- pivot with conditional aggregation
SELECT user_id,
       SUM(CASE WHEN category='food'   THEN amount ELSE 0 END) AS food_spend,
       SUM(CASE WHEN category='travel' THEN amount ELSE 0 END) AS travel_spend
FROM txns GROUP BY 1;

-- sessionisation: a new session when the gap exceeds 30 minutes
SELECT *, SUM(new_session) OVER (PARTITION BY user_id ORDER BY ts) AS session_id
FROM (SELECT *, CASE WHEN ts - LAG(ts) OVER (PARTITION BY user_id ORDER BY ts)
                          > INTERVAL '30 minutes' THEN 1 ELSE 0 END AS new_session
      FROM events) t;
```

⚠️ **Join traps:** an inner join silently drops rows (changing your cohort); a one-to-many join
duplicates rows and inflates aggregates — always check `COUNT(*)` before and after a join. `NULL`
never equals `NULL`, so join keys with nulls vanish. ⭐

---

## 2. Pipeline design ⭐⭐

```
 sources ─► ingest (batch/CDC/stream) ─► raw/bronze ─► clean/silver ─► features/gold ─► training
                                             │                │              │
                                      schema checks      quality tests   feature registry
```

**Properties a pipeline must have:**

```
Idempotent   : re-running a day's job produces the same result (no double counting) ⭐
Backfillable : you can recompute history when the logic changes
Partitioned  : by date, so you process and reprocess incrementally
Tested       : schema, ranges, null rates, row-count anomalies, referential integrity
Observable   : freshness, volume and quality metrics with alerts
Versioned    : both code and data (Delta/Iceberg time travel, DVC)
```

**Batch vs streaming**

| | Batch | Streaming |
|---|---|---|
| Latency | minutes–hours | seconds |
| Complexity | low | high (state, ordering, late events) |
| Tools | Spark, dbt, Airflow | Kafka, Flink, Spark Structured Streaming |
| Use for ML | training data, batch scoring | real-time counters (velocity features) ⭐ |

⚠️ **Lambda architecture pain:** implementing the same feature twice — once in batch for training,
once in streaming for serving — is a leading cause of training–serving skew. Prefer one definition
executed by one engine, or a feature store that guarantees parity.

⚠️ **Late-arriving and out-of-order events** are the hard part of streaming: use event time (not
processing time), watermarks, and accept that very late events may be dropped or require a
correction job.

---

## 3. Formats and storage ⭐

```
CSV      : human-readable, no types, slow, huge          — avoid for anything large ⚠️
Parquet  : columnar, compressed, typed, predicate pushdown — the default for ML ⭐
Arrow    : in-memory columnar, zero-copy between tools
Delta / Iceberg / Hudi : ACID transactions, schema evolution, time travel on a data lake ⭐
TFRecord / WebDataset  : sequential formats for large-scale training I/O
```

⭐ Columnar + partitioning is why a well-laid-out Parquet dataset reads 100× faster than CSV for a
query touching five of two hundred columns.

---

## 4. Scale: when pandas stops working ⭐

```
< 1 GB      pandas
1–50 GB     pandas with dtype optimisation (category, float32), chunking; or Polars/DuckDB ⭐
            — DuckDB runs SQL over Parquet files far faster than pandas, in-process
50 GB–TB    Spark / Dask / Ray; or push the aggregation into the warehouse ⭐
> TB        distributed warehouse + sampling; only materialise the features you need
```

⭐ The best answer to "the data does not fit in memory" is usually **"do the aggregation in SQL in
the warehouse and bring back only the feature table"**, not "get a bigger machine".

Memory tricks worth naming: downcast numeric dtypes, use `category` for low-cardinality strings,
read only the needed columns from Parquet, process in chunks, and use generators rather than lists.

---

## Recall questions

1. Write a rolling 30-day average that does not include the current row, and explain the guard.
2. Write an as-of join and say why `<` rather than `<=` matters.
3. Write the three-CTE training-table pattern.
4. Sessionise an event stream with a 30-minute gap rule.
5. Name three join traps and how you detect them.
6. What does idempotent mean for a daily pipeline, and why does it matter?
7. Why does the lambda architecture cause training–serving skew?
8. Event time vs processing time; what is a watermark?
9. Why Parquet over CSV, and what does partitioning buy?
10. The dataset is 80 GB. Give three options in order of preference.
