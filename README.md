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



## Assessment of Missingness

## Hypothesis Testing

## Framing a Prediction Problem

### Prediction Problem

I plan to predict whether a team wins or loses a match using team-level in-game statistics. This is a binary classification problem because the target column, result, represents either a win or a loss.

## Baseline Model

## Final Model

## Fairness Analysis
