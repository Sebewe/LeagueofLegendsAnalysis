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

