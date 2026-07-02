# NBA Game Demand Classification: Predicting High-Demand New York Knicks Home Games

## Project Overview

NBA teams make critical business decisions before games are played, including ticket pricing, marketing campaigns, staffing levels, security planning, concessions management, and merchandise inventory allocation.

This project develops a machine learning classification framework to identify high-demand New York Knicks home games before they occur, using 30 seasons of historical data (1,168 games) covering attendance, team performance, scheduling, rivalry, playoff context, and player star power.

Rather than predicting exact attendance figures, the objective is to identify games likely to generate exceptional fan demand and support proactive business decision-making. Evaluated across five classification models, the final model (AdaBoost) achieved 77% precision and 71% recall on unseen seasons, with top-ranked games showing a 1.39x lift over random selection, a measurable improvement over intuition-based game-demand assessment.

---

# Business Problem

Demand for NBA games varies significantly depending on factors such as:

* Team performance
* Opponent popularity
* Rivalry matchups
* Playoff implications
* Scheduling effects
* Presence of star players

The challenge addressed in this project was:

> How can the New York Knicks identify high-demand home games before they occur so that revenue, marketing, and operational resources can be allocated more effectively?

Accurately identifying these games enables organizations to:

* Optimize ticket pricing strategies
* Allocate marketing resources efficiently
* Improve staffing and arena operations planning
* Manage concessions and merchandise inventory
* Enhance fan experience during peak-demand events

The resulting model functions as a decision-support tool rather than simply a predictive model.

---

# Dataset Summary

The project combines NBA game-level attendance data with NBA All-Star player information to create a comprehensive game-demand classification dataset.

| Metric                    | Value                |
| ------------------------- | --------------------- |
| Total NBA Games Available | 30,743                |
| Knicks Home Games Used    | 1,168                 |
| Seasons Covered           | 1990–2019              |
| NBA All-Star Records      | 1,004                 |
| Unit of Analysis          | One Knicks Home Game  |

---

# Key Variables

### Attendance & Venue

* Attendance
* Arena Capacity

### Team Performance

* Current Win Percentage
* Previous Season Win Percentage
* Last Five Games Performance

### Scheduling Factors

* Day of Week
* Month
* Weekend Indicator

### Game Context

* Playoff Indicator
* Rivalry Indicator

### Opponent Information

* Visitor Team

### Star Power

* Home All-Star Count
* Visitor All-Star Count

---
## Data Preparation

The project combined game-level attendance data with player-level NBA All-Star data to create a predictive modeling dataset focused on New York Knicks home games.

Key preparation steps included:

* Filtering the dataset to New York Knicks home games across 30 NBA seasons (1990–2019)
* Aggregating player-level All-Star records into Home All-Star Count and Visitor All-Star Count features
* Merging attendance and All-Star datasets at the game level
* Creating occupancy rate and high-demand target variables
* Engineering performance, scheduling, and game-context features including Current Win %, Previous Season Win %, Last Five Games Performance, Playoffs, Rivalry, Day of Week, and Month
* Restricting model inputs to pre-game information to ensure realistic demand predictions
* Preparing a final classification-ready modeling dataset for machine learning analysis

# Target Variable Development

## Initial Approach

The project initially defined demand using arena occupancy rates.

**High Demand = Sold-Out Game (100% Occupancy)**

While this definition aligned with business intuition, exploratory analysis revealed that Knicks attendance was heavily concentrated near arena capacity, with roughly 78% of all games effectively sold out.

As a result, occupancy-based demand labels provided limited separation between classes and reduced the ability of classification models to distinguish meaningful demand differences.

## Refined Approach

The target variable was redefined based on relative attendance ranking rather than a fixed occupancy threshold:

**High Demand = Highest-Attendance Games (~70% of dataset)**

This reflects how consistently Knicks home games sell near full arena capacity. The label captures the highest tier of attendance rather than a strict top-decile cutoff, which is not achievable given how tightly clustered attendance is near capacity across the dataset.

## Note on Threshold Calculation

The attendance threshold used to define "High Demand" is calculated using only the training-period seasons (1990–2014), not the full 1990–2019 dataset. During development, an earlier version of this threshold was calculated using the full dataset, including the 2015–2019 test seasons. This allowed information from the test period to influence which historical games were labeled High Demand, artificially inflating model performance (ROC-AUC of 0.81 with a recall of under 1%, meaning the model was not actually identifying high-demand games in practice).

This was corrected by restricting the threshold calculation to training-period data only (≤2014), then applying that same threshold consistently across the full dataset. All results reported below reflect this corrected methodology.

---

# Feature Engineering

Several business-oriented variables were incorporated to better capture factors influencing fan demand, including:

* Weekend Indicator
* Rivalry Indicator
* Playoff Indicator
* Home All-Star Count
* Visitor All-Star Count
* Team Performance Metrics

These variables were selected based on their potential influence on attendance behavior and game demand.

---

# Methodology

## Data Preparation

The following steps were performed:

* Data cleaning and validation
* Missing value assessment
* Feature engineering
* Target variable development
* Time-based train/test split

## Models Evaluated

Five classification models were compared:

* Logistic Regression
* K-Nearest Neighbors (KNN)
* Decision Tree
* Random Forest
* AdaBoost

## Evaluation Metrics

Models were evaluated using:

* Accuracy
* Precision
* Recall
* F1 Score
* ROC-AUC
* Precision-Recall AUC

Additional evaluation techniques included:

* Confusion Matrix Analysis
* Threshold Optimization
* Lift Analysis
* Cumulative Lift Analysis
* Feature Importance Analysis

---

# Model Selection

Five classification models were evaluated using a consistent, time-based train/test framework: Logistic Regression, KNN, Decision Tree, Random Forest, and AdaBoost.

**AdaBoost achieved the strongest overall performance and was selected as the final model.**

| Model | ROC-AUC |
| --- | --- |
| **AdaBoost** | **0.661** |
| Random Forest | 0.599 |
| KNN (k=11) | 0.574 |
| Decision Tree | 0.522 |
| Logistic Regression | 0.513 |

---

# Final Model Performance

| Metric                   | Value |
| ------------------------ | ----- |
| ROC-AUC                  | 0.661 |
| Precision-Recall AUC     | 0.813 |
| Precision                | 0.766 |
| Recall                   | 0.708 |
| F1 Score                 | 0.736 |
| Classification Threshold | 0.50  |

The final model demonstrated a meaningful, validated ability to distinguish between high-demand and lower-demand games on out-of-sample seasons (2015–2019).

---

# Quantifiable Business Impact

* **77% precision:** when the model flags a game as "High Demand," it is correct roughly 3 out of 4 times, supporting confident allocation of marketing spend and staffing.
* **71% recall:** the model correctly catches about 7 out of 10 truly high-demand games in advance, reducing the risk of being under-staffed or under-marketed for a big game.
* **1.39x lift in the top decile:** games ranked in the model's top 10% by predicted demand saw attendance-driving characteristics at a rate 39% higher than random selection, meaning targeted resource allocation based on model output is significantly more efficient than a non-data-driven approach.
* Supports data-driven decisions across **ticket pricing, marketing spend, staffing levels, and merchandise/concessions inventory**, replacing manual or intuition-based game-demand assessments.

---

# Threshold Optimization

The default classification threshold of 0.50 was evaluated against lower alternative thresholds (0.05–0.40).

At thresholds below 0.50, the model defaulted to predicting nearly every game as "High Demand," since a majority of games (~70%) fall into that class, which produced misleadingly high recall without genuine discrimination between classes. Threshold 0.50 was retained as it provides the model's most balanced, genuinely discriminative performance between precision and recall.

---

# Key Findings

Several factors were found to influence game demand:

* Team performance (Previous Season Win % and Current Win % were the strongest predictors)
* Presence of All-Star players
* Opponent strength and popularity
* Rivalry matchups
* Scheduling effects
* Arena capacity constraints

The strongest-performing model consistently leveraged a combination of team quality, star power, scheduling characteristics, and opponent information.

---

# Business Insight

The most important lesson from this project was not related to algorithm selection.

The largest improvement in predictive performance came from refining the business definition of demand and validating the target-variable methodology, rather than changing machine learning models alone.

An earlier version of the target variable inadvertently used future (test-period) data when calculating the demand threshold, producing an inflated ROC-AUC (0.81) alongside a recall of under 1%, a model that looked strong on a single headline metric but was not functionally useful. Correcting this to use only training-period data for threshold calculation produced a lower but validated and trustworthy ROC-AUC of 0.66.

This reinforces a core analytics principle:

> A model's headline metric is only meaningful if the methodology behind it is sound. Validating how a number was produced matters as much as the number itself.

---

# Business Applications

This model can help NBA team:

* Forecast high-demand games before tickets go on sale
* Support dynamic ticket pricing decisions
* Improve marketing campaign planning
* Allocate staffing resources efficiently
* Optimize concessions and merchandise inventory
* Improve operational planning

The resulting framework provides actionable business insights that support proactive decision-making.

---

# Future Improvements

Potential future enhancements include:

* Weather data integration
* Historical ticket pricing information
* Player injury data
* Promotional campaign information
* Social media sentiment analysis
* Real-time fan engagement metrics

---

# Tools & Technologies

* Python
* Pandas
* Scikit-Learn
* Machine Learning
* Predictive Analytics
* Classification Modeling
* Matplotlib
* Seaborn
* Jupyter Notebook

---


## Model Performance

### ROC Curve Comparison
![ROC Curve](image/roc_curve_comparison%20%282%29.png)

### Precision-Recall Curve Comparison
![PR Curve](image/pr_curve_comparison%20%282%29.png)

### Confusion Matrix
![Confusion Matrix](image/confusion_matrix%20%282%29.png)

### Feature Importance
![Feature Importance](image/feature_importance%20%282%29.png)
