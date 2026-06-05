# Blue Side Advantage in Professional League of Legends

Authors: Nhan Doan, Sean Liu

## Introduction

### General Introduction

League of Legends (LoL) is a popular Multiplayer Online Battle Arena (MOBA) game developed by Riot Games. It has become one of the largest video games worldwide and supports a highly competitive professional esports scene. The dataset used in this project is sourced from Oracle’s Elixir and contains professional match data from 2014 through 2026.

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
  src="assets/side-counts.html"
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
><\iframe>

As shown in the bar plot, the Blue side has around a 53.2% winrate. This corresponds to a 3.2% deviation from an equal 50–50 split. This suggests that Blue side teams win slightly more often in the observed data. This pattern is consistent with the hypothesis that Blue side may confer a competitive advantage, though further statistical testing is needed to determine whether this difference is statistically significant.

We also performed a bivariate analysis on the gold differential at 10 miuntes for winning and losing teams.

<iframe
  src="assets/gold-diff-at-10-by-result.html"
  width="800"
  height="600"
  frameborder="0"
><\iframe>

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

## Hypothesis Testing

## Framing a Prediction Problem

### Prediction Problem

I plan to predict whether a team wins or loses a match using team-level in-game statistics. This is a binary classification problem because the target column, result, represents either a win or a loss.

## Baseline Model

## Final Model

## Fairness Analysis
