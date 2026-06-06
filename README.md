# Blue Side Advantage in Professional League of Legends

Authors: Nhan Doan, Sean Liu

## Introduction

### General Introduction

League of Legends (LoL) is a popular Multiplayer Online Battle Arena (MOBA) game developed by Riot Games. It has become one of the largest video games worldwide and supports a highly competitive professional esports scene. The dataset used in this project is sourced from Oracle’s Elixir and contains professional match data from 2014 through 2026. The we will be using the recorded match data from professional League of Legends esports matches throughout 2022.

### Dataset Size

The raw dataset contains 150348 rows of professional League of Legends match data from Oracle’s Elixir. Each actual esports match consists of up to 12 rows, corresponding to 10 player entries and 2 team entries (one per side) for that game.

After filtering to retain only team level observations (Blue and Red sides) and removing invalid or incomplete entries, the final dataset contains 25058 rows, representing both teams for each match (2 rows per game).

### Research Question

A commonly held belief among League of Legends players is that the Blue side provides a competitive advantage due to draft order and map orientation advantages, particularly around neutral objectives.

**Question: Does blue side provide a competitive advantage in professional League of Legends matches?**

Understanding whether Blue side confers a competitive advantage is important for evaluating fairness in professional League of Legends. If a systematic side bias exists, it could influence drafting strategies, tournament design, and how analysts interpret match outcomes. Quantifying this effect provides a data-driven perspective on whether observed win rate differences reflect true skill differences or structural advantages.

### Columns of Interest

We focus on the following variables:
- gameid: unique identifier for each match
- league: professional league where the match was played
- side: whether the team played on Blue or Red side
- result: match outcome (1 = win, 0 = loss)
- goldat10: team gold at 10 minutes into a game
- xpat10: team experience at 10 minutes into a game
- csat10: team creep score at 10 minutes
- golddiffat10: team gold differential at 10 minutes in a game
- xpdiffat10: team experience differential at 10 minutes in a game
- csdiffat10: team creep score differential at 10 minutes in a game

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

The Oracle's Elixir dataset contains both player-level and team-level observations. Since our research question concerns whether Blue side provides an advantage at the match level, we first filtered the dataset to retain only rows where `position == "team"`. This ensures that each observation represents a team's overall performance in a match rather than an individual player's statistics.

We then removed observations whose side was not recorded as either Blue or Red and removed rows whose match result was not recorded as either a win (`1`) or loss (`0`). These filtering steps ensure that all observations correspond to completed professional matches with clearly defined outcomes.

We also created two derived variables: `result_label`, which converts match outcomes from numerical values into the more interpretable labels "Win" and "Loss", and `is_blue`, a binary indicator that equals 1 when a team played on Blue side and 0 otherwise. These derived variables simplify later visualization and modeling tasks.

After cleaning, the dataset contains 196320 team-level observations representing both sides of 98160 professional matches.

| gameid                | league   | side   |   result |   goldat10 |   xpat10 |   csat10 |   golddiffat10 |   xpdiffat10 |   csdiffat10 |
|:----------------------|:---------|:-------|---------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|
| ESPORTSTMNT01_2690210 | LCKC     | Blue   |        0 |      16218 |    18213 |      322 |           1523 |          137 |           -8 |
| ESPORTSTMNT01_2690210 | LCKC     | Red    |        1 |      14695 |    18076 |      330 |          -1523 |         -137 |            8 |
| ESPORTSTMNT01_2690219 | LCKC     | Blue   |        0 |      14939 |    17462 |      317 |          -1619 |        -1586 |          -27 |
| ESPORTSTMNT01_2690219 | LCKC     | Red    |        1 |      16558 |    19048 |      344 |           1619 |         1586 |           27 |
| 8401-8401_game_1      | LPL      | Blue   |        1 |        nan |      nan |      nan |            nan |          nan |          nan |

*Note: The DataFrame above is not the full DataFrame. Only the first 5 rows are shown for reference.

### Univariate Analysis

We permformed univariate analysis on the occurance of either side in the dataset

<iframe
  src="assets/side-counts.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This bar plot shows the number of team-level observations for the Blue and Red sides in the dataset is equal. This is expected since each professional match contributes one Blue-side and one Red-side observation.

We also plot a graph for the dirstribution of team gold at the 10 minutes (`goldat10`) in the data set.

