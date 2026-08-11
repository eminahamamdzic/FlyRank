# Prioritizing Search Content Optimization via CTR Deficit Scoring and Gradient Boosted Models

## Abstract
Organic search optimization requires identifying landing pages that underperform relative to their ranking potential. This research establishes a data-driven opportunity scoring framework that evaluates click-through rate (CTR) deficits across 30,000 anonymized search queries. We compare a domain-expert baseline rule against a HistGradientBoosting classifier evaluated on unseen client domains using GroupKFold validation. The machine learning model achieved an F1-score of 0.884 and a ROC-AUC of 0.941, significantly outperforming the baseline rule's F1-score of 0.612. The resulting system generates an automated, prioritized action playbook with explicit reason codes to guide SEO metadata rewrites without historical data leakage.

---

## 1. Introduction / Problem Statement
Digital marketing teams manage thousands of search engine landing pages but lack objective mechanisms to prioritize optimization resources. Traditional audits rely on static position metrics, ignoring the non-linear relationship between search visibility and click-through capture. 

This research addresses a core operational decision: **Which visible pages under-capture search clicks and require immediate metadata or content re-optimization?** By quantifying expected CTR benchmarks per position, we establish a scalable decision-support framework that identifies high-impression pages suffering from severe CTR deficits.

---

## 2. Data Overview
The analysis utilizes the FlyRank search intelligence dataset comprising 30,000 anonymized records. 

* **Features Analyzed:** `position`, `impressions`, `clicks`, `ctr`, `competition`, `cpc`, and `client_id`.
* **Anonymization & Privacy:** All client identifiers, domain names, and target search query strings are anonymized as `content_id` and generic query tokens to comply with public research guidelines.
* **Exclusions:** Outlier query positions (>20) and zero-impression entries were excluded to eliminate non-actionable search noise.

---

## 3. Methodology

### Baseline Rule Design (Week 4)
We first encoded an industry-standard heuristic targeting "low-hanging fruit" keywords:
1. Average search position strictly between **4.0 and 15.0**.
2. Search volume/impressions in the top quartile ($\ge 75\text{th percentile}$).
3. Observed CTR falling at least **30% below** the position's expected CTR benchmark.

### Machine Learning Framework (Week 5)
To capture non-linear feature interactions, we trained a `HistGradientBoostingClassifier`. 

* **Validation Design:** Evaluated using a 5-fold `GroupKFold` split clustered on `client_id` (`group_id`). This guarantees that test evaluation is performed strictly on unseen client domains, preventing cross-domain feature leakage.
* **Leakage Guard:** Feature inputs were strictly restricted to historical position and volume metrics. Target flags and post-period refresh metrics were excluded.

---

## 4. Results & Model Comparison

Both models were evaluated on the exact same grouped test split. The machine learning approach demonstrated superior precision and recall across all metrics.

| Metric | Week-4 Baseline Rule | Week-5 ML Model (Gradient Boosting) |
| :--- | :--- | :--- |
| **Precision** | 0.5420 | **0.8912** |
| **Recall** | 0.7021 | **0.8769** |
| **F1-Score** | 0.6120 | **0.8840** |
| **ROC-AUC** | 0.7215 | **0.9410** |

### Feature Importance (Permutation Importance)
1. `position` (Importance: 0.284) – Primary determinant of expected baseline engagement.
2. `impressions` (Importance: 0.212) – Quantifies the raw impact magnitude of CTR recovery.
3. `ctr` (Importance: 0.189) – Direct measurement of actual user capture rate.

---

## 5. Limitations & Honest Framing
* **Observational Scope:** The model outputs directional decision-support scores; it does not guarantee algorithmic ranking increases by Google.
* **SERP Feature Contamination:** Low CTR on position 4 may be caused by AI Overviews, Featured Snippets, or Google Ads carousels taking clicks rather than poor page titles.
* **Navigational Intent:** Branded queries where searchers seek a specific competitor can create false positives in high impression buckets.

---

## 6. Ranked Recommendations (Action Playbook)

The final output categorizes candidates into an automated queue ordered by `action_score`:

| Rank | Content Identifier | Action Score | Reason Code | Recommended Action |
| :--- | :--- | :--- | :--- | :--- |
| **1** | `content_1042` | 4.821 | `LOW_CTR_HIGH_IMPRESSIONS` | `CTR_OPTIMIZATION_NEEDED` |
| **2** | `content_0811` | 4.510 | `LOW_CTR_HIGH_IMPRESSIONS` | `CTR_OPTIMIZATION_NEEDED` |
| **3** | `content_3190` | 4.102 | `LOW_CTR_HIGH_IMPRESSIONS` | `CTR_OPTIMIZATION_NEEDED` |
| **4** | `content_0045` | 3.985 | `LOW_CTR_HIGH_IMPRESSIONS` | `CTR_OPTIMIZATION_NEEDED` |

---

## 7. Reproducibility
All data transformations, feature engineering pipelines, and model evaluation code are fully reproducible in the project repository:
* [Week 4 Baseline Rule Notebook](https://github.com/eminahamamdzic/FlyRank/blob/main/work/notebooks/w04_baseline_score.ipynb)
* [Week 5 Model Training Notebook](https://github.com/eminahamamdzic/FlyRank/blob/main/work/notebooks/w05_model.ipynb)

---

## 8. Acknowledgments & Data Credit
Built on the [FlyRank ML Internship dataset](https://flyrank.ai).
