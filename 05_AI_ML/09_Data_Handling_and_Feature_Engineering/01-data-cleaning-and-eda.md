
# Data Cleaning and EDA ⭐⭐

> **Core idea in 3 lines**
> 1. Model quality is capped by data quality; most real ML work is here.
> 2. Every cleaning decision is a modelling decision — imputation, outlier removal and encoding all
>    inject assumptions.
> 3. Every transformation must be *fitted on training data only* and applied to the rest, or you
>    have leaked.

---

## 1. EDA checklist ⭐⭐

```
□ shape, dtypes, memory;  head/tail/sample
□ target distribution — imbalance? skew? are there impossible values?
□ missingness per column and the PATTERN of missingness ⭐
□ per-feature: distribution, skew, outliers, cardinality, constant/near-constant columns
□ duplicates — exact and near-duplicate rows ⚠️
□ correlations with the target and between features (multicollinearity)
□ time: is there a temporal column? are there gaps, seasonality, regime changes?
□ groups: do rows repeat per entity (user, patient, device)? ⚠️ affects the split
□ sanity: negative ages, future dates, impossible categories, unit mixtures
□ segment sizes: will any important slice be too small to evaluate?
```

⭐ Always plot the target against time. Regime changes, data-collection changes and seasonality are
invisible in summary statistics and fatal if missed.

---

## 2. Missing data ⭐⭐⭐

**The mechanism matters more than the method:**

```
MCAR  Missing Completely At Random  — missingness independent of everything
      (a sensor randomly dropped a reading).  Dropping rows is unbiased, just wasteful.
MAR   Missing At Random — missingness depends on OBSERVED variables
      (income missing more often for younger respondents). Imputation using the other
      features is valid.
MNAR  Missing Not At Random — missingness depends on the UNOBSERVED value itself
      (high earners refuse to state income). ⚠️ No imputation fixes this; the fact of
      missingness is itself informative — add a MISSING INDICATOR feature ⭐
```

Being able to name these three and say what each implies is a standard interview question.

| Strategy | When | Caveat |
|---|---|---|
| Drop rows | few rows, MCAR | biases the sample if not MCAR |
| Drop the column | > 50–70% missing and low value | may discard the missingness signal |
| Mean/median imputation | quick baseline | shrinks variance, distorts correlations ⚠️ |
| Mode / "Unknown" category | categorical | "Unknown" is often genuinely informative ⭐ |
| Forward/backward fill | time series | never fill backwards from the future ⚠️ leakage |
| kNN / iterative (MICE) | MAR, moderate size | expensive; must be fit on train only |
| Model-native handling | XGBoost/LightGBM learn a default split direction ⭐ | simplest and often best |
| **+ missing indicator** | almost always worth adding | lets the model use missingness |

⚠️ **Imputation must be fitted inside the pipeline**, per CV fold. Computing the median over the
whole dataset before splitting leaks test information.

---

## 3. Outliers ⭐

**Detection**

```
z-score          |z| > 3            assumes normality ⚠️
IQR rule         outside [Q1 − 1.5·IQR, Q3 + 1.5·IQR]     robust, distribution-free ⭐
modified z-score uses the median and MAD — robust to the outliers themselves
Isolation Forest / LOF / DBSCAN     multivariate
domain rules     an age of 200, a negative price
```

**Treatment — and the judgement that matters ⭐⭐:**

```
Is it an ERROR (bad sensor, unit mix-up, data entry)?  → fix or remove
Is it a RARE BUT REAL event (a genuine large transaction)? → KEEP IT ⚠️
                                                              — in fraud, the outliers ARE the signal
Otherwise: winsorise/clip, log-transform, or use a robust model/loss (MAE, Huber, trees)
```

⚠️ Never delete outliers reflexively. Ask what generated them first. Tree models are largely
insensitive to them anyway.

---

## 4. Duplicates and consistency

```
exact duplicates      → drop (but check whether they are legitimate repeat events first ⚠️)
near-duplicates       → fuzzy matching, MinHash/LSH for text, perceptual hashes for images
                        ⚠️ near-duplicates ACROSS the train/test split inflate scores badly ⭐
inconsistent categories → "NY" / "New York" / "new york" → canonicalise
mixed units/currencies  → normalise; look for bimodal distributions as the tell
type problems           → numbers stored as strings, dates in mixed formats
```

