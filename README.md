# Blue Side Advantage in Professional League of Legends

Authors: Nhan Doan, Sean Liu

## Introduction

### General Introduction

League of Legends (LoL) is a Multiplayer Online Battle Arena (MOBA) game developed by Riot Games. It has one of the largest professional esports scenes in the world, where teams compete from two sides of the map: Blue side and Red side.

The dataset used in this project is sourced from Oracle's Elixir. For this project, we focus only on professional League of Legends esports matches from 2022. This keeps the analysis within one competitive season, which is important because League of Legends changes across years through patches, champion changes, item changes, and professional meta shifts.

### Dataset Size

The raw 2022 dataset contains **150,348 rows** and **165 columns** of professional League of Legends match data. Each actual match can have up to 12 rows: 10 player-level rows and 2 team-level rows, one for each side.

After filtering to team-level observations, keeping only Blue and Red side rows, and keeping rows with valid match results, the cleaned dataset contains **25,058 team-level observations**. Since each match contributes one Blue-side team row and one Red-side team row, these observations represent **12,529 professional matches**.

### Research Question

A commonly held belief among League of Legends players is that Blue side provides a competitive advantage because of draft order, map orientation, and how teams approach neutral objectives.

**Question: Does Blue side provide a competitive advantage in 2022 professional League of Legends matches?**

Understanding whether Blue side provides an advantage is important for evaluating fairness in professional League of Legends. If a systematic side bias exists, it could influence drafting strategies, tournament design, and how analysts interpret match outcomes. Quantifying this effect provides a data-driven perspective on whether observed win rate differences reflect structural side advantages.

### Columns of Interest

We focus on the following variables:

- `gameid`: unique identifier for each match.
- `league`: professional league where the match was played.
- `position`: whether the row describes an individual player or the team as a whole.
- `side`: whether the team played on Blue or Red side.
- `result`: match outcome, where 1 means win and 0 means loss.
- `goldat10`: team gold at 10 minutes.
- `xpat10`: team experience at 10 minutes.
- `csat10`: team creep score at 10 minutes.
- `golddiffat10`: team gold differential at 10 minutes.
- `xpdiffat10`: team experience differential at 10 minutes.
- `csdiffat10`: team creep score differential at 10 minutes.
- `firstherald`: whether the team secured the first Rift Herald, used for the missingness analysis.
- `url`: official match or data-provider link, used for the NMAR discussion.

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

The Oracle's Elixir dataset contains both player-level and team-level observations. Since our research question concerns whether Blue side provides an advantage at the match level, we first filtered the dataset to retain only rows where `position == "team"`. This ensures that each observation represents a team's overall performance in a match rather than an individual player's statistics.

We then removed observations whose `side` was not recorded as either Blue or Red and removed rows whose match result was not recorded as either a win (`1`) or loss (`0`). These filtering steps ensure that all observations correspond to completed professional matches with clearly defined outcomes.

The League of Legends dataset description notes that some columns should be boolean but are not stored that way. To address this, we converted boolean-like columns such as `firstblood`, `firstdragon`, `firstherald`, `firstbaron`, `firsttower`, and `playoffs` to pandas nullable boolean type when those columns were present. This preserves missing values while making the data type match the meaning of the columns.

We also created two derived variables: `result_label`, which converts match outcomes from numerical values into the more interpretable labels "Win" and "Loss", and `is_blue`, a binary indicator that equals 1 when a team played on Blue side and 0 otherwise. These derived variables simplify later visualization and modeling tasks.

After cleaning, the dataset contains **25,058 team-level observations** representing both sides of 2022 professional matches.

| gameid                | league   | side   |   result |   goldat10 |   xpat10 |   csat10 |   golddiffat10 |   xpdiffat10 |   csdiffat10 |
|:----------------------|:---------|:-------|---------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|
| ESPORTSTMNT01_2690210 | LCKC     | Blue   |        0 |      16218 |    18213 |      322 |           1523 |          137 |           -8 |
| ESPORTSTMNT01_2690210 | LCKC     | Red    |        1 |      14695 |    18076 |      330 |          -1523 |         -137 |            8 |
| ESPORTSTMNT01_2690219 | LCKC     | Blue   |        0 |      14939 |    17462 |      317 |          -1619 |        -1586 |          -27 |
| ESPORTSTMNT01_2690219 | LCKC     | Red    |        1 |      16558 |    19048 |      344 |           1619 |         1586 |           27 |
| 8401-8401_game_1      | LPL      | Blue   |        1 |        NaN |      NaN |      NaN |            NaN |          NaN |          NaN |