<iframe
  src="assets/gold-at-10-distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The histogram is mostly normal, but slightly skewed to the right, meaning the data is well-behaved. These higher values can be interpreted as early advantages gained through successful skirmishes, objective control, or lane dominance. This shape is reasonable in a competitive setting like professional League of Legends, where early-game gold tends to be relatively stable across most matches, but occasionally diverges when early fights or strategic advantages create larger-than-average leads.

### Bivariate Analysis

We permformed bivariate analysis on the side and result statistic in the database to visualize how the winrates of both sides.

<iframe
  src="assets/win-rate-by-side.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>>

As shown in the bar plot, the Blue side has around a 53.2% winrate. This corresponds to a 3.2% deviation from an equal 50–50 split. This suggests that Blue side teams win slightly more often in the observed data. This pattern is consistent with the hypothesis that Blue side may confer a competitive advantage, though further statistical testing is needed to determine whether this difference is statistically significant.

We also performed a bivariate analysis on the gold differential at 10 miuntes for winning and losing teams.

<iframe
  src="assets/gold-diff-at-10-by-result.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The box and whisker plots show that winning teams tend to have higher 10-minute gold differences compared to losing teams, which is consistent with the idea that early-game advantages are associated with match success in professional League of Legends. However, there is noticeable overlap between the two distributions, indicating that a small positive gold differential at 10 minutes is not sufficient on its own to reliably predict a win. This reflects the fact that early-game gold differences are often still relatively small at this stage of the game, and outcomes can still change significantly in later phases.

### Interesting Aggregate

Here are some intersting aggregates to invest within the data set.

| side   |   win_rate |   goldat10 |   xpat10 |   csat10 |   golddiffat10 |   xpdiffat10 |   csdiffat10 |
|:-------|-----------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|
| Blue   |      0.525 |    15733.2 |  18211.9 |  314.679 |         88.156 |       24.639 |        0.344 |
| Red    |      0.475 |    15645   |  18187.2 |  314.335 |        -88.156 |      -24.639 |       -0.344 |

The table compares average early-game statistics and win rates between Blue and Red sides. We observe that Blue side has a higher average win rate (0.525 vs 0.475), consistent with earlier visualizations suggesting a modest advantage for Blue side teams.

In addition, Blue side teams tend to have slightly higher early-game resource values, including gold, experience, and creep score at 10 minutes. The corresponding “difference” features reflect this directly, with Blue side showing positive average differentials and Red side showing symmetric negative values, as expected from the paired structure of match data.

Overall, this table reinforces the pattern that Blue side teams perform slightly better on average in both early-game metrics and match outcomes, though the magnitude of the differences in early-game statistics is relatively small.

## Assessment of Missingness

### NMAR Analysis

We believe that no column in this dataset is clearly NMAR, but some missingness patterns may appear NMAR at first glance. For example, missing values in first herald-related gameplay statistics may seem dependent on the quality or completeness of match tracking, which is not directly observed in the dataset. However, this missingness is more likely MAR, because it could be explained by additional observed metadata such as match tracking source, tournament coverage level, or patch version. If we had access to metadata such as whether a match was fully recorded, broadcasted, or processed by a specific data provider, we could better explain the missingness mechanism and potentially convert this from “apparently NMAR” to MAR.

### Missingness Dependency

To test for missingness dependency in our dataset, we selected the `firstherald` column for analysis. We then examined whether its missingness depends on `league` and `side` using permutation tests. The hypotheses for each test are below. The significance level we choose for both permutation tests is 0.05, and the test statistic is Total Variance Distance (TVD).

First, we test whether the missingness of `firstherald` depend on `league`?

**Null Hypothesis:** The distribution of `league` is the same when `firstherald` is missing and when it is not missing.

**Alternative Hypothesis:** The distribution of `league` is different when `firstherald` is missing compared to when it is not missing.

The observed statistic for this permutation test is 0.9925847457627119 and a p-value of 0.0.

<iframe
  src="assets/missingness-league-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Since the p-value is below the selected 0.05 significance level, we reject the null hypothesis. Thus, the missingness of `firstherald` depends on the `league` column.

Second, we test whether the missingness of `firstherald` depend on `side`?

**Null Hypothesis:** The distribution of `side` is the same when `firstherald` is missing and when it is not missing.

**Alternative Hypothesis:** The distribution of `side` is different when `firstherald` is missing compared to when it is not missing.

The observed statistic for this permutation test is 0.0 and a p-value of 1.0.

<iframe
  src="assets/missingness-side-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Since the p-value is above the selected 0.05 significance level, we fail to reject the null hypothesis. Thus, there is no evidence that the missingness of `firstherald` depends on the `side` column.

