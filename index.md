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

Our dataset was already mostly clean, so in order to clean our data, we mostly focussed on two aspects of our dataset.

- We started by noticing how some columns seemed to be boolean values as integers. In order to make the dataset more efficient to work with, we started by searching for columns that contained only 0 and 1, and replaced these columns with boolean values.
- Afterwards, we removed the rows that had "incomplete" data, along with the rows that displayed the team summaries.

```python
def data_cleaning():
    data = pd.read_csv(os.path.join('data', 'league_data.csv'))

    boolCols = data.columns[data.isin([0, 1, np.nan]).all()]
    data[boolCols] = data[boolCols].replace({0: False, 1: True})

    return data[(data.champion.isna()) & (data.datacompleteness == 'complete')]
```

<iframe>
  src="assets/GoldDist.html"
  width="800"
  height="600"
  frameborder="0"
</iframe>

<iframe
  src="assets/KillDist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/GoldDistPerChamp.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/RandChampAvgKills.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


