<h1>League of Legends Cash Money (I wouldn't recommend this game)</h1>

<h2>Introduction</h2>

On Oracle's Elixer's website, there is a dataset of match data surrounding professional leagues in the game "League of Legends". As two people who avidly love videogames, we were interested in seeing the patterns that could be drawn from this dataset. When analyzing the dataset at a surface level, we found ourselvews interested primarily in the gold "economy" of a League of Legends game. As a result, we posed the question:

Does the amount of gold earned over the course of a game have an effect on whether or not the team wins the game?

This question is interesting, as League of Legends is one of the most popular e-sports games. Furthermore, gold allows characters to buy items that make their champions stronger. As a result, its an important resource in the game. However, some "roles" that people play can gain different amounts of gold. Supporting roles are incredibly important to the game, being vital to any fight they are a part of, yet they earn less gold than other roles.

Within this dataset, there are 61572 rows and 161 columns. Out of these columns, a few columns are especially important to us. The columns `champion`, `earnedgold`, `kills`, `barons`, and `towers` are notable in exploring our question.

- The `champion` column describes the "champion" or character of a player.
- The `earnedgold` column describes the total gold that a player earns throughout the game.
- The `kills` column describes the number of kills that a player gets throughout the game. Kills are a major source of gold throughout a game.
- The `barons` column describes the number of "barons" (a major non-player enemy found throughout the game's map) that a player kills throughout the game. Killing barons give gold, making this important to the gold economy in a game.
- The `towers` column describes the number of "towers" (a major obstacle that separates the players from the game's main objective) that a player destroys throughout the game. Like barons, destroying towers gives gold, while also contributing to the progress of a game.
- The `visionscore` column describes the number of times an enemy player walked into a "ward" placed down by a player.


<h2>Data Cleaning and Exploratory Data Analysis</h2>

<h3>Data Cleaning</h3>

Our dataset was already mostly clean, so in order to clean our data, we mostly focussed on three aspects of our dataset.

- We started by noticing how some columns seemed to be boolean values as integers. In order to make the dataset more efficient to work with, we started by searching for columns that contained only 0 and 1, and replaced these columns with boolean values.
- Afterwards, we removed the rows that had "incomplete" data.
- The last thing to fix was the fact that some rows focussed on team summaries, rather than individual players. Instead of removing it entirely, we decided to use two separate methods, one to generate a cleaned dataset of team summaries, and one method to generate a cleaned dataset of player data.

```python
def data_cleaning_summaries():
    data = pd.read_csv('LoLData.csv')

    boolCols = data.columns[data.isin([0, 1, np.nan]).all()]
    data[boolCols] = data[boolCols].replace({0: False, 1: True})

    return data[(data.champion.isna()) & (data.datacompleteness == 'complete')].dropna(axis=1)

def data_cleaning_players():
    data = pd.read_csv('LoLData.csv')

    boolCols = data.columns[data.isin([0, 1, np.nan]).all()]
    data[boolCols] = data[boolCols].replace({0: False, 1: True})

    return data[(~data.champion.isna()) & (data.datacompleteness == 'complete')].dropna(axis=1)
```

Team Summaries:

<sub>(Vertically and Horizontally truncated for readability)</sub>

| gameid           | datacompleteness   | league   |   year | playoffs   | date                |   game |   patch |   participantid | side   | position   | teamname                |   gamelength | result   |   kills |
|:-----------------|:-------------------|:---------|-------:|:-----------|:--------------------|-------:|--------:|----------------:|:-------|:-----------|:------------------------|-------------:|:---------|--------:|
| LOLTMNT03_179647 | complete           | LFL2     |   2025 | False      | 2025-01-11 11:11:24 |      1 |   15.01 |             100 | Blue   | team       | IziDream                |         1592 | False    |       3 |
| LOLTMNT03_179647 | complete           | LFL2     |   2025 | False      | 2025-01-11 11:11:24 |      1 |   15.01 |             200 | Red    | team       | Team Valiant            |         1592 | True     |      13 |
| LOLTMNT06_96134  | complete           | LFL2     |   2025 | False      | 2025-01-11 12:06:37 |      1 |   15.01 |             100 | Blue   | team       | Esprit Shōnen           |         1922 | True     |      21 |
| LOLTMNT06_96134  | complete           | LFL2     |   2025 | False      | 2025-01-11 12:06:37 |      1 |   15.01 |             200 | Red    | team       | Skillcamp               |         1922 | False    |      10 |
| LOLTMNT06_95160  | complete           | LFL2     |   2025 | False      | 2025-01-11 13:07:47 |      1 |   15.01 |             100 | Blue   | team       | Karmine Corp Blue Stars |         1782 | False    |      18 |

Players:

<sub>(Vertically and Horizontally truncated for readability)</sub>

| gameid           | datacompleteness   | league   |   year | playoffs   | date                |   game |   patch |   participantid | side   | position   | teamname   | champion   |   gamelength | result   |
|:-----------------|:-------------------|:---------|-------:|:-----------|:--------------------|-------:|--------:|----------------:|:-------|:-----------|:-----------|:-----------|-------------:|:---------|
| LOLTMNT03_179647 | complete           | LFL2     |   2025 | False      | 2025-01-11 11:11:24 |      1 |   15.01 |               1 | Blue   | top        | IziDream   | Gnar       |         1592 | False    |
| LOLTMNT03_179647 | complete           | LFL2     |   2025 | False      | 2025-01-11 11:11:24 |      1 |   15.01 |               2 | Blue   | jng        | IziDream   | Maokai     |         1592 | False    |
| LOLTMNT03_179647 | complete           | LFL2     |   2025 | False      | 2025-01-11 11:11:24 |      1 |   15.01 |               3 | Blue   | mid        | IziDream   | Hwei       |         1592 | False    |
| LOLTMNT03_179647 | complete           | LFL2     |   2025 | False      | 2025-01-11 11:11:24 |      1 |   15.01 |               4 | Blue   | bot        | IziDream   | Jinx       |         1592 | False    |
| LOLTMNT03_179647 | complete           | LFL2     |   2025 | False      | 2025-01-11 11:11:24 |      1 |   15.01 |               5 | Blue   | sup        | IziDream   | Leona      |         1592 | False    |

<h3>Univariate Analysis</h3>

<iframe
  src="assets/GoldDist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

When examining the distribution of gold, we can see that there is a somewhat normal trend, where most players average 7000-8000 gold per game. This histogram is also skewed right, with some outliers existing past 20,000.

<iframe
  src="assets/KillDist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

When examining the distribution of kills, we can see that there is a downward trend, where the mode of the kills is 1 kill per game, with increasing amounts of kills becoming less common as the amount of kills increase.

<h3>Bivariate Analysis</h3>

<iframe
  src="assets/GoldDistPerChamp.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Because there are 170 champions in league, to visualize the gold earned by each champion and retain readability, we randomly selected 10 champions. Regardless, this graph shows the clear differences between champions and how much gold they earn, as characters like Lulu usually earn significantly less gold than characters like Kayle. 

<iframe
  src="assets/RandChampAvgKills.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Once again, to retain readability in this count plot, we randomly selected 25 champions. This plot once again shows the differences between the average champion stats. Characters like Master Yi outperform characters like Yuumi by very large margins, which would greatly contribute to the gold earned.

<h3>Interesting Aggregates</h3>

<sub>(Vertically and Horizontally truncated for readability)</sub>

| champion   |   100 Thieves |   5M Esports |   A One Man Army |   A One Man Army Prime |   A One Man Army Prime Academy |   AKA HERO |   Actions Per Minute |   All for One Gaming |   Ankora Gaming |   Anubis Gaming |   Anyone's Legend |
|:-----------|--------------:|-------------:|-----------------:|-----------------------:|-------------------------------:|-----------:|---------------------:|---------------------:|----------------:|----------------:|------------------:|
| Aatrox     |       8795.5  |          nan |          6955    |                   3097 |                            nan |        nan |              6211.25 |                  nan |             nan |         4808    |           8024    |
| Ahri       |       9062    |         9141 |          7159    |                    nan |                            nan |        nan |              8599    |                 6441 |            5327 |        10001.8  |           6824    |
| Akali      |      10404.5  |          nan |           nan    |                    nan |                            nan |        nan |              9643    |                  nan |             nan |         7458    |           9675.17 |
| Akshan     |        nan    |          nan |           nan    |                    nan |                            nan |        nan |               nan    |                  nan |             nan |          nan    |            nan    |
| Alistar    |       3928.33 |          nan |          3549.86 |                    nan |                            nan |        nan |              3833    |                  nan |             nan |         3731.71 |           4088.25 |

Within this table, we can see the averaged gold earned by specific characters used in specific teams. This table notably shows that teams perform differently with each character. For example, Ankora Gaming and Anubis Gaming both have used the champion "Ahri". However, we can see that Anubis Gaming usually earns almost double the amount of gold that Ankora Gaming would typically earn. As a result, we can see that characters have an impact on gold earnings, but there is a lot more than just the characters themselves. 


<h2>Assessment of Missingness</h2>

<h3>NMAR Analysis</h3>

In our dataset, there are many columns with missing values. However, when observed more carefully, we can see that this is a result of how the data was collected. For each unique `gameid`, we can see that there are 12 corresponding rows. 10 of these are for each team of 5 players, while 2 rows exist as summary data for each team. Because they are summaries for the entire team, they ultimately interact with columns differently, making those columns MD. This makes intuitive sense, as an entire team cannot play as a single character, meaning that the summary's `champion` column will be missing. This pattern continues for other significant columns, for example, only one player in a game can get a 'first blood' kill in a game. In the game, this gives bonus gold to that player, and as a result of it being focused around one player, the value will be missing in a team's summary. There are also other missing columns, shown by the `datacompleteness` column. These rows are missing data, but when we sort by their `league`, we can see that this data is MAR, as the "partially" complete data is only present in the "LPL" league. Because of this, it seems that none of our data is NMAR, as every missing data point can be described as either MAR or MD.

<h3>Missingness Dependency</h3>

To check for the dependency of the missingness, we used permutation tests while starting with the null hypothesis: "The missingness of X does not depend on the value of Y" and the alternative hypothesis: "The missingness of X does depend on the value of Y", where X and Y were two columns of the dataset.

After experimenting with different columns, we tested to see if the missingness of `doublekill` depended on the value of `league`. We found a p-value of 0. This showed that we could accept our null hypothesis, showing that the missingness of `doublekill` depended on the value of `league`. This also correlated to what we found in our NMAR analysis.

We then tried seeing if the missingness of `ban3` depended on the value of `playerid`. We found a p-value of 0.181. This showed that we failed to reject our null hypothesis, showing that the missingness of `ban3` was not dependent on the value of `playerid`. 

<iframe
  src="assets/EmpKSStat.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

When graphed, we can see that the observed Kolmogorov–Smirnov statistic is far greater than any of the permuted statistics. This shows why we consistently obtaina  0 

<h3>Hypothesis Testing</h3>

Null Hypothesis: The amount of gold earned over the course of a game DOES NOT have an effect on whether or not the team wins the game.

Alternative Hypothesis: The amount of gold earned over the course of a game DOES have an effect on whether or not the team wins the game.

When performing our permutation testing, we started by using the difference in average gold depending on whether they won or lost as our test statistic. We obtained a p-value of 0.0, and since we used a significance level of 0.05, this implied that we should reject the null hypothesis.

Ultimately, these choices were useful in answering our question, as it used the `earnedgold` column's mean, along with a permutation test, to see if there was really a correlation between the distribution of `earnedgold` and the distribution of a game's results.

<h3>Framing a Prediction Problem</h3>

<h3>Baseline Model</h3>

<h3>Final Model</h3>

<h3>Fairness Analysis</h3>
