<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FlyRank Research Paper - CTR Deficit Scoring</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@exampledev/new.css@1.1.2/new.min.css">
    <style>
        body { max-width: 800px; margin: 40px auto; padding: 0 20px; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; }
        table { width: 100%; border-collapse: collapse; margin: 20px 0; }
        th, td { border: 1px solid #ddd; padding: 10px; text-align: left; }
        th { background-color: #f4f4f4; }
        code { background: #f4f4f4; padding: 2px 6px; border-radius: 4px; }
    </style>
</head>
<body>

<h1>Prioritizing Search Content Optimization via CTR Deficit Scoring and Gradient Boosted Models</h1>

<h2>Abstract</h2>
<p>Organic search optimization requires identifying landing pages that underperform relative to their ranking potential. This research establishes a data-driven opportunity scoring framework that evaluates click-through rate (CTR) deficits across 30,000 anonymized search queries. We compare a domain-expert baseline rule against a HistGradientBoosting classifier evaluated on unseen client domains using GroupKFold validation. The machine learning model achieved an F1-score of 0.884 and a ROC-AUC of 0.941, significantly outperforming the baseline rule's F1-score of 0.612. The resulting system generates an automated, prioritized action playbook with explicit reason codes to guide SEO metadata rewrites without historical data leakage.</p>

<hr>

<h2>1. Introduction / Problem Statement</h2>
<p>Digital marketing teams manage thousands of search engine landing pages but lack objective mechanisms to prioritize optimization resources. Traditional audits rely on static position metrics, ignoring the non-linear relationship between search visibility and click-through capture.</p>
<p>This research addresses a core operational decision: <strong>Which visible pages under-capture search clicks and require immediate metadata or content re-optimization?</strong> By quantifying expected CTR benchmarks per position, we establish a scalable decision-support framework that identifies high-impression pages suffering from severe CTR deficits.</p>

<h2>2. Data Overview</h2>
<p>The analysis utilizes the FlyRank search intelligence dataset comprising 30,000 anonymized records.</p>
<ul>
    <li><strong>Features Analyzed:</strong> <code>position</code>, <code>impressions</code>, <code>clicks</code>, <code>ctr</code>, <code>competition</code>, <code>cpc</code>, and <code>client_id</code>.</li>
    <li><strong>Anonymization & Privacy:</strong> All client identifiers, domain names, and target search query strings are anonymized as <code>content_id</code> and generic query tokens to comply with public research guidelines.</li>
    <li><strong>Exclusions:</strong> Outlier query positions (>20) and zero-impression entries were excluded to eliminate non-actionable search noise.</li>
</ul>

<h2>3. Methodology</h2>
<h3>Baseline Rule Design (Week 4)</h3>
<p>We first encoded an industry-standard heuristic targeting "low-hanging fruit" keywords:</p>
<ol>
    <li>Average search position strictly between <strong>4.0 and 15.0</strong>.</li>
    <li>Search volume/impressions in the top quartile (≥ 75th percentile).</li>
    <li>Observed CTR falling at least <strong>30% below</strong> the position's expected CTR benchmark.</li>
</ol>

<h3>Machine Learning Framework (Week 5)</h3>
<p>To capture non-linear feature interactions, we trained a <code>HistGradientBoostingClassifier</code>.</p>
<ul>
    <li><strong>Validation Design:</strong> Evaluated using a 5-fold <code>GroupKFold</code> split clustered on <code>client_id</code> (<code>group_id</code>). This guarantees that test evaluation is performed strictly on unseen client domains, preventing cross-domain feature leakage.</li>
    <li><strong>Leakage Guard:</strong> Feature inputs were strictly restricted to historical position and volume metrics. Target flags and post-period refresh metrics were excluded.</li>
</ul>

<h2>4. Results & Model Comparison</h2>
<p>Both models were evaluated on the exact same grouped test split. The machine learning approach demonstrated superior precision and recall across all metrics.</p>

<table>
    <thead>
        <tr>
            <th>Metric</th>
            <th>Week-4 Baseline Rule</th>
            <th>Week-5 ML Model (Gradient Boosting)</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><strong>Precision</strong></td>
            <td>0.5420</td>
            <td><strong>0.8912</strong></td>
        </tr>
        <tr>
            <td><strong>Recall</strong></td>
            <td>0.7021</td>
            <td><strong>0.8769</strong></td>
        </tr>
        <tr>
            <td><strong>F1-Score</strong></td>
            <td>0.6120</td>
            <td><strong>0.8840</strong></td>
        </tr>
        <tr>
            <td><strong>ROC-AUC</strong></td>
            <td>0.7215</td>
            <td><strong>0.9410</strong></td>
        </tr>
    </tbody>
</table>

<h2>5. Limitations & Honest Framing</h2>
<ul>
    <li><strong>Observational Scope:</strong> The model outputs directional decision-support scores; it does not guarantee algorithmic ranking increases by Google.</li>
    <li><strong>SERP Feature Contamination:</strong> Low CTR on position 4 may be caused by AI Overviews, Featured Snippets, or Google Ads carousels taking clicks rather than poor page titles.</li>
    <li><strong>Navigational Intent:</strong> Branded queries where searchers seek a specific competitor can create false positives in high impression buckets.</li>
</ul>

<h2>6. Ranked Recommendations (Action Playbook)</h2>
<table>
    <thead>
        <tr>
            <th>Rank</th>
            <th>Content Identifier</th>
            <th>Action Score</th>
            <th>Reason Code</th>
            <th>Recommended Action</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><strong>1</strong></td>
            <td><code>content_1042</code></td>
            <td>4.821</td>
            <td><code>LOW_CTR_HIGH_IMPRESSIONS</code></td>
            <td><code>CTR_OPTIMIZATION_NEEDED</code></td>
        </tr>
        <tr>
            <td><strong>2</strong></td>
            <td><code>content_0811</code></td>
            <td>4.510</td>
            <td><code>LOW_CTR_HIGH_IMPRESSIONS</code></td>
            <td><code>CTR_OPTIMIZATION_NEEDED</code></td>
        </tr>
        <tr>
            <td><strong>3</strong></td>
            <td><code>content_3190</code></td>
            <td>4.102</td>
            <td><code>LOW_CTR_HIGH_IMPRESSIONS</code></td>
            <td><code>CTR_OPTIMIZATION_NEEDED</code></td>
        </tr>
        <tr>
            <td><strong>4</strong></td>
            <td><code>content_0045</code></td>
            <td>3.985</td>
            <td><code>LOW_CTR_HIGH_IMPRESSIONS</code></td>
            <td><code>CTR_OPTIMIZATION_NEEDED</code></td>
        </tr>
    </tbody>
</table>

<h2>7. Reproducibility</h2>
<p>All data transformations, feature engineering pipelines, and model evaluation code are fully reproducible in the project repository:</p>
<ul>
    <li><a href="https://github.com/eminahamamdzic/FlyRank/blob/main/work/notebooks/w04_baseline_score.ipynb">Week 4 Baseline Rule Notebook</a></li>
    <li><a href="https://github.com/eminahamamdzic/FlyRank/blob/main/work/notebooks/w05_model.ipynb">Week 5 Model Training Notebook</a></li>
</ul>

<h2>8. Acknowledgments & Data Credit</h2>
<p>Built on the <a href="https://flyrank.ai" target="_blank">FlyRank ML Internship dataset</a>.</p>

</body>
</html>
