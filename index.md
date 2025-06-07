<h1>Is a League of Legends Gold Advantage Actually an Indicater of Winning?</h1>

Created By:
- Diego Inostroza
- Dylan Berry

<h2><ins>Introduction</ins></h2>

On Oracle's Elixer's website, there is a dataset of match data surrounding professional leagues in the game "League of Legends". As two people who avidly love videogames, we were interested in seeing the patterns that could be drawn from this dataset. When analyzing the dataset at a surface level, we found ourselvews interested primarily in the gold "economy" of a League of Legends game. As a result, we posed the question:

Does the amount of gold earned over the course of a game have an effect on whether or not the team wins the game?

This question is interesting, as League of Legends is one of the most popular e-sports games. Furthermore, gold allows characters to buy items that make their champions stronger. As a result, its an important resource in the game. However, some "roles" that people play can gain different amounts of gold. Supporting roles are incredibly important to the game, being vital to any fight they are a part of, yet they earn less gold than other roles.

Within this dataset, there are 61572 rows and 161 columns. Out of these columns, a few columns are especially important to us. The columns `earnedgold` and `result` are especially notable in exploring our question.

- The `earnedgold` column describes the total gold that a player earns throughout the game.
- The `result` column describes the outcome of the match, showing if a player or team won the match.

However, there are some other interesting columns, columns like `kills` and `champion` which are interesting to explore, as they are directly connected to how gold may be earned.

- The `champion` column describes the "champion" or character of a player. 

- The `kills` column describes the number of kills that a player gets throughout the game. Kills are a major source of gold throughout a game, so this should be connected to how much gold a player earns.

<h2><ins>Data Cleaning and Exploratory Data Analysis</ins></h2>

<h3><ins>Data Cleaning</ins></h3>

Our dataset was already mostly clean, so in order to clean our data, we mostly focussed on four aspects of our dataset.

- We started by noticing how some columns seemed to be boolean values as integers. In order to make the dataset more efficient to work with, we started by searching for columns that contained only 0 and 1, and replaced these columns with boolean values.
- Afterwards, we removed the rows that had "incomplete" data.
- We then needed to fix the fact that some rows focussed on team summaries, rather than individual players. Instead of removing it entirely, we decided to use two separate function, one to generate a cleaned dataset of team summaries, and one to generate a cleaned dataset of player data.
- Separating these rows caused the dataframes to have some columns that were entirely empty, so in order to avoid having entirely empty columns, we every column that only contained "nan" values.

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

<h3><ins>Univariate Analysis</ins></h3>

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

<h3><ins>Bivariate Analysis</ins></h3>

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

<h3><ins>Interesting Aggregates</ins></h3>

<sub>(Vertically and Horizontally truncated for readability)</sub>

| champion   |   100 Thieves |   5M Esports |   A One Man Army |   A One Man Army Prime |   A One Man Army Prime Academy |   AKA HERO |   Actions Per Minute |   All for One Gaming |   Ankora Gaming |   Anubis Gaming |   Anyone's Legend |
|:-----------|--------------:|-------------:|-----------------:|-----------------------:|-------------------------------:|-----------:|---------------------:|---------------------:|----------------:|----------------:|------------------:|
| Aatrox     |       8795.5  |          nan |          6955    |                   3097 |                            nan |        nan |              6211.25 |                  nan |             nan |         4808    |           8024    |
| Ahri       |       9062    |         9141 |          7159    |                    nan |                            nan |        nan |              8599    |                 6441 |            5327 |        10001.8  |           6824    |
| Akali      |      10404.5  |          nan |           nan    |                    nan |                            nan |        nan |              9643    |                  nan |             nan |         7458    |           9675.17 |
| Akshan     |        nan    |          nan |           nan    |                    nan |                            nan |        nan |               nan    |                  nan |             nan |          nan    |            nan    |
| Alistar    |       3928.33 |          nan |          3549.86 |                    nan |                            nan |        nan |              3833    |                  nan |             nan |         3731.71 |           4088.25 |

