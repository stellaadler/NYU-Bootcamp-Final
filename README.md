# NYU-Bootcamp-Final
My final project for Data Bootcamp Class

## 1. Introduction

This project investigates whether YouTube video performance can be predicted using readily available metadata and limited natural language processing (NLP) features. Specifically, the task is to classify videos into discrete view count categories (0–10K, 10K–50K, 50K–100K, 100K–500K, 500K+) using a combination of temporal, engagement, and channel-level variables, as well as sentiment extracted from video titles.

Multiple supervised learning models were evaluated, including baseline classifiers, tree-based methods, and ensemble models. Across all experiments, tree-based models—particularly Random Forests—performed best. Importantly, metadata-only models consistently matched or slightly outperformed models that included title sentiment, suggesting that structural engagement features are more predictive of view outcomes than coarse textual sentiment signals.

## 2. Data Description

The dataset consists of YouTube videos collected via the YouTube Data API from a curated set of baking-related channels. Each observation represents a single video, labeled by its total view count category.

### Key Feature Groups

- Metadata Features
- Engagement metrics: likes per view, comments per view, engagement rate
- Temporal features: year, month, hour of upload, day of week
- Video properties: duration (seconds), duration category
- Channel identifiers (one-hot encoded)
- Caption availability and timing indicators

NLP Features
- Title sentiment score (continuous polarity)
- Title sentiment label (categorical)

The target variable is a multi-class categorical label representing view count ranges. Because these classes are imbalanced, macro F1 score was used as the primary evaluation metric to ensure performance across all view categories.

## 3. Models and Methods

### Baseline Models
To establish performance benchmarks, the following models were trained on both feature sets:
- Dummy Classifier (most frequent)
- Logistic Regression
- Decision Tree
- Random Forest

All models were implemented using a unified preprocessing pipeline with:
- Standardization of numeric features
- One-hot encoding of categorical variables
- Stratified train/test split (80/20)

### Hyperparameter Tuning
Given its strong baseline performance, Random Forest was further optimized using 5-fold cross-validation and macro F1 scoring. A reasonable but non-exhaustive grid was used to balance runtime and performance gains.

### Advanced Model
A tuned Gradient Boosting classifier was also evaluated on the metadata + NLP feature set to explore whether a different ensemble method could outperform Random Forest.

### Interpretability
To understand feature influence, two interpretability approaches were used:
- Built-in Random Forest feature importances
- SHAP values for global and interaction-level explanations

## 4. Results and Interpretation

### Model Performance Summary

Model performance was evaluated using accuracy and macro-averaged F1 score, with macro F1 prioritized due to class imbalance across view-count buckets. Across both feature sets (metadata-only and metadata + NLP), tree-based models consistently outperformed linear models, indicating that YouTube video performance is driven by nonlinear relationships among features.

The strongest overall results were achieved by:

Random Forest (tuned, metadata-only)
- Accuracy ≈ 0.72
- Macro F1 ≈ 0.60

Gradient Boosting (tuned, metadata + NLP)
- Accuracy ≈ 0.73
- Macro F1 ≈ 0.62

While Gradient Boosting achieved the highest macro F1 overall, the performance gain relative to the tuned Random Forest was modest, suggesting diminishing returns from increased model complexity.

### Baseline Model Comparison

Across both feature sets:

- Logistic Regression consistently underperformed, with macro F1 scores around 0.41–0.43
- Decision Trees performed moderately, improving substantially over linear models but remaining less stable than ensemble methods
- Random Forest dominated baseline comparisons, delivering the strongest and most consistent performance

This pattern strongly suggests that video popularity is governed by nonlinear and interaction-driven effects, rather than additive linear relationships. Features such as engagement rate, timing, and channel identity interact in complex ways that linear classifiers fail to capture.

### Effect of Feature Sets (Metadata vs. Metadata + NLP)

Contrary to expectations, adding NLP-derived features (e.g., sentiment scores) resulted in only marginal improvements:
- Macro F1 gains were small and inconsistent
- Metadata-only Random Forest performed nearly as well as NLP-augmented models

