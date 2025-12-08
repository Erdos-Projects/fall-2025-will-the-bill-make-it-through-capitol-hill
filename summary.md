# Will the Bill Make It Through Capitol Hill? - Project Summary Report

## 1. Problem Overview

Each year, thousands of bills are introduced in the United States Congress, yet only about 0.3% ultimately become law. Advocacy groups, policy researchers, and government affairs teams often rely on intuition or limited datasets to decide which legislation to monitor or support. This project examines whether modern natural language processing techniques can extract predictive information directly from bill text to identify which proposals are most likely to succeed.

## 2. Project Goals

The primary objective was to predict a bill’s likelihood of becoming law using its legislative text alone. The core research questions focused on whether successful bills contain identifiable linguistic patterns, whether transformer-based models can capture such signals, and how effectively rare-event prediction can be handled under extreme class imbalance.

## 3. Data Sources

1. congress.gov
   
Much of the information about any given bill is available on congress.gov, including the bill's full text, sponsors, whether it passed the House and the Senate, and other intermediate actions. This data is available for free using an API, though the number of API requests is limited to 5000 per hour. This request limit creates a bottleneck due to the sheer number of bills multiplied by the number of requests needed to collect each bill's text, sponsors, actions, etc.

2. opensecrets.org
   
Each year, organizations spend billions of dollars lobbying Congress to influence which bills pass. Detailed information about each lobbying agency's expenditure is collected by opensecrets.org. 1. Social Media How is a bill's passage correlated to mentions about it on popular social media platforms such as Reddit and Twitter? Unfortunately, acquiring this information can be costly. Unlike congress.gov, using social media platforms' APIs is not free.

3. News Coverage
   
In addition to social media mentions, we wondered whether a bill's passage was associated with how much it was covered by news outlets. Many news outlets don't provide direct access to this information, but we were able to obtain data for free from The Guardian.

## 4. Data Challenges and Pivots

Data sources other than congress.gov proved to be unhelpful for modelling the likelihood of bill passage. 

#### Lobbying Data 

Our goal was to use lobbying data from opensecrets.org to collect the amount of lobbying for each bill. After the initial EDA contradicted our expectations, we researched how lobbying data is officially reported and how opensecrets.org catalogs this information. This investigation revealed that it is simply not possible to assign a one-to-one correspondence between lobbying amounts and each bill using the information reported by the lobby agencies. This is because agencies report lobbying for specific issues, not for any particular bill. The way opensecrets.org connects this to a bill is by cataloging which issues are relevant to it. However, any given bill may be reported as related to many separate issues, while, conversely, one issue can be addressed by several distinct bills, and lobbying amounts are overcounted and then averaged across many related bills. 

#### Media Mentions 

The vast majority of bills aren't mentioned on social media. This is perhaps unsurprising, since 99% of bills never make it past introduction. However, even among the small fraction of bills that *do* get mentioned, there was very little meaningful correlation with bill passage. Bills that got mentioned more on social media had the same rate of passage compared with those that didn't. This result was somewhat surprising since we expected bills that were expected to pass to receive more attention. 

## 5. Final Dataset

Since lobbying data could not be directly linked to individual bills, and media mentions proved unhelpful, we ultimately used the bill's raw text as the main feature.

The final dataset came exclusively from Congress.gov, covering bills introduced between January 2013 and January 2025. It contained 13,767 bills, of which only 45 became law, yielding a passage rate of approximately 0.33%. This created an extreme class imbalance of roughly 305 to 1. The primary modeling feature was each bill’s full legislative text.

## 6. Modeling Approach

The task was framed as rare-event text classification under heavy imbalance. Rather than attempting unstable binary decisions, the objective was to rank bills by estimated likelihood of passage. Model success was therefore defined by how effectively genuine successful bills were concentrated near the top of the ranking distribution.

## 7. Text Preprocessing

HTML content was cleaned and converted to plain text prior to modeling. All post-passage indicators such as legislative history sections and approval language were removed to prevent data leakage. Bills were truncated to include only pre-vote content to preserve temporal integrity. MPNet sentence-transformer embeddings were generated from the cleaned text, and long documents were summarized at the embedding level to remain within model input limits while preserving semantic structure.

## 8. Data Splitting Strategy

Bills were divided using a chronological split aligned with congressional sessions. Training data covered the 113–114 Congress, validation used the 115–116 Congress, and testing applied to the 117–119 Congress. This ensured that the evaluation was conducted on genuinely unseen future data, preventing look-ahead bias and preserving realistic performance estimates.

## 9. Class Imbalance Handling

Given the extreme rarity of positive outcomes, no resampling or synthetic data generation methods were used. Instead, class-weighted learning was applied through weighted Logistic Regression and SVM models, as well as XGBoost with adjusted positive-class weighting. Performance evaluation focused on Precision–Recall AUC, a ranking metric stable under extreme imbalance, rather than threshold-based accuracy or F1 measures.

## 10. Primary Model

The primary model combined MPNet sentence embeddings with an XGBoost classifier. MPNet encoded each bill into a 768-dimensional semantic representation capturing deeper meaning than keyword-based features. XGBoost learned nonlinear patterns across this embedding space while accounting for imbalance through class weighting. The model output continuous likelihood scores that were used strictly for bill ranking rather than binary classification.

## 11. Alternative Baselines

Classical baselines included TF-IDF features paired with a calibrated linear SVM and class-weighted logistic regression. These models achieved reasonable ranking accuracy but lacked the deep semantic understanding provided by transformer embeddings and demonstrated weaker probability calibration under severe imbalance.

## 12. Results

The MPNet and XGBoost pipeline achieved the strongest performance with a PR-AUC of approximately 0.66, outperforming all TF-IDF baselines. Despite the extremely low base rate of bill passage, the model proved effective at concentrating real successful bills near the top of the prediction ranking, enabling practical candidate prioritization even when definitive classification remains unrealistic.

## 13. Limitations

The overwhelming class imbalance fundamentally limits predictive certainty. Although ranking performance significantly exceeded random chance, the scarcity of positive examples prevents reliable threshold-based prediction. Results are therefore best interpreted as prioritization tools rather than outcome guarantees.

## 14. Future Work

Further improvements may include using long-context transformer architectures such as Longformer to process complete documents, exploring ensemble modeling strategies, testing alternative class-balancing techniques, and fine-tuning models on domain-specific legislative corpora. Additional features such as sponsor affiliations, committee actions, session timing, amendment patterns, and similarity to previously successful legislation could also enhance model performance.

## 15. Practical Value

This work supports advocacy organizations in focusing resources on high-potential bills, aids policy researchers in identifying structural success factors, assists government affairs professionals with legislative monitoring, supports staff in refining bill drafting strategies, and contributes to public understanding of the legislative process through data-driven transparency.

# Conclusion

The project demonstrates that transformer-based NLP models can extract meaningful predictive signal directly from legislative text, even under extreme data imbalance. Through robust preprocessing to prevent leakage, temporally realistic evaluation, and ranking-focused modeling, the system achieved strong prioritization performance with a PR-AUC near 0.66, validating text as a valuable source for understanding and forecasting legislative success.