*Note: The DataFrame above is not the full DataFrame. Only the first 5 rows are shown for reference.*

### Univariate Analysis

We first performed a univariate analysis on the number of team-level observations for each side.

<iframe
  src="assets/side-counts.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This bar plot shows that the number of Blue-side and Red-side team-level observations is equal. This is expected because each professional match contributes exactly one Blue-side team and one Red-side team.

We also plotted the distribution of team gold at 10 minutes, `goldat10`.

<iframe
  src="assets/gold-at-10-distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The histogram is mostly centered around the middle of the distribution, with a slight right skew. This means most teams have similar amounts of gold at 10 minutes, but some teams build much larger early leads. These higher values can be interpreted as early advantages gained through successful skirmishes, objective control, or lane dominance.

### Bivariate Analysis

We performed a bivariate analysis on `side` and `result` to compare win rates between Blue side and Red side.

<iframe
  src="assets/win-rate-by-side.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The Blue-side win rate is approximately **52.5%**, while the Red-side win rate is approximately **47.5%**. This suggests that Blue-side teams won slightly more often in the observed 2022 data. This pattern is consistent with the idea that Blue side may provide a modest competitive advantage, though statistical testing is needed before drawing a stronger conclusion.

We also performed a bivariate analysis on gold differential at 10 minutes for winning and losing teams.

<iframe
  src="assets/gold-diff-at-10-by-result.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The box plot shows that winning teams tend to have higher 10-minute gold differences compared to losing teams. This is consistent with the idea that early-game advantages are associated with match success in professional League of Legends. However, there is still noticeable overlap between the two distributions, so a positive gold differential at 10 minutes does not guarantee a win.

### Interesting Aggregate

The table below compares average early-game statistics and win rate by side.

| side   |   win_rate |   goldat10 |   xpat10 |   csat10 |   golddiffat10 |   xpdiffat10 |   csdiffat10 |
|:-------|-----------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|
| Blue   |      0.525 |    15733.2 |  18211.9 |  314.679 |         88.156 |       24.639 |        0.344 |
| Red    |      0.475 |    15645.0 |  18187.2 |  314.335 |        -88.156 |      -24.639 |       -0.344 |

The table shows that Blue side has a higher average win rate than Red side. Blue-side teams also have slightly higher average gold, experience, and creep score at 10 minutes. The corresponding differential features are positive for Blue side and negative for Red side. This reinforces the pattern that Blue-side teams performed slightly better on average in both match outcomes and early-game metrics, though the size of the early-game differences is relatively small.

## Assessment of Missingness

### NMAR Analysis

To decide whether a column is likely NMAR, we need to reason about the data-generating process, not just the observed data. Some missingness in this dataset is structural. For example, player-specific fields are missing in team-level rows because those values do not apply to team summaries.

One column that may be NMAR is `url`. This column is missing when there is no official match link or data-provider link recorded for that game. The missingness may depend on the missing value itself: if no official link exists or was recorded, then the value of `url` is missing. To make this missingness MAR instead, we would want an additional column such as `data_provider_present` or `official_match_page_available` that records whether each game had an official tracked source.

### Missingness Dependency

To test for missingness dependency in the dataset, we selected the `firstherald` column for analysis. This column has non-trivial missingness in the cleaned team-level data. We examined whether its missingness depends on `league` and whether it depends on `side` using permutation tests. For both tests, the significance level is 0.05 and the test statistic is Total Variation Distance (TVD).

First, we test whether the missingness of `firstherald` depends on `league`.

**Null Hypothesis:** The distribution of `league` is the same when `firstherald` is missing and when it is not missing.

**Alternative Hypothesis:** The distribution of `league` is different when `firstherald` is missing compared to when it is not missing.

The observed TVD for this permutation test is **0.9926**, and the p-value is **0.0**.

<iframe
  src="assets/missingness-league-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Since the p-value is below the selected 0.05 significance level, we reject the null hypothesis. This suggests that the missingness of `firstherald` depends on `league`. This makes sense because different leagues may have different data tracking practices, tournament formats, or coverage quality.

Second, we test whether the missingness of `firstherald` depends on `side`.

**Null Hypothesis:** The distribution of `side` is the same when `firstherald` is missing and when it is not missing.

**Alternative Hypothesis:** The distribution of `side` is different when `firstherald` is missing compared to when it is not missing.

The observed TVD for this permutation test is **0.0**, and the p-value is **1.0**.

