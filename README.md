# Will The Bill Make It Through Capitol Hill? Predicting the Success of Congressional Bills.

This is a repository for the project ``Will The Bill Make It Through Capitol Hill?'' as part of The Erdős Institute Fall 2025 Data Science Bootcamp.

Team Members: Spencer Arnesen, Arjun Gopinath, Chris Jackson, Connor McCranie, Sara Mezuri.

1. [Introduction](#introduction)
2. [Data Generation](#data-generation)
3. [Exploratory Data Analysis](#exploratory-data-analysis)
4. [Modeling](#modeling)
5. [Results and Conclusion](#results-and-conclusion)
6. [Future Work](#future-work)


## Introduction

Legislative processes are notoriously difficult to predict, yet accurate forecasting of bill outcomes has significant value for advocacy groups, businesses, and researchers. This project develops a data-driven approach to predict the passage probability and timeline of congressional bills using publicly available data sources. By combining traditional political science variables with modern NLP techniques applied to bill text and media coverage, we aim to create a real-time legislative intelligence system that outperforms existing prediction methods.

## Data Generation 
Our dataset was collected from Congress.gov using their public API, spanning bills from the 113th through 119th Congresses (January 2013 - January 2025). The data collection process involved:
Data Source: Congress.gov API

Endpoint: https://api.congress.gov/v3/bill/{congress}/{billType}/{billNumber}
Retrieved full bill text in HTML format along with metadata including:

Bill sponsorship information (representative/senator, state, party)
Introduction date and congressional session
Bill type (HR, S, HJRES, SJRES, etc.)
Committee assignments
Final status (became law vs. did not pass)

Preprocessing:

HTML Parsing: Used BeautifulSoup to extract text content from HTML-formatted bill documents
Text Cleaning: Removed HTML tags, normalized whitespace, and standardized formatting
Label Assignment: Bills marked as "law=True" if they became public law, "law=False" otherwise

Final Dataset Characteristics:

Total Bills: 13,812 bills
Date Range: January 2013 - January 2025
Passed Bills: ~345 bills (~2.5% of dataset)
Class Imbalance: Approximately 40:1 ratio (unsuccessful to successful bills)
Fields: Bill ID, sponsor, state, date, congress session, bill type, topic, text, law status

Initially, we collected additional features including:
Media coverage from The Guardian API
Sentiment analysis scores
Network centrality measures (degree, betweenness, closeness)
Lobbying data

However, due to data quality issues, a lack of 1:1 connection between lobbying and bill data, and incomplete coverage, we focused on features directly available from Congress.gov.

## Exploratory Data Analysis
After learning that predictive modeling was not viable due to extreme class imbalance, our EDA became the main part of this project.

Class Imbalance

Passage Rate: Only ~2.5% of bills successfully become law (~345 out of 13,812)
Class Ratio: Approximately 40:1 (unsuccessful to successful)
Extreme imbalance made traditional ML approaches prone to severe overfitting

## Modeling
We passed the bill's text through MPNet to encode the text as vectors in a high dimensional vector space. The resulting vectors were passed into several models including a loggrithmic regression, SVM and XGBoost. We selected XGBoost since it outperformed the other models.

## Results and Conclusion
MPNet + XGBoost achieves the best ranking performance (PR-AUC = 0.66), outperforming TF-IDF baselines and effectively concentrating rare successful bills at the top for practical prioritization rather than binary classification.

## Future Work
Model Improvements:
Use Longformer for full document context (4096+ tokens)
Try ensemble methods combining multiple models
Experiment with different class balancing techniques
Fine-tune on domain-specific legislative corpus

Additional Features to Explore:
Sponsor/cosponsor party affiliation and seniority
Committee assignments and markup history
Temporal features (session timing, election cycles)
Bill amendment patterns
Similarity to previously successful bills
