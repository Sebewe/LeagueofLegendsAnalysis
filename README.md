# Analyzing the Impact of Champions and Objectives

Sebastian Modafferi

### The Goal from the Beginning
My goal with this data was to draw a few conclusions about different champions, tactics, and strategies that could improve objective completions. I initially focused specifically on towers, and towerplates. These are effecient ways of measuring the 'objective pressure' that a team has. I believe it is more of a foundational assessment of team performance, whereas things like winrate or kill-to-death ratio are incredibly biased by individual micro performance which is almost impossible to measure from a dataset like this. Even metrics such as gold per 15 minutes are influenced by factors that may be difficult to measure with this dataset. I went into it knowing that if I had to focus on any given champion it would be Ashe, her ability to permanently slow the enemy is very frustrating to play against and so i wanted to figure out more about her.

### About the Dataset
The data was downloaded from [OraclesElixir](https://oracleselixir.com/tools/downloads). They provide yearly data on the professional scene of League of Legends. I am working with the 2022 data since it contains the most up-to-date and simeltaenously year-long observations. The data comes in a `csv` file, where there is 12 rows for each game. 10 rows for the competeing players, and 2 rows for team-aggregate data. There was a total of 149400 entries, which comes out to 12450 games. As for the columns, there was 123. Many of these were not used, however the most used columns for my analysis were:
 - `url`: this column was used for verfying the data cleaning process. It contains a URL link to matches, which could be used to confirm a boolean representation of different numerically represented observations.
 - `result`: this column represents if the team (or player), won or lost that game.
 - `side`: this column represents the side of the map that the given player/team played on. It was later converted to a boolean representation with the title `blueside`.
 - `playoffs`: this column describes if the match was taken place in part of a playoff series or not. It was later converted to a boolean representation with the title `isplayoff`.
 - `position`: this column represents the role that a player was playing during the match. It is one of the 6 given values:
    1. top
    2. mid
    3. jng - jungle
    4. bot - also referred to as 'adc'
    5. sup - support
    6. team - Was used on rows that were for team-aggregrate data. This was used to separate into two `DataFrame`s.
 - `patch`: this column refers to the patch of the game that the match was played on.
 - `towers`: the amount of towers destroyed by the team that game. This data is **only** available for the rows where `position=='team'`
 - `turretplates`: the amount of turret plates that were destroyed before they dropped off the towers. Again, this data was only available for rows where `position=='team'`.
 - `goldat15`: the amount of gold that a player/team collected at 15 minutes.
 - `dpm`: the average damage dealt per minute spread across the entire game.

### Data Cleaning 
The majority of the data cleaning took place with converting integer types to boolean types. Many columns were listed as 0-1 representation instead of True/False. This leads to a lot of memory being wasted when loading up the `DataFrame`, so that was my first priority. To be more specific, the columns I converted to boolean representation include: `['firstblood', 'firstdragon', 'firsttower', 'firstmidtower', 'firsttothreetowers', 'isplayoff', 'side', 'heralds', 'firstherald']`. I also corrected the type of the `'date'` column, and convereted it to `pandas.DateTime`. In addition to this, I found it incredibly helpful to split the provided `DataFrame` into two dataframes, one containing player data and another containing match aggregrate data for each team. They still maintianed a common `'gameid'` and `'blueside'` column, making it so that I could re-join the two, given it as necessary. Here is what the `player_data` frame looked like:

| blueside   | isplayoff   | result   | position   | champion   |   goldat15 |   deathsat15 |   killsat15 |     dpm |   patch | gameid                |
|:-----------|:------------|:---------|:-----------|:-----------|-----------:|-------------:|------------:|--------:|--------:|:----------------------|
| True       | False       | False    | top        | Renekton   |       5025 |            0 |           0 | 552.294 |   12.01 | ESPORTSTMNT01_2690210 |
| True       | False       | False    | jng        | Xin Zhao   |       5366 |            2 |           2 | 412.084 |   12.01 | ESPORTSTMNT01_2690210 |
| True       | False       | False    | mid        | LeBlanc    |       5118 |            0 |           0 | 499.405 |   12.01 | ESPORTSTMNT01_2690210 |
| True       | False       | False    | bot        | Samira     |       5461 |            2 |           2 | 389.002 |   12.01 | ESPORTSTMNT01_2690210 |
| True       | False       | False    | sup        | Leona      |       3836 |            2 |           1 | 128.301 |   12.01 | ESPORTSTMNT01_2690210 |

And then the head of the `game_data` frame looks like: 

| blueside   | isplayoff   | result   |   goldat15 |   deathsat15 |   killsat15 |     dpm |   turretplates |   towers |   patch | gameid                |
|:-----------|:------------|:---------|-----------:|-------------:|------------:|--------:|---------------:|---------:|--------:|:----------------------|
| True       | False       | False    |      24806 |            6 |           5 | 1981.09 |              5 |        3 |   12.01 | ESPORTSTMNT01_2690210 |
| False      | False       | True     |      24699 |            5 |           6 | 2799.02 |              0 |        6 |   12.01 | ESPORTSTMNT01_2690210 |
| True       | False       | False    |      23522 |            3 |           1 | 1690.98 |              2 |        3 |   12.01 | ESPORTSTMNT01_2690219 |
| False      | False       | True     |      25285 |            1 |           3 | 2124.55 |              3 |       11 |   12.01 | ESPORTSTMNT01_2690219 |
| True       | False       | True     |        nan |          nan |         nan | 1762.02 |            nan |        8 |   12.01 | 8401-8401_game_1      |

### Univariate and Bivariate Analysis
The first question I had pertained to tower destruction. More specifically, I wanted to know how many towers were destroyed each in game, as this was fundamental to my assumptions around Ashe and her ability to capture objectives. I initially drew a histogram of how many towers are getting destroyed in each game:
<iframe src="assets/towers_univariate.html" width=800 height=600 frameBorder=0></iframe>
This histogram appeared to be bimodal, and it lead me to reason that this was because teams who win the game are going to have to destroy more towers. The existence of any values under 5 proved this, given that nearly no surrenders happen in the professional field. To support this argument, I plotted the same histogram, except I colored winning teams red, and losing teams blue. This helped differentiate which data was coming from where.
<iframe src="assets/towers_bivariate.html" width=800 height=600 frameBorder=0></iframe>
After looking at this histogram, I realized that it was exactly what I'd expect, and there likely wont be much to reason off of towers and winrates. There was not enough games where people lost but had many towers destroyed for me to go futher down this line of thinking. Instead, I began to question gold distribution at 15 minutes, as this would be a more solid metric for predicting the future of a game. This lead me to plot a histogram from `player_data` of the column `goldat15`.
<iframe src="assets/gold15_univariate.html" width=800 height=600 frameBorder=0></iframe>
Looking at this graph, you see a similar scenario with two peaks. Knowing the support role decently well, I quickly understood it's because supports get much less gold, especially in the early game. The first hill in the histogram was likely due to low-income roles, like support and jungle. To prove this, I plotted another colored histogram, where each color represented a role.
<iframe src="assets/gold15_bivariate.html" width=800 height=600 frameBorder=0></iframe>
This histogram supported part of my hypothesis, but showed that junglers recieve more gold than I had previously thought. Thinking about supports more, I have recently played against a lot of Ashe supports, and thought it would be interesting to see how much gold and damage they deal. I considered if Ashe support was in fact stronger than Ashe bot, as thats how I had percieved it. To get more insight on this, I aggregrated the data to show the difference in `goldat15`, `dpm`, `killsat15`, and `deathsat15` for Ashe support againt Ashe bot. These aggregrates have been aggregrated by median, as League of Legends data is prone to have large amounts of variance due to the large difference in skill between competing teams.

| position   |   goldat15 |   deathsat15 |   killsat15 |     dpm |
|:-----------|-----------:|-------------:|------------:|--------:|
| bot        |       5093 |            0 |           0 | 471.978 |
| sup        |       3532 |            1 |           0 | 389.309 |

Looking at it, I was surprised to see that the numbers, especially for dpm, are not too far off. But without having any baseline metric to compare it against, I needed to check the overall averages for the same statistics between all supports and botlane. The aggregrate data below was collected the same way, except this time for all supports and bot lanes, instead of just Ashe.

| position   |   goldat15 |   deathsat15 |   killsat15 |     dpm |
|:-----------|-----------:|-------------:|------------:|--------:|
| bot        |       5391 |            0 |           1 | 531.121 |
| sup        |       3385 |            1 |           0 | 151.88  |

Looking at this, we can see that the gold and dpm for Ashe bot is lower than the overall averages, and at the same time the gold and dpm for Ashe support is higher than the overall averages. This really piquied my interest and so I decided to transition my initial 'hypothesis' to be more around this topic: is Ashe support just as strong, or possibly stronger, than Ashe bot?

### Assessments of Missingness

#### NMAR Analysis
I believe that the missingness for the columns `ban#` (where # is 1-5) are **NMAR**. This is because not only are there very few values missing, but they are different for each ban column. This suggests that the reason it is missing is because it is either a champion that was not correctly updated to the database directly after release. i.e, they are playing a 'new' champion in the games, and they haven't been added to the database that OracleElixer collects from before the match. Or, it could also be that the team decided not to ban a champion in that ban round. If the amount of missing values for each `ban#` column were the same, it would suggest that it was an error in collecting the data as a whole, but since they are all different, it suggests that the error is due to the previous reasons stated.

#### Missingness Dependency
Since I was still curious about measuring objectives, I decided to look into why so many values in the `turretplates` column were missing. My initial hypothesis was games that were less relevant to the scene (i.e. not playoffs) may have not been recorded or uploaded correctly, since it was assumed people wouldn't be interested in them. To test this hypothesis, I set up a **permutation test** with these hypotheses:
 - **null hypothesis**: `turretplates` missing is due to randomness.
 - **alternative hypothesis**: `turretplates` missing is related to the game being a `playoff` game or not.
 - **test statistic**: I decided to use `difference in group means`, where the mean is the amount of playoff matches divided by the total amount of matches. 
 - **p-value**: I decided to set my p-value to 0.05 for this test.
After running the permutation test over 1000 trials, here is the plotted distribution:
<iframe src="assets/missing_playoff.html" width=800 height=600 frameBorder=0></iframe>
This distribution, and the associated 0.0% chance of finding a distribution like this under the null allows me to **disprove the null hypothesis**, and suggests that the missingness of the `turretplates` column is associated with the values in the `isplayoff` column. While this was interesting to find out, I knew that specific patches were more likey to be played during the playoffs. To confirm this I plotted the bar-chart below
<iframe src="assets/playoff_patch_bar.html" width=800 height=600 frameBorder=0></iframe>
After confirming that playoff matches were not evenly spread out throughout patches, it lead me to question if the `turretplates` column being missing was because of the patch not outputting compatible data for OracleElixir to collect and provide. To test this suspiscion, I laid out another **permutation test**:
 - **null hypothesis**: `turretplates` missing is due to randomness.
 - **alternative hypothesis**: `turretplates` missing is related to the `patch` of the game that was played in.
 - **test statistic**: Since patches are most accurately treated as categorical distributions, **total variation distance** was going to be the most accurate test statistic available to me.
 - **p-value**: I decided to set my p-value to 0.05 for this test.
 After running the permutation test over 1000 tirals, here is the plotted distribution:
 <iframe src="assets/missing_patch.html" width=800 height=600 frameBorder=0></iframe>
 This distrubtion, and the associated 0.0% chance of finding a distribution like this under the null, showed that I was able to **disprove the null hypothesis**. This suggests that the missingness for `turretplates` was due to both to the values in the `patch` and `isplayoff` column. This lead me to question if it also had anything to do with the amount of towers destroyed? To answer this, I laid out a 3rd **permutation test** to determine missingness:
 - **null hypothesis**: `turretplates` missing is due to randomness.
 - **alternative hypothesis**: `turretplates` missing is related to the amount of `towers` destroyed by that team.
 - **test statistic**: Since towers is a numerical variable, I felt it was best to describe this with another `difference in group means`.
 - **p-value**: I decided to set my p-value to 0.05 for this test.
After running the permutation test over 1000 tirals, here is the plotted distribution:
<iframe src="assets/missing_towers.html" width=800 height=600 frameBorder=0></iframe>
This plot, and the associated 41% likelihood showed that I **failed to disprove the null hypothesis**. Which suggests that the missingness of the `turretplates` column is **not** related the values in the `towers` column. Ultimately, I believe that the missingness for the `turretplates` column is related to the patch, and the reason there is an association between the missingness of `turretplates` and `playoff` is because of the association between `playoff` and `patch`. 

### Hypothesis Testing

The first one I'd like to test is *whether or not Ashe games tend to have more towers destroyed*. For this question, we will set up our experiment like this:
 - **null hypothesis**: Playing Ashe and not playing Ashe have the **same average amount of towers destroyed**
 - **alternative hypothesis**: Playing Ashe leads to **less towers destroyed than** not playing Ashe.
 - I will use a **permutation test**. This is because I want to get an understanding of the entirety of the game, and don't want to accept the 2022 professional state of the game as my population distribution.
 - **test statistic**: I will be using (mean towers destroyed **not playing** Ashe) - (mean towers destroyed **playing** Ashe). I chose this since my alternative hypothesis suggests a difference in one direction, a larger test statistic will support this.
 - **p-value**: I've decided to again choose a 0.05 p-value.
 After setting up my experiment and collecing 1000 simulated test statistics, here was the plotted distribution:
<iframe src="assets/hypothesis_towers.html" width=800 height=600 frameBorder=0></iframe>
Given the plot, and the 22% chance of finding this under the **null hypothesis** means that I **failed to reject the null hypothesis**. This suggests that Ashe does not on average destroy less towers than other laners. However it **does not** suggest that she destroys more towers. This being said, this test was laid out with both Ashe bot and Ashe support, so I decided to run a test to see if bot Ashe or sup Ashe is 'better'. To do this, I tested `dpm`, damage per minute. I wanted to determine if the difference between Ashe support dpm and Ashe bot dpm was purely due to chance, and in reality they have the **same damage distribution**. I laid out my experiemnt like this:
 - **Null Hypothesis**: Ashe bot and Ashe sup have the same dpm, and the variation we see is due to randomness.
 - **Alternative Hypothesis**: Ashe bot does **more damage per minute** than ashe support, and the variation we see is due to this.
 - **Test statistic**: I will be using (mean ashe bot dpm) - (mean ashe sup dpm) as my test statistic. This is because a larger test statistic will point in favor of the alternative hypothesis, making it simpler to test a p-value.
 - **Test type**: Since the hypothesis is not claiming for the observed data to be the true population data, but rather we are trying to extrapolate from this data to a larger population, we will again be using a **permutation test**.
 - **p-value**: I've decided to again choose a 0.05 p-value.
After setting up my experiment and collecting 1000 simulated test statistics, here was the plotted distribution:
<iframe src="assets/hypothesis_dpm.html" width=800 height=600 frameBorder=0></iframe>
This distribtion, and the associated 0.0% chance of finding it under the **null hypothesis** means that I was **able to reject the null hypothesis**. This suggests that Ashe bot has a higher `dpm` than Ashe support, meaning my theory of them being on par is not supported, as far as damage is concerned.