<iframe
  src="assets/missingness-side-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Since the p-value is above the selected 0.05 significance level, we fail to reject the null hypothesis. There is not enough evidence that the missingness of `firstherald` depends on `side`. This result is reasonable because each match has one Blue-side team and one Red-side team, so there is no strong reason for `firstherald` missingness to depend on side.

## Hypothesis Testing

We investigate whether Blue-side teams win more often than would be expected under a fair 50/50 model.

**Null Hypothesis:** Blue-side teams win 50% of games on average.

**Alternative Hypothesis:** Blue-side teams win more than 50% of games on average.

**Test Statistic:** Blue-side win rate minus 0.5.

**Significance Level:** 0.05.

Since the alternative hypothesis says the Blue-side win rate is higher than 50%, this is a right-tailed test.

<iframe
  src="assets/blue-side-hypothesis-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The observed Blue-side win rate is **0.5248**, which gives an observed test statistic of **0.0248**. The p-value from the simulation is approximately **0.0**, meaning that such an extreme result is very unlikely under the null hypothesis.

Since the p-value is below 0.05, we reject the null hypothesis. There is statistically significant evidence that Blue-side teams win more often than expected under a 50% null model. However, this does not prove that Blue side directly causes teams to win; it only suggests that Blue side is associated with a higher win rate in the 2022 professional League of Legends data.

## Framing a Prediction Problem

Our prediction problem is:

**Can we predict whether a team wins a professional League of Legends match using information available at 10 minutes?**

The response variable is `result`, where a value of 1 indicates a win and a value of 0 indicates a loss. This is a binary classification problem because there are only two possible outcomes: win or loss.

We chose this prediction problem because early-game advantages are often important indicators of match outcomes in professional League of Legends. Predicting the winner using only information available at 10 minutes allows us to investigate how much predictive power early-game performance has before many major game-defining events occur.

To avoid data leakage, we only use information that would be known at approximately 10 minutes into a match. The features used in our models are `side`, `league`, `goldat10`, `xpat10`, `csat10`, `golddiffat10`, `xpdiffat10`, and `csdiffat10`. These variables capture a team's resources and relative advantages during the early stages of the game. We do not use final match statistics such as total `kills`, `dragons`, `barons`, `towers`, final gold, or game length because those values would not be available at the time the prediction is made.

For modeling, we use the 2022 team-level dataset and split the data by `gameid`, so rows from the same game do not appear in both the training and test sets. This prevents game-level leakage and gives a cleaner evaluation on unseen matches. We use 75% of the games for training and 25% of the games for testing. Since each match contributes one winning team row and one losing team row, the dataset is balanced, so accuracy is an appropriate primary evaluation metric. We also report precision, recall, and F1-score for additional context.

Below is the head of the modeling DataFrame:

| side   | league   |   goldat10 |   xpat10 |   csat10 |   golddiffat10 |   xpdiffat10 |   csdiffat10 |   result |
|:-------|:---------|-----------:|---------:|---------:|---------------:|-------------:|-------------:|---------:|
| Blue   | LCKC     |      16218 |    18213 |      322 |           1523 |          137 |           -8 |        0 |
| Red    | LCKC     |      14695 |    18076 |      330 |          -1523 |         -137 |            8 |        1 |
| Blue   | LCKC     |      14939 |    17462 |      317 |          -1619 |        -1586 |          -27 |        0 |
| Red    | LCKC     |      16558 |    19048 |      344 |           1619 |         1586 |           27 |        1 |
| Blue   | LPL      |        NaN |      NaN |      NaN |            NaN |          NaN |          NaN |        1 |

## Baseline Model

Our baseline model is a Logistic Regression classifier that uses simple early-game information available at approximately 10 minutes into a match.

The baseline model uses four features:

| Feature | Type | Encoding / Transformation |
|:---|:---|:---|
| `goldat10` | Quantitative | Median imputation, then `StandardScaler` |
| `xpat10` | Quantitative | Median imputation, then `StandardScaler` |
| `csat10` | Quantitative | Median imputation, then `StandardScaler` |
| `side` | Nominal | Most-frequent imputation, then `OneHotEncoder` |

There are no ordinal features in the baseline model.

For the quantitative features, we first impute missing values using the median and then standardize the features using `StandardScaler`. We use median imputation because it is robust to outliers and allows us to retain observations with missing values. For the nominal feature `side`, we impute missing values using the most frequent category and then apply one-hot encoding so that the model can work with categorical data. All preprocessing steps and model training are implemented in a single sklearn Pipeline.