## Hypothesis Testing

We investigate whether Blue-side teams win more often than would be expected under a fair 50/50 model.

**Null Hypothesis:** Blue-side teams win 50% of games on average.

**Alternative Hypothesis:** Blue-side teams win more than 50% of games on average.

**Test Statistic:** Blue-side win rate minus 0.5

**Significance Level:** 0.05

Below is a histogram of the distribution of our test statistics during the hypothesis test

<iframe
  src="assets/blue-side-hypothesis-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We have an observed Blue-side win rate of 0.5247825045893527, which also gives us an observed test statistic of 0.0247825045893527. The p-value from the simulation is approximately 0 (extremely small), indicating that such an extreme result is very unlikely under the null hypothesis.

Since the p-value is lower than the significance level of 0.05, we reject the null hypothesis. There is statistically significant evidence that Blue-side teams win more than would be expected under a fair 50% win rate model.

## Framing a Prediction Problem

Our prediction problem is:

**Can we predict whether a team wins professional League of Legends match using information available at 10 minutes?**

The response variable is `result`, where a value of 1 indicates a win and a value of 0 indicates a loss. This is a binary classification problem because there are only two possible outcomes: win or loss. We chose this prediction problem because early-game advantages are often important indicators of match outcomes in professional League of Legends. Predicting the winner using only information available at 10 minutes allows us to investigate how much predictive power early-game performance has before many major game-defining events occur.

To avoid data leakage, we only use information that would be known at approximately 10 minutes into a match. The features used in our models are `side`, `league`, `goldat10`, `xpat10`, `csat10`, `golddiffat10`, `xpdiffat10`, and `csdiffat10`. These variables capture a team's resources and relative advantages during the early stages of the game. We do not use final match statistics such as total `kills`, `dragons`, `barons`, `towers`, or other post-game information because those values would not be available at the time the prediction is made. Below is the head of the dataframe we will be using:

| side   | league   |   goldat10 |   xpat10 |   csat10 |   golddiffat10 |   xpdiffat10 |   csdiffat10 |   result |
|:-------|:---------|-----------:|---------:|---------:|---------------:|-------------:|-------------:|---------:|
| Blue   | LCKC     |      16218 |    18213 |      322 |           1523 |          137 |           -8 |        0 |
| Red    | LCKC     |      14695 |    18076 |      330 |          -1523 |         -137 |            8 |        1 |
| Blue   | LCKC     |      14939 |    17462 |      317 |          -1619 |        -1586 |          -27 |        0 |
| Red    | LCKC     |      16558 |    19048 |      344 |           1619 |         1586 |           27 |        1 |
| Blue   | LPL      |        nan |      nan |      nan |            nan |          nan |          nan |        1 |

For computational efficiency, we restrict the modeling dataset to matches played during 2025. After filtering the data, our modeling dataset contains 20306 observations. We split the data into training and testing sets, using 75% of the observations for training and 25% for testing. We evaluate our models primarily using accuracy. Since the dataset is perfectly balanced, with equal numbers of wins and losses, accuracy provides an intuitive measure of how often the model correctly predicts match outcomes. We also report precision, recall, and F1-score in later sections to provide additional insight into model performance, but accuracy is our primary evaluation metric because class imbalance is not a significant concern in this prediction task.

## Baseline Model

Compared to random guessing on a balanced dataset, which would achieve approximately 50% accuracy, the baseline model performs noticeably better. This suggests that early-game information contains meaningful predictive signal regarding match outcomes. However, the model is far from perfectly accurate, which is expected because many important events that influence the outcome of a match occur after the 10-minute mark. Therefore, while the baseline model provides a useful starting point, there is still substantial room for improvement through additional features and model tuning.

Our baseline model is a Logistic Regression classifier that uses simple early-game information available at approximately 10 minutes into a match.

The model uses four features:
- Quantitative features: `goldat10`, `xpat10`, and `csat10`
- Nominal feature: `side`

For the quantitative features, we first impute missing values using the median and then standardize the features using StandardScaler. We use median imputation because it is robust to outliers and allows us to retain observations with missing values. For the nominal feature side, we impute missing values using the most frequent category and then apply one-hot encoding so that the model can work with categorical data. All preprocessing steps and model training are implemented in a single sklearn Pipeline.

The baseline model achieved the following performance on the test set:

| Metric | Value |
|:--------|--------------------:|
| Accuracy | 0.6262368337057134 |
| Precision | 0.6262368337057134 |
| Recall | 0.6176188956271944 |
| F1-Score | 0.6229877656149388 |