Within this table, we can see the averaged gold earned by specific characters used in specific teams. This table notably shows that teams perform differently with each character. For example, Ankora Gaming and Anubis Gaming both have used the champion "Ahri". However, we can see that Anubis Gaming usually earns almost double the amount of gold that Ankora Gaming would typically earn. As a result, we can see that characters have an impact on gold earnings, but there is a lot more than just the characters themselves. 


<h2><ins>Assessment of Missingness</ins></h2>

<h3><ins>NMAR Analysis</ins></h3>

In our dataset, there are many columns with missing values. However, when observed more carefully, we can see that this is a result of how the data was collected. For each unique `gameid`, we can see that there are 12 corresponding rows. 10 of these are for each team of 5 players, while 2 rows exist as summary data for each team. Because they are summaries for the entire team, they ultimately interact with columns differently, making those columns MD. This makes intuitive sense, as an entire team cannot play as a single character, meaning that the summary's `champion` column will be missing. This pattern continues for other significant columns, for example, only one player in a game can get a 'first blood' kill in a game. In the game, this gives bonus gold to that player, and as a result of it being focused around one player, the value will be missing in a team's summary. There are also other missing columns, shown by the `datacompleteness` column. These rows are missing data, but when we sort by their `league`, we can see that this data is MAR, as the "partially" complete data is only present in the "LPL" league. Because of this, it seems that none of our data is NMAR, as every missing data point can be described as either MAR or MD.

<h3><ins>Missingness Dependency</ins></h3>

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

<h2><ins>Hypothesis Testing</ins></h2>

- Null Hypothesis: The amount of gold earned over the course of a game DOES NOT have an effect on whether or not the team wins the game.

- Alternative Hypothesis: The amount of gold earned over the course of a game DOES have an effect on whether or not the team wins the game.

When performing our permutation testing, we started by using the difference in average gold depending on whether they won or lost as our test statistic. We obtained a p-value of 0.0, and since we used a significance level of 0.05, this implied that we should reject the null hypothesis. As a result, this shows the amount of gold earned the course of a game likely affects whether or not the team wins the game.

Ultimately, these choices were useful in answering our question, as it used the `earnedgold` column's mean, along with a permutation test, to see if there was really a correlation between the distribution of `earnedgold` and the distribution of a game's results.

<h2><ins>Framing a Prediction Problem</ins></h2>

With this project, we will be predicting how much gold is earned at the end of a game of League of Legends. There are many moving parts to this, and as such, we went through a lot of the columns in the data set to determine the best fit. This is a regression problem, and we picked it because it seemed like the most interesting value to predict. Also, gold is arguably the most important resource in League of Legends, and thus has the most value in predicting. To assess the accuracy of our model, we went with Mean Squared Error, because we are doing linear regression, so MSE makes the most sense to use in the scope of our project. All our features are available at any given time over the course of a game, but the total gold earned is only revealed at the end. As such, we would have available to us all of the information we need to effectively and concisely predict gold.

There is a column in the data set that could be used to predict the amount of gold earned quite effectively, with the column in question being gold spent. However, we thought that using the amount of gold spent would not be helpful with predicting due to the 2 values (gold spent, gold earned) being directly connected. It would ruin the prediction, and that is why we decided to not use the other columns that reference gold.

<h2><ins>Baseline Model</ins></h2>

So, the model we decided to start with is a simple linear regression model. There was no adjustment done to the data, and it was not preprocessed in any way. We just threw the ‘result’, ‘kills’, and ‘monsterkills’ columns into a basic linear regression model from scikit-learn, and then tested on a train-test split to ensure the data generalised well. These columns we chose represent whether or not the team won the game, how many kills the team got over the course of the game, and how many monsters in the jungle were killed by each team. We calculated the mean squared error, the r squared value, and the average absolute error. On average, we were getting a MSE of 12 million, an r-squared of .84, and an average absolute error of 2700.

The MSE is a bit misleading, because it is large enough to warrant concern, but it is only that large because of the amount of data we have. The MSE on its own is not a great indicator of how good our model may or may not predict certain values, but the r-squared showing a decently high correlation, and the average absolute error only being 2700 pieces away is pretty good. In a game of league of legends, where there can be 15 to 40 thousand pieces of gold earned over the course of a game, being off on average by 5 to 20 percent is not bad for a baseline model. However, we can do better. We will show as such in the next part, because our model will be more advanced, have more features, and those features will be engineered to get the best possible prediction.