The baseline model achieved the following performance on the test set:

| Metric | Value |
|:---|---:|
| Accuracy | 0.6262 |
| Precision | 0.6285 |
| Recall | 0.6176 |
| F1-Score | 0.6230 |

<iframe
  src="assets/baseline-confusion-matrix.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Compared to random guessing on a balanced dataset, which would achieve approximately 50% accuracy, the baseline model performs noticeably better. This suggests that early-game information contains meaningful predictive signal regarding match outcomes. However, the model is still limited because it only uses raw early-game resource totals and side, without using opponent-relative differences.

## Final Model

For our final model, we expanded upon the baseline model by incorporating additional early-game information and engineering new features that better capture a team's relative advantage over its opponent. We also replaced Logistic Regression with a Random Forest classifier to allow the model to capture nonlinear relationships between early-game performance and match outcomes.

The final model uses the following original features:

- Quantitative features: `goldat10`, `xpat10`, `csat10`, `golddiffat10`, `xpdiffat10`, and `csdiffat10`
- Nominal features: `side` and `league`
- Ordinal features: none

In addition, we engineered three new features:

- `overall_resource_at10`: combines gold, experience, and creep score at 10 minutes.
- `overall_diff_at10`: combines gold, experience, and creep score differentials at 10 minutes.
- `positive_gold_diff`: indicates whether a team was ahead in gold at the 10-minute mark.

These engineered features were chosen because League of Legends is fundamentally a game of resource accumulation and relative advantages. While raw gold, experience, and creep score provide information about a team's overall strength, the corresponding differential statistics more directly capture whether a team is outperforming its opponent. The engineered features summarize these relationships and provide the model with a more complete view of early-game advantages.

All preprocessing, feature engineering, and model training were implemented within a single sklearn Pipeline. Quantitative features were median-imputed to handle missing values, while nominal features were imputed using the most frequent category and one-hot encoded.

To improve model performance, we used GridSearchCV with 5-fold cross-validation to tune the Random Forest hyperparameters. We searched over the number of trees (`n_estimators`), maximum tree depth (`max_depth`), and minimum samples per leaf (`min_samples_leaf`). These hyperparameters control the complexity of the model and help balance predictive performance against overfitting. The best-performing hyperparameters were:

- `n_estimators = 100`
- `max_depth = 8`
- `min_samples_leaf = 10`

The final model achieved the following performance on the test set:

| Model | Accuracy | Precision | Recall | F1 |
|:---|---:|---:|---:|---:|
| Baseline Logistic Regression | 0.6262 | 0.6285 | 0.6176 | 0.6230 |
| Final Random Forest | 0.6668 | 0.6677 | 0.6639 | 0.6658 |

<iframe
  src="assets/final-confusion-matrix.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/model-comparison.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The final model improved upon the baseline model across every evaluation metric. Accuracy increased from approximately 62.6% to 66.7%, while precision, recall, and F1-score also improved. This suggests that incorporating opponent-relative statistics and engineered resource-based features provides additional predictive information beyond the raw early-game statistics used in the baseline model. The Random Forest classifier was also able to capture nonlinear relationships that the baseline Logistic Regression model could not represent.

## Fairness Analysis

We evaluate whether our final model performs differently for Blue-side and Red-side teams.

- Group X: Blue-side teams
- Group Y: Red-side teams
- Evaluation metric: accuracy

We choose accuracy because our dataset is balanced between the two classes, win and loss, and accuracy provides a straightforward measure of overall model correctness for each group.

**Null Hypothesis:** The final model is fair with respect to side. The accuracy for Blue-side and Red-side teams is approximately the same, and any observed differences are due to random chance.

**Alternative Hypothesis:** The final model is unfair with respect to side. The accuracy for Blue-side teams differs from the accuracy for Red-side teams.

**Test Statistic:** Absolute difference in accuracy between Blue-side and Red-side teams.

**Significance Level:** 0.05.

The observed difference in accuracy was approximately **0.0019**, indicating nearly identical performance across both groups. We performed a permutation test by randomly shuffling the side labels and recomputing the test statistic under the null hypothesis. The resulting p-value was **0.887**.

<iframe
  src="assets/fairness-side-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Since the p-value is much larger than 0.05, we fail to reject the null hypothesis. There is not enough evidence to conclude that the model performs differently for Blue-side and Red-side teams. This suggests that the model's predictions are similar across both groups with respect to side.