<iframe
  src="assets/baseline-confusion-matrix.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Compared to random guessing on a balanced dataset, which would achieve approximately 50% accuracy, the baseline model performs noticeably better. This suggests that early-game information contains meaningful predictive signal regarding match outcomes. However, the model is far from perfectly accurate, which is expected because many important events that influence the outcome of a match occur after the 10-minute mark. Therefore, while the baseline model provides a useful starting point, there is still substantial room for improvement through additional features and model tuning.

## Final Model

For our final model, we expanded upon the baseline model by incorporating additional early-game information and engineering new features that better capture a team's relative advantage over its opponent. We also replaced Logistic Regression with a Random Forest classifier to allow the model to capture nonlinear relationships between early-game performance and match outcomes.

The final model uses the following features:
- Quantitative features: `goldat10`, `xpat10`, `csat10`, `golddiffat10`, `xpdiffat10`, and `csdiffat10`
- Nominal features: `side` and `league`

In addition, we engineered three new features:
- `overall_resource_at10`: which combines gold, experience, and creep score at 10 minutes.
- `overall_diff_at10`: which combines gold, experience, and creep score differentials at 10 minutes.
- `positive_gold_diff`: which indicates whether a team was ahead in gold at the 10-minute mark.

These engineered features were chosen because League of Legends is fundamentally a game of resource accumulation and relative advantages. While raw gold, experience, and creep score provide information about a team's overall strength, the corresponding differential statistics more directly capture whether a team is outperforming its opponent. The engineered features summarize these relationships and provide the model with a more complete view of early-game advantages.

All preprocessing, feature engineering, and model training were implemented within a single sklearn Pipeline. Quantitative features were median-imputed to handle missing values, while nominal features were imputed using the most frequent category and one-hot encoded.

To improve model performance, we used GridSearchCV with 5-fold cross-validation to tune the Random Forest hyperparameters. We searched over the number of trees (`n_estimators`), maximum tree depth (`max_depth`), and minimum samples per leaf (`min_samples_leaf`). These hyperparameters control the complexity of the model and help balance predictive performance against overfitting. The best-performing hyperparameters were:

- n_estimators = 100
- max_depth = 8
- min_samples_leaf = 10

The final model achieved the following performance on the test set:

| Model                        |   Accuracy |   Precision |   Recall |     F1 |
|:-----------------------------|-----------:|------------:|---------:|-------:|
| Baseline Logistic Regression |     0.6262 |      0.6285 |   0.6176 | 0.6230  |
| Final Random Forest          |     0.6666 |      0.6676 |   0.6636 | 0.6656 |

<iframe
  src="assets/final-confusion-matrix.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The final model improved upon the baseline model across every evaluation metric. Accuracy increased from approximately 62.6% to 66.7%, while precision, recall, and F1-score also improved. This suggests that incorporating opponent-relative statistics and engineered resource-based features provides additional predictive information beyond the raw early-game statistics used in the baseline model. The Random Forest classifier was also able to capture nonlinear relationships that the baseline Logistic Regression model could not represent.

## Fairness Analysis

We evaluate whether our final model performs differently for Blue-side and Red-side teams.
- Group X: Blue-side teams
- Group Y: Red-side teams
- Evaluation metric: Accuracy (fraction of correct predictions)

We choose accuracy because our dataset is balanced between the two classes (win/loss), and accuracy provides a straightforward measure of overall model correctness for each group.

**Null Hypothesis**: The final model is fair with respect to side. The accuracy for Blue-side and Red-side teams is the same, and any observed differences are due to random chance.

**Alternative Hypothesis**: The final model is unfair with respect to side. The accuracy for Blue-side teams differs from the accuracy for Red-side teams.

**Test Statistic**： We use the absolute difference in accuracy between Blue-side and Red-side teams: |Accuracy of Blue - Accuracy of Red|

**Significance Level**: 0.05

### Results

The observed difference in accuracy was approximately 0.0016, indicating nearly identical performance across both groups. We perform a permutation test by randomly shuffling the side labels and recomputing the test statistic under the null hypothesis. The resulting p-value is:
- p-value = 0.907
<iframe
  src="assets/fairness-side-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Conclusion

Since the p-value is much larger than 0.05, we fail to reject the null hypothesis. There is not enough evidence to conclude that the model performs differently for Blue-side and Red-side teams. This suggests that the model’s predictions are consistent across both groups with respect to side.
