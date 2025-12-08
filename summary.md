# Will the Bill Make It Through Capitol Hill?
## Project Summary Report
## 1. Problem Overview

Each U.S. Congress introduces thousands of bills, yet only about 0.3% become law. Advocacy organizations, researchers, and government affairs teams largely rely on intuition or limited datasets to prioritize legislative monitoring and engagement.
This project evaluates whether natural language processing can extract predictive signals directly from bill text to rank the likelihood of passage, even under extreme class imbalance.

2. Project Goals

Primary objective:
Predict whether a bill becomes law using legislative text alone.

Research questions:

Do linguistic patterns distinguish bills that pass from those that fail?

Can transformer models capture useful signals from raw text?

How effectively can rare-event classification be handled under extreme imbalance?

3. Data Sources and Strategy

Initial data collection targeted multiple potential feature sources:

Congress.gov API
Official legislative database containing bill text, sponsors, procedural history, and actions.

OpenSecrets (Lobbying Data)
Intended to link lobbying expenditures to individual bills.

Social Media (Reddit & Twitter/X)
Attempted collection of public discourse volume related to bills.

News Coverage (The Guardian)
Mentions of bills in mainstream media.

4. Data Challenges and Pivots

External data sources proved unsuitable for modeling:

Lobbying Data

Lobbying expenditures are reported by issue category rather than individual bill, preventing meaningful 1:1 assignment between spending and specific legislation. Overlapping issue mappings cause unavoidable averaging and overcounting, rendering the data unusable for bill-level prediction.

Social Media

Only a tiny fraction of bills were mentioned across platforms. Among those mentioned, no correlation emerged between attention volume and passage probability.

News Coverage

Guardian coverage was sparse relative to the corpus of bills and showed no predictive relationship with outcomes.

5. Final Dataset

Due to these limitations, the project pivoted to a text-only approach using data solely from Congress.gov:

Time period: January 2013 – January 2025

Total bills: 13,767

Bills passed: 45

Pass rate: 0.33%

Class imbalance: ~305:1

Primary feature: Raw legislative bill text

6. Modeling Approach

The problem was formulated as rare-event text classification, with the recognition that binary classification under this imbalance is unreliable.

Objective:
Rank bills by likelihood of passage rather than produce threshold-based yes/no predictions.

Success was measured by how well models concentrated true positive bills near the top of the ranking.

7. Text Preprocessing

To eliminate label leakage and preserve realistic prediction:

HTML documents were cleaned and converted to plain text.

Post-passage cues such as “Public Law,” “Approved,” and legislative history sections were removed.

Text was truncated to pre-vote content only, ensuring no future information was inadvertently included.

Bills were encoded using MPNet sentence embeddings.

Long documents were handled using embedding summarization to avoid token-limit truncation artifacts.

This process ensured the model learned from genuine predictive language rather than direct outcome signals.

8. Data Splitting Strategy

Data was split strictly by chronology, aligned with congressional sessions:

Training: 113–114 Congress

Validation: 115–116 Congress

Test: 117–119 Congress

This simulated a true forecasting setup where the model only learns from past bills and is evaluated on entirely unseen future legislation, preventing temporal leakage and inflated performance.

9. Class Imbalance Handling

Given a 0.3% positive rate, traditional resampling techniques were avoided.

Instead, the project used:

Class-weighted Logistic Regression and SVM

XGBoost with scale_pos_weight adjustment

Model evaluation focused on Precision–Recall AUC (PR-AUC), a threshold-independent ranking metric suitable for rare-event detection, rather than accuracy or F1 score, which are unstable under extreme imbalance.

10. Primary Model

Architecture:

Text Encoder: all-mpnet-base-v2 sentence-transformer producing 768-dimensional embeddings.

Classifier: XGBoost (binary logistic) trained with class imbalance weighting.

Output: Continuous probability scores used for ranking, not binary classification.

Why this works:

MPNet captures deep semantic relationships beyond simple keyword features.

XGBoost models nonlinear interactions among embedding dimensions.

The combination yielded the strongest balance between ranking accuracy and probability stability.

11. Alternative Baselines

Two classical NLP pipelines were tested for comparison:

TF-IDF + Linear SVM (calibrated)

Strong non-neural baseline.

Reasonable ranking performance.

Inferior semantic representation compared to transformers.

TF-IDF + Logistic Regression

Simplest baseline.

Effective at coarse ranking.

Weak probability calibration under extreme imbalance.

12. Results

Best-performing model:
MPNet + XGBoost

PR-AUC ≈ 0.66, outperforming all TF-IDF baselines.

Despite the minuscule base pass rate (0.33%), the model successfully concentrated true positive bills near the top of the ranking, enabling practical prioritization of potentially successful legislation even when reliable binary classification is not feasible.

13. Limitations

The central limitation remains extreme class imbalance:

Only 45 positive examples across the entire dataset restrict model learning.

Even strong PR-AUC performance does not translate into dependable yes/no predictions.

Current outputs are most useful for ranking and filtering, not deterministic prediction.

14. Future Work

Potential directions for improvement include:

Model Enhancements

Apply long-context transformers such as Longformer for full-document ingestion.

Ensemble multiple architectures for robustness.

Experiment with advanced imbalance mitigation strategies.

Domain-specific fine-tuning on legislative corpora.

Feature Expansion

Sponsor and cosponsor demographics and political affiliation.

Committee paths and markup histories.

Session timing and election cycle effects.

Amendment trajectories.

Similarity measures to historically successful bills.

15. Practical Value

Applications:

Advocacy organizations can prioritize lobbying resources toward high-potential legislation.

Policy researchers can identify structural factors influencing bill success.

Government affairs professionals can monitor emerging legislation efficiently.

Legislative staff can refine bill drafting strategies based on patterns associated with passage.

Public transparency efforts benefit from enhanced insight into legislative dynamics.

Conclusion

This project demonstrates that modern NLP techniques can extract meaningful predictive structure from legislative text alone, even under extreme rarity conditions. Through careful leakage prevention, temporally realistic evaluation, and transformer-based modeling, the system achieved substantial ranking power (PR-AUC ≈ 0.66), enabling practical bill prioritization and validating text as a meaningful signal in legislative outcome analysis.
