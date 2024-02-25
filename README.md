# NBA_Odds_Analysis
-link to notebook
-story behind data, previous work
-motivations behind the project
-objectives of the project
-broader impact of the project
-how we preprocessed (choosing model, over/underfitting, etc)
-conclusions
-possible future follow ups
-list of group members and contributions

## website
https://www.kaggle.com/datasets/christophertreasure/nba-odds-data


## data description

### date:
The date on which the game took place

### season:
The year/season during which the game took place

### team:
The team on which the bet is being placed. There are 32 unique teams.

### home/visitor:
Boolean value that represents whether the team on which the bet is placed, is playing at their homeground, or is visiting their opponent.

### opponent:
The team against which our team is playing.

### score:
Our team's final game score

### opponent score:
The enemy team's final game score.

### moneyLine:
The simplest type of bet; if your team wins, you win the bet, no matter by how much (margin) they win. The moneyline payouts are an integer representing the payout if a given team wins; negative for favorites "likely" to win, positive for "underdogs" likely to lose. If negative represents how much money you must bet to gain $100, if positive represents how much you will gain on a $100 bet. For example, for teams A and B with moneylines -550 (underdog) and -800 (favorite) respectively: if you bet $100 on A and win you will gain $550, if you bet 800 on B you will gain $100

### total:
Total points bet; also known as over/under bet; this involves predicting if the total points socred by both teams will be over or under the projected point total, which is set by the sportsbook; assume the projected point total is 212, and you bet the final total points will be over the projected point total. If the actual points total after the game is higher than 212, then you win. If it is lower than 212, then you lose. If the actual score is equal to 212, then the bet is refunded.

### spread:
Point spread bet; this bet requires a team to win or lose by a specific number of points, which is set by the sportsbook. For example, if the spread is +3 for Los Angeles Lakers in a game that Lakers vs Clippers, Clippers will have the spread of -3 in this game. If you bet on Lakers, it means that you are betting on the chance that Lakers will win the game by at least 4 points more than Clippers. If Lakers actually win Clippers with at least 4 more points, you win the bet. If Lakers win Clippers with 2 points for fewer or even lose the game, you lose the bet. If Lakers win Clippers with eactly 3 more points, the bet is refunded. On the contrary, if you bet on Clippers, it means you are betting on the chance that Clippers will lose no more than 3 points. Therefore, if Clippers lose Lakers with no more than 2 points or even win Lakers, you win the bet. If Clippers lose Lakers with at least 4 points, you lose the bet.

### secondHalfTotal:
The same as total except that this bet only calculates points in the second half of the game.

## Data distributions - interpretation:

### season:
The data that we have goes from the 2008 season to the 2023 season, and is roughly evenly distributed to have the same number of games per season. This makes sense since the number of games played each season should be pretty similar if no rule changes were made.

### score: 
The scores seem to be somewhat normally distributed with the mean and median both being 104. The scores range from 54 to 168, and the standard deviation is 13.30. 

### opponentScore:
As opponentScore and score have the same data points just ordered differently, their distributions are identical.

### moneyLine:
moneyLine seems to have extreme outliers on both sides, as it ranges from -13000 to 6500, but 50% of the data lies between -240 to 195 and 90% of the data lies between -800 and 575.

### opponentMoneyLine:
As opponentMoneyLine and moneyLine have the same data points just ordered differently, their distributions are identical.

### total
Total number of points has an almost normal distribution ranged from 170.5 to 251, since it calculates points from both teams in a game.

### spread
The spread has a symmetric distribution because in every game, the magnitude of spread is the same for both teams but the sign is opposite. The data does not have spread to be zero for any entry because it means the game is a tie. However, NBA does not allow tie games.

### secondHalfTotal
Just like total, secondHalfTotal has an almost normal distribution ranged from 84 to 125, which is nearly half of the minimum and maximum of total because the points added together from both teams for the first half of the game should be roughly equal to the one from the second half.


## Data Preprocessing

### Encoding
To preprocess the data, we plan to encode the home/visitor column to convert the data values to be integers instead of objects. We plan to experiment with two datasets: one using a label encoder, and another with one-hot encoding to represent the teams, and seeing if either method will affect our model's performance. We also one-hot encoded the home/visitor column to keep the data numerical.

### Normalization and Standardization
Based on the pairplot generated in exploratory analysis, we found that the data in the scores (and opponent scores) columns looks roughly normal, and may benefit from standardization. We used the Shapiro-Wilk test to confirm that the scores and opponent scores were indeed normally distributed, prompting us to standardize those columns. Because the Shapiro-Wilk test pvalues are inaccurate for more than 5000 samples and we have 37k samples, we randomly sampled 1000 data points from each column to perform the Shapiro-Wilk test. For the rest of the numerical data, we applied a min-max normalization as our data is scaled differently depending on the feature. 

### Feature Selection
We discussed whether we need to filter out duplicate games, since there are two entries: one for the home team and one for the away team for each game, but ultimately decided to keep them so our dataset isn't biased toward home or away games. Additionally, we added features from the existing data that may affect our predictions, such as win rate for the teams, and the win rate against the specific opponent. For the latter, there is minimal data between two teams in a season, so we will see if the added feature benefits our predictions.

### Additional Columns
We created the following additional columns based on the given dataset: win, year, month, day, day_of_week, games_played, cumulative_wins, cumulative_win_rate, cumulative_score, and average_score. The win column represents whether team or opponent won the game. The year, month, day, and day_of_week columns help represent the date in a more manageable numerical format. The games_played, cumulative_wins, cumulative_win_rate, cumulative_score, and average_score columns inform the model as to the team's performance in the current season.

### Prediction targets and inputs
The input columns we plan to feed our model are: 'date', 'season', 'team', 'home/visitor', 'opponent', 'day', 'day_of_week', 
              'games_played', 'cumulative_wins', 'cumulative_win_rate', 'cumulative_score', 'average_score', 
              'opponent_games_played', 'opponent_cumulative_wins', 'opponent_win_rate',
              as these are all publicly available before the moneyLine is announced.

The targets we plan to have our model predict are: 'moneyLine', 'total', 'spread', 'secondHalfTotal', 'score', 'winner',
as these will all be important aspects of betting that occur for the given game.

### Next 2 Models
One of the next models that we're thinking of building is the RNN. This is due to the fact that our data can be interpreted as a time
series, where we want to predict the next point in the series. In this case, an RNN would be able to generate a robust representation
of each team, which could then be used for the final prediction step. The benefit of doing this over a traditional NN is that it will
be better equipped to handle how each team changes over time, and be better able to predict how that team will be for a timestep that
hasn't been seen before, which is what our current test set is composed of.

The other model that we want to try using is an SARIMA (Seasonal Auto-Regressive Integrated Moving Average). This model shows promise
for the same reason as the RNN: it is built to account for changes/constants over time. We chose the seasonal variant of the base
ARIMA because we suspect that teams will have verying performance based on seasonal changes (not the season variable but like month
or day of week for example), which we hope this variant will be able to capture.