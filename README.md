# Blue Side Advantage in Professional League of Legends

Authors: Nhan Doan, Sean Liu

## Introduction

### General Introduction

League of Legends (LoL) is a popular Multiplayer Online Battle Arena (MOBA) game developed by Riot Games. It has become one of the largest video games worldwide and supports a highly competitive professional esports scene. The dataset used in this project is sourced from Oracle’s Elixir and contains professional match data from 2014 through 2026. The 2026 game data is snapshot on May 21st, 2026, meaning games after this date were not included.

### Dataset Size

The raw dataset contains approximately 1177920 rows of professional League of Legends match data from Oracle’s Elixir. Each match consists of up to 12 rows, corresponding to 10 player entries and 2 team entries (one per side), which corresponds to 98160 matches.

After filtering to retain only team level observations (Blue and Red sides) and removing invalid or incomplete entries, the final dataset contains 196320 rows, representing both teams for each match (2 rows per game).

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

| gameid   | league   | side   |   result |   goldat10 |   xpat10 |   csat10 |   golddiffat10 |   xpdiffat10 |   csdiffat10 |
|:---------|:---------|:-------|---------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|
| TRLH3/33 | EU LCS   | Blue   |        1 |      13851 |    17193 |      245 |           -119 |         -463 |           -4 |
| TRLH3/33 | EU LCS   | Red    |        0 |      13970 |    17656 |      249 |            119 |          463 |            4 |
| TRLH3/44 | EU LCS   | Blue   |        1 |      13792 |    18711 |      263 |           -179 |         -458 |            7 |
| TRLH3/44 | EU LCS   | Red    |        0 |      13971 |    19169 |      256 |            179 |          458 |           -7 |
| TRLH3/76 | EU LCS   | Blue   |        0 |      13923 |    18814 |      291 |            847 |         1271 |           22 |

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
| Blue   |      0.532 |    15712.8 |  18474.8 |  315.499 |        110.259 |       47.279 |         1.31 |
| Red    |      0.468 |    15602.5 |  18427.5 |  314.189 |       -110.259 |      -47.279 |        -1.31 |

The table compares average early-game statistics and win rates between Blue and Red sides. We observe that Blue side has a higher average win rate (0.532 vs 0.468), consistent with earlier visualizations suggesting a modest advantage for Blue side teams.

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

The observed statistic for this permutation test is 0.8494627571515647 and a p-value of 0.0.

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

We have an observed Blue-side win rate of 0.5317339038304808, which also gives us an observed test statistic of 0.0317339038304808. The p-value from the simulation is approximately 0 (extremely small), indicating that such an extreme result is very unlikely under the null hypothesis.

Since the p-value is lower than the significance level of 0.05, we reject the null hypothesis. There is statistically significant evidence that Blue-side teams win more than would be expected under a fair 50% win rate model.

## Framing a Prediction Problem

Our prediction problem is:

**Can we predict whether a team wins a professional League of Legends match using information available at 10 minutes?**

The response variable is `result`, where a value of 1 indicates a win and a value of 0 indicates a loss. This is a binary classification problem because there are only two possible outcomes: win or loss. We chose this prediction problem because early-game advantages are often important indicators of match outcomes in professional League of Legends. Predicting the winner using only information available at 10 minutes allows us to investigate how much predictive power early-game performance has before many major game-defining events occur.

To avoid data leakage, we only use information that would be known at approximately 10 minutes into a match. The features used in our models are `side`, `league`, `goldat10`, `xpat10`, `csat10`, `golddiffat10`, `xpdiffat10`, and `csdiffat10`. These variables capture a team's resources and relative advantages during the early stages of the game. We do not use final match statistics such as total `kills`, `dragons`, `barons`, `towers`, or other post-game information because those values would not be available at the time the prediction is made.

For computational efficiency, we restrict the modeling dataset to matches played during 2025. After filtering the data, our modeling dataset contains 20306 observations. We split the data into training and testing sets, using 75% of the observations for training and 25% for testing. We evaluate our models primarily using accuracy. Since the dataset is perfectly balanced, with equal numbers of wins and losses, accuracy provides an intuitive measure of how often the model correctly predicts match outcomes. We also report precision, recall, and F1-score in later sections to provide additional insight into model performance, but accuracy is our primary evaluation metric because class imbalance is not a significant concern in this prediction task.

## Baseline Model

## Final Model

## Fairness Analysis