<h2><ins>Final Model</ins></h2>

The model, while still being based on linear regression, has gotten some upgrades. In terms of new columns, we went with ‘barons’, ‘towers’, ‘vision score’, ‘minion kills’, and ‘dragons’. 

We dropped the monster kills column due to only 1 player on the team really benefiting from killing the monsters, so we instead switched to minion kills, which are more impactful for the other 4 players in the game. As it was more impactful, it is also a better predictor of how much gold was earned over the course of a game. 

The barons column, which represents Baron Nashor, an optional side objective that you and your team can take on to help with winning the game, how many times he was killed. The more times you kill him, the more likely you are to be winning, and thus the more likely you are to have more gold. As such, it is a good predictor of gold earned. 

Towers are another good predictor, because every time you or one of your teammates destroys a tower, you get gold. Thus, more towers destroyed equals more gold for your team. 

Vision score, which represents how impactful your wards (aka cameras) were over the course of the game. The team with better vision score is more likely to win the game due to an informational advantage, and despite not being directly correlated with gold, become a predictor due to their involvement in winning the game. 

Dragons are another side objective, much like Baron Nashor, dragons also give you gold and help you win the game. The more dragons you kill, the more likely you are to be winning, and thus the more gold we can expect you to have. 

These features are all linearly correlated with gold earned, but we figured that model could use some more advanced feature engineering, which is why we decided to use a Quantile Transformer with n equaling 10 on the columns ‘vision score’ and ‘minion kills’. These columns had a lot of variance due to having larger ranges, and thus we thought it could benefit in the readability of the model on the inside. This had a great effect on the accuracy of our prediction.

Next, we have the hyperparameters. Of the hyperparameters to tune, the one we chose is Polynomial Features. To tune this, we tested every polynomial from 1 to 20 on a k-folds cross-validation, and found the best option to be either 2 or 3. In the actual final build of the model, we set it up to run a k-folds cross-validation from 1 to 5 every time the model runs. As such, sometimes it will decide that either 2 or 3 is the better one to use based on the training data. 
 
Now to the meat and potatoes of our analysis, the performance of the model. We reduced the MSE by approximately a factor of 10, bringing it down to 1.6 million, much better than the previous 12 million. The r-squared value went up to .98, suggesting a higher correlation then before, which is quite nice. And lastly, we got the average absolute error of about 980, which is significantly better than the previous error of 2700. This is a great improvement over our previous model, because it is magnitudes more accurate, and being off by only 900 gold on average is barely a blip in the radar. Gold is used to buy items, and the average item costs 3000 gold. Being off by 2700 is almost an entire item, which is a HUGE power spike in terms of gameplay. 900 gold, on the other hand, is only closer to 1/3rd of an item, which does not grant any large scale power spikes. As such, 900 gold is close enough to be unimportant, over 2700 which could make the difference between winning and losing the game. As such, our model is a good model, and can make reasonably accurate predictions.


<h2><ins>Fairness Analysis</ins></h2>

For our 2 groups to test between, we decided to go with the different leagues that are recorded in the data set. The 2 leagues we ended up choosing are LFL2, which stands for Ligue Française de League of Legends and is the 11th edition of the French League of Legends semi-professional league. The other group we ended up choosing is the LCKC, the League of Legends Championship Korea. The test statistic we chose is the difference in the MSE, because as stated earlier, MSE is very useful for comparing the models, but not as helpful in seeing how accurate the model itself is.

Our null hypothesis is that the groups are NOT being treated differently, and the alternative hypothesis is that the groups ARE being treated differently. With this in mind, we performed our permutation test, and got a shocking p-value of .2183. This p-value tells us that we fail to reject the null hypothesis, and there is no evidence that the groups are being treated differently. As such, there is no evidence that our model treats the leagues any differently. 
 


<iframe
  src="assets/part8_permtest.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