---

## 5. Scaling and transformation ⭐⭐

| Transform | Formula | Use |
|---|---|---|
| Standardisation | `(x − μ)/σ` | the default; required by linear models, SVM, k-NN, k-means, PCA, neural nets |
| Min-max | `(x − min)/(max − min)` | bounded ranges, image pixels; sensitive to outliers ⚠️ |
| Robust scaling | `(x − median)/IQR` | outlier-heavy data |
| Log / log1p | `log(1+x)` | right-skewed positives (income, counts, prices) ⭐ |
| Box-Cox / Yeo-Johnson | learned power transform | to make a feature more normal (Yeo-Johnson allows ≤ 0) |
| Quantile / rank | map to a uniform or normal distribution | very robust, breaks monotone interpretability |

⭐ **Which models need scaling?**

```
NEED it     : linear/logistic regression (for regularisation and convergence), SVM,
              k-NN, k-means, PCA, neural networks
DON'T need  : decision trees, random forests, gradient boosting  ⚠️ classic exam question
```

---

## 6. Categorical encoding ⭐⭐⭐

| Encoding | Use | Caveat |
|---|---|---|
| **One-hot** | low cardinality (< ~15), linear models | explodes with cardinality; drop one column to avoid the dummy trap with an intercept ⚠️ |
| **Ordinal/label** | genuinely ordered categories; tree models | ⚠️ imposes a false order for linear models |
| **Target/mean encoding** | high cardinality, tree models ⭐ | ⚠️⚠️ **leaks** unless computed out-of-fold with smoothing — the single most common leakage bug |
| Frequency/count | high cardinality | loses identity |
| **Hashing** | very high cardinality, streaming | collisions; not invertible |
| **Embeddings** | high cardinality with a neural model or CatBoost | needs data |
| Binary/BaseN | middle ground | less interpretable |

📐 **Smoothed target encoding** (write this if asked):

```
enc(category) = (n_c · ȳ_c + m · ȳ_global) / (n_c + m)
```

where `n_c` is the category's count and `m` the smoothing strength — rare categories are pulled
towards the global mean. Compute it **out-of-fold** (or with CatBoost's ordered statistics) so a
row never contributes to its own encoding. ⭐⭐⭐

⚠️ **Unseen categories at inference** must have a defined behaviour: an "other" bucket, the global
mean, or a hash. Define it explicitly or serving will crash.

---

## 7. Imbalanced and skewed data

See `03_Classical_ML/07-metrics-and-evaluation.md` for the full ordering: metric → threshold →
class weights → resampling → reframing. ⚠️ Any resampling (SMOTE included) goes **inside** the CV
fold and on training data only.

---

## 8. A defensible preprocessing pipeline ⭐⭐⭐

```python
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder

num = Pipeline([("imp", SimpleImputer(strategy="median", add_indicator=True)),
                ("sc",  StandardScaler())])
cat = Pipeline([("imp", SimpleImputer(strategy="constant", fill_value="Unknown")),
                ("oh",  OneHotEncoder(handle_unknown="ignore", min_frequency=10))])

pre = ColumnTransformer([("num", num, num_cols), ("cat", cat, cat_cols)])
model = Pipeline([("pre", pre), ("clf", HistGradientBoostingClassifier())])
# every fit_transform now happens INSIDE each CV fold  ⭐
```

⚠️ `handle_unknown="ignore"` and `add_indicator=True` are exactly the details an interviewer looks
for.

---

## Recall questions

1. Give the EDA checklist from memory.
2. Define MCAR, MAR and MNAR, and say what each implies for imputation.
3. When should you add a missing indicator?
4. Give three outlier-detection methods and the judgement that decides treatment.
5. Why are near-duplicates across a split so damaging?
6. Which model families need feature scaling and which do not?
7. Write smoothed target encoding and explain why out-of-fold computation is mandatory.
8. What must happen when an unseen category arrives at inference?
9. Why must imputation live inside the pipeline?
10. Name two details in the pipeline above that prevent production failures.
