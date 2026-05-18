## The Problem: Customer Segmentation for a Subscription Streaming Service
You're a data scientist at a streaming platform. The marketing team wants to move from generic campaigns to targeted ones, and they've asked you to build customer segments based on behavior and demographics. They also want recommendations for which segments to prioritize for retention vs. growth campaigns.
You'll work with four tables that need to be cleaned and joined. The dataset has the kind of mess you've now seen multiple times — bad column names, mixed formats, missing values, dupes — plus a few new wrinkles (categorical encoding, outliers, leaky features).

__The tables__
* users.csv — one row per user: user_id, signup date, age, gender, country, signup channel, subscription tier (basic/standard/premium)

* viewing_sessions.csv — one row per viewing session: session_id, user_id, content_id, start time, watch duration in seconds, device type, completion percentage

* content.csv — content catalog:  content_id, title, content type (movie/series/documentary), genre, release year, runtime minutes, content rating

* subscriptions.csv — subscription history: user_id, plan tier, monthly price, start date, end date (null if active), payment method, was the subscription canceled

### Tasks (build → segment → verify)

__Phase 1 — Feature engineering (the hard part of any ML project)__
1. Data hygiene. Clean column names, parse dates, handle missing/junk values, dedupe. Document each decision. Sanity-check by joining the tables and looking for orphans (sessions with no user, etc.).
2. Customer feature matrix. For each user, build a feature row containing:

    * Demographics: age (handle outliers/impossible values), gender, country, tenure in days
    * Subscription behavior: current tier, total months subscribed, number of plan changes, ever-canceled flag
    * Viewing behavior: total watch hours, sessions per week (averaged over tenure), average completion %, weekend-vs-weekday ratio, device diversity (number of distinct devices)
    * Content preferences: top genre, genre diversity (Shannon entropy or just count of distinct genres), share of watch time on series vs. movies
    * Recency: days since last session

This is the meat of the problem. The output should be one row per user with ~15–20 columns, all numeric or properly encoded.


__Phase 2 — Segmentation__

3. Exploratory analysis. Distributions of key features. Correlations. Are there obvious clusters visible in scatter plots of (watch_hours, recency) or (tenure, completion_pct)? PCA to 2D — does it show structure?
4. K-means clustering. Scale your features (StandardScaler). Try k = 2 through 8. Use the elbow method (inertia) and silhouette score to pick a k. Don't just take the math's word for it — does the chosen k produce interpretable segments?
5. Compare to a second algorithm. Run hierarchical clustering (or DBSCAN, or Gaussian mixture) on the same features. Do the two algorithms agree on broad structure? Use adjusted Rand index to quantify agreement. If they disagree, which one tells a more useful story?
6. Segment profiling. For your chosen segmentation, produce a profile table: one row per segment, columns showing mean/median values of the key features, plus segment size. Give each segment a human-readable name ("Bingewatchers," "Casual Movie Viewers," "At-Risk Premium," etc.) based on what actually differentiates it.


__Phase 3 — Verification & recommendation__

7. Stability check. Are the clusters robust? Re-run k-means with different random seeds — do users stay in the same segments? Drop one feature at a time and re-cluster — which features are load-bearing?
8. Predict churn from segment. Build a logistic regression (or random forest) predicting whether a user canceled, using their features. Compare two models: (a) using raw features, (b) using segment membership as a one-hot feature. Does segment ID alone capture meaningful churn signal? Report AUC for both on a held-out test set.
Watch out: "canceled" is a label leaked from one of your input tables. Make sure your churn target is properly held out and your features don't trivially predict it (e.g., don't use "ever_canceled" as a feature when predicting churn).
9. Recommendation memo. A short markdown cell at the end: for the marketing team, which 2–3 segments should be prioritized, for what (retention vs. expansion vs. winback), and what's the evidence?

 
__Skills this exercises__

merge across 4 tables with different keys, pivot/melt for genre features, computing Shannon entropy per group, sklearn's StandardScaler / KMeans / AgglomerativeClustering / silhouette_score / adjusted_rand_score, PCA for visualization, train/test split with train_test_split, roc_auc_score, and — the meta-skill — distinguishing features from labels in a way that doesn't leak.