This indicates that:
- Engagement-based metadata already captures much of the signal that text sentiment attempts to proxy
- Title and caption sentiment may be too noisy or shallow to significantly improve predictive power
- Viewer response (likes, comments, engagement rate) is a stronger indicator of performance than textual tone alone

### Confusion Matrix Analysis (Tuned Random Forest)

The confusion matrix for the tuned Random Forest reveals several important patterns:

- The model performs best on high-volume classes (100K–500K, 500K+), with strong diagonal concentration
- Misclassifications tend to occur between adjacent buckets, rather than extreme jumps (e.g., 0–10K → 500K+), indicating that the model has learned an ordinal structure
- Mid-range categories (50K–100K) are the hardest to classify, reflecting overlapping engagement behavior across neighboring tiers

This behavior suggests that while the model is effective at learning relative popularity levels, class imbalance and fuzzy boundaries between tiers limit perfect separation.

### Feature Importance and Interpretability

Feature importance and SHAP analyses reinforce these findings:

- Engagement efficiency metrics (likes per view, comments per view, engagement rate) dominate predictions
- Video duration and timing features (hour, month, year) play a secondary but meaningful role
- Channel identity features matter, but less than engagement behavior

SHAP interaction plots further reveal that:
- Engagement metrics interact strongly with duration and timing
- No single feature drives performance alone; rather, combinations of features determine outcomes

This explains why ensemble tree models outperform linear baselines: they are better equipped to capture conditional dependencies and feature interactions inherent in content performance.

## 5. Interpreting the Limited Impact of Sentiment Features

While metadata + NLP models performed competitively, the addition of title sentiment did not produce consistent performance gains. This result is likely driven not by the irrelevance of textual information, but by limitations in the sentiment representation itself.

The sentiment score used in this project compresses complex linguistic information into a single polarity measure. YouTube video titles—especially in the baking domain—often rely on stylistic cues such as exaggeration, branding, curiosity framing, emojis, or capitalization, which are poorly captured by generic sentiment models. As a result, the sentiment feature may introduce noise rather than useful signal.

Additionally, sentiment does not necessarily align with viewer behavior. Highly successful videos often use neutral or instructional titles, while emotionally positive titles do not guarantee engagement. This disconnect between linguistic sentiment and audience response limits the predictive value of simple sentiment-based features.

These findings suggest that the issue is not NLP itself, but the use of a coarse sentiment proxy that fails to capture semantic intent, novelty, or curiosity.

## 6. Model Interpretability Insights

Feature importance analysis from the tuned Random Forest reinforces these conclusions. Engagement metrics dominate the importance rankings, followed by duration and timing variables.

Channel identifiers appear among the top features, reflecting systematic differences in audience size and brand recognition across creators. While informative, this also highlights a potential limitation: channel-level features may partially encode prior popularity rather than intrinsic video quality.

SHAP analysis further confirms that engagement and timing features consistently drive predictions across view categories, while sentiment-related features contribute little to marginal prediction changes.

## 7. Limitations

Several limitations should be acknowledged:

- Simplistic NLP representation: Using a single sentiment score limits the ability to capture semantic nuance, curiosity framing, or instructional intent.
-Channel-level leakage: Channel identifiers may implicitly encode historical popularity, inflating predictive performance.
-Static engagement ratios: Engagement metrics are measured post-publication and may not reflect early-stage prediction scenarios.
-Domain specificity: Results are based on baking-related content and may not generalize to other YouTube niches.

## 8. Conclusion and Next Steps

This project demonstrates that YouTube video performance can be predicted reasonably well using metadata alone, with Random Forest models achieving strong accuracy and macro F1 scores. Engagement and timing features emerge as the most reliable predictors, while simple title sentiment features provide limited additional value.

Future work could improve performance by:
- Incorporating richer NLP representations (e.g., transformer-based embeddings)
- Modeling early engagement dynamics over time
- Removing or controlling for channel identity to test generalization
- Extending analysis to video descriptions or thumbnails

Overall, the findings highlight the importance of feature quality over feature novelty, and show that well-engineered metadata can outperform shallow NLP features in real-world prediction tasks.
