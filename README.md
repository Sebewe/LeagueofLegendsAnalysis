# Analyzing the Impact of Champions and Objectives

Sebastian Modafferi

#### The Goal from the Beginning
My goal with this data was to draw a few conclusions about different champions, tactics, and strategies that could improve objective completions. I initially focused specifically on towers, and towerplates. These are effecient ways of measuring the 'objective pressure' that a team has. I believe it is more of a foundational assessment of team performance, whereas things like winrate or kill-to-death ratio are incredibly biased by individual micro performance which is almost impossible to measure from a dataset like this. Even metrics such as gold per 15 minutes are influenced by factors that may be difficult to measure with this dataset.

#### About the Dataset
The data was downloaded from [OraclesElixir](https://oracleselixir.com/tools/downloads). They provide yearly data on the professional scene of League of Legends. I am working with the 2022 data since it contains the most up-to-date and simeltaenously year-long observations. The data comes in a `csv` file, where there is 12 rows for each game. 10 rows for the competeing players, and 2 rows for team-aggregate data. There was a total of 149400 entries, which comes out to 12450 games. As for the columns, there was 123. Many of these were not used, however the most used columns for my analysis were:
 - `url`: this column was used for verfying the data cleaning process. It contains a URL link to matches, which could be used to confirm a boolean representation of different numerically represented observations.
 - `result`: this column represents if the team (or player), won or lost that game.
 - `side`: this column represents the side of the map that the given player/team played on. It was later converted to a boolean representation with the title `blueside`.
 - `playoffs`: this column describes if the match was taken place in part of a playoff series or not. It was later converted to a boolean representation with the title `isplayoff`.
 - `position`: this column represents the role that player was playing in that match. It is one of the 6 given values:
    1. top
    2. mid
    3. jng - jungle
    4. bot - also referred to as 'adc'
    5. sup - support
    6. team - This was used to separate into two `DataFrame`s.
 - `patch`: this column refers to the patch of the game that the match was played on.
 - `towers`: the amount of towers destroyed by the team that game. This data is **only** available for the rows where `position=='team'`
 - `turretplates`: the amount of turret plates that were destroyed before they dropped off the towers. Again, this data was only available for rows where `position=='team'`.
 - `goldat15`: the amount of gold that player/team collected at 15 minutes.
 - `dpm`: the average damage dealt per minute spread across the entire game.

#### Data Cleaning 
The majority of the data cleaning took place with converting integer types to boolean types. Many columns were listed as 0-1 representation instead of True/False. This leads to a lot of memory being wasted when loading up the `DataFrame`, so that was my first priority. To be more specific, the columns I converted to boolean representation include: `['firstblood', 'firstdragon', 'firsttower', 'firstmidtower', 'firsttothreetowers', 'isplayoff', 'side', 'heralds', 'firstherald']`. I also corrected the type of the `'date'` column, and convereted it to `pandas.DateTime`. In addition to this, I found it incredibly helpful to split the provided `DataFrame` into two dataframes, one containing team data and another containing match aggregrate data for each team. They still maintianed a common `'gameid'` and `'blueside'` column, making it so that I could re-oin the two given the ncessesity. Here is what the `player_data` frame looked like:
| blueside   | isplayoff   | result   | position   | champion   |   goldat15 |   deathsat15 |   killsat15 |     dpm |   turretplates |   towers |   patch | gameid                |
|:-----------|:------------|:---------|:-----------|:-----------|-----------:|-------------:|------------:|--------:|---------------:|---------:|--------:|:----------------------|
| True       | False       | False    | top        | Renekton   |       5025 |            0 |           0 | 552.294 |            nan |      nan |   12.01 | ESPORTSTMNT01_2690210 |
| True       | False       | False    | jng        | Xin Zhao   |       5366 |            2 |           2 | 412.084 |            nan |      nan |   12.01 | ESPORTSTMNT01_2690210 |
| True       | False       | False    | mid        | LeBlanc    |       5118 |            0 |           0 | 499.405 |            nan |      nan |   12.01 | ESPORTSTMNT01_2690210 |
| True       | False       | False    | bot        | Samira     |       5461 |            2 |           2 | 389.002 |            nan |      nan |   12.01 | ESPORTSTMNT01_2690210 |
| True       | False       | False    | sup        | Leona      |       3836 |            2 |           1 | 128.301 |            nan |      nan |   12.01 | ESPORTSTMNT01_2690210 |

And then the head of the `game_data` frame looks like: 
| blueside   | isplayoff   | result   |   goldat15 |   deathsat15 |   killsat15 |     dpm |   turretplates |   towers |   patch | gameid                |
|:-----------|:------------|:---------|-----------:|-------------:|------------:|--------:|---------------:|---------:|--------:|:----------------------|
| True       | False       | False    |      24806 |            6 |           5 | 1981.09 |              5 |        3 |   12.01 | ESPORTSTMNT01_2690210 |
| False      | False       | True     |      24699 |            5 |           6 | 2799.02 |              0 |        6 |   12.01 | ESPORTSTMNT01_2690210 |
| True       | False       | False    |      23522 |            3 |           1 | 1690.98 |              2 |        3 |   12.01 | ESPORTSTMNT01_2690219 |
| False      | False       | True     |      25285 |            1 |           3 | 2124.55 |              3 |       11 |   12.01 | ESPORTSTMNT01_2690219 |
| True       | False       | True     |        nan |          nan |         nan | 1762.02 |            nan |        8 |   12.01 | 8401-8401_game_1      |
#### Univariate and Bivariate Analysis
The first question I had pertained tower destruction. More specifically, I wanted to know how many towers were destroyed each game, as this was fundamental to my assumptions around Ashe and her ability to capture objectives. I initially drew a histogram of how many towers are getting destroyed in each game. 
