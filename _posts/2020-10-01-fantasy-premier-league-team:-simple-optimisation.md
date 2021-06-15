---
layout: post
title: Optimising your Fantasy Premier League team with Linear Optimisation
subtitle: Exploring optimisation for FPL to get the best out of your team
# cover-img: /assets/img/path.jpg
# thumbnail-img: /assets/img/thumb.png
# share-img: /assets/img/path.jpg
tags: [FPL,fantasy,premier,league,sports,optimisation]
---


# Fantasy Premier League - Team Optimisation

#### This piece of work sought to give a way to try and remove emotion and bias from decisions about your Fantasy Premier League team. This is achieved by using past performance as an indicator of future success and apply basic optimisation to choose the best combination of players to fit this.

#### Please note that this article was originally written in September 2020, so discussion of the current 'gameweek' may be outdated

Fantasy Premier League (FPL) is a notoriously unpredictable game of strategy, footballing knowledge, and above all, luck. This fact has never been more true than in the 2020/2021 season, with one of the shortest pre-seasons ever and empty stadiums meaning that knowing which players and teams will do well has been extremely difficult. The first 3 Game Weeks have demonstrated this excellently, with great teams falling to their “lesser” opponents, defending being abandoned in favour of scoring plenty of goals, and penalties being awarded left, right, and centre. This chaotic start to the FPL season has also meant that plenty of managers have already played their wildcards, and not seen the returns they might have hoped for from playing the precious chip at an early game week.

With this in mind, I wanted to see if there was a way to try and make the selection of my wildcard team a little bit more logical, and less clouded by the blind panic that my high value assets haven’t returned the value I would hope for across the small sample size of the season so far.

As an aside, if you are looking for ways to begin using data to look at your FPL team, or just the game more broadly, there are plenty of great resources to get you started. David Allen has a great tutorial on how to start using the [FPL API](https://towardsdatascience.com/fantasy-premier-league-value-analysis-python-tutorial-using-the-fpl-api-8031edfe9910) which will go a long way to helping you produce some visualisations and insight. For a dataset which goes a little bit deeper, Vaastav has put together a great resource with current and historic FPL data, as well as some data from [Understat](https://github.com/vaastav/Fantasy-Premier-League). I’ll be using content from [Vaastav’s repo](https://github.com/vaastav/Fantasy-Premier-League) for my work, and I think it’s a really well structured source of FPL data.

## How can we do it?
Moving on to the interesting stuff. FPL can be thought of as a risk/reward problem, where you risk spending more on high-value assets to try and yield the best possible reward. The risk of spending on high value players is increased by the innate randomness that a much cheaper player can easily outscore the most expensive players week-in week-out. To some extent, this is not dis-similar from an investment portfolio, where you can risk putting too much capital in a blue-chip tech company, and miss out on the firm which is rising quick and that those who are watching closely will identify before anyone else. You however also risk that the cheaper players might have one good week, and then never perform again, similar to people buying an investment after a spike in its price. Taking the analogy further, I wanted to see if I could optimise my portfolio (squad) by maximising the returns (points).

A simple approach to this might be something like taking a common metric in FPL like Value (typically defined as points gained so far divided by the cost of the player) and choosing the top value players such that the position count and budget constraints are adhered to. My problem with this approach was:
- It relies on historic data, so might not include things like those players could have had an easy start to the season, and are destined to return fewer points as the season goes on
- It also limits the capability of a fully optimal solution to be found. If a player which is valued highly is very expensive, a saving will need to be made later on in the team, whereas it’s possible that some combination of more middle priced assets could actually yield more points in total.

<p align="center">
  <img src="/assets/img/linear_opt_image.png" />
</p>

<div align="center">
**A visualisation of how linear optimisation chooses a solution.**
</div>


Python has a package called PuLP which allows exactly the sort of linear optimisation that I’m looking for to be done. I was inspired to turn to this approach after reading a very similar approach applied to [Fantasy (American) Football](https://medium.com/ml-everything/using-python-and-linear-programming-to-optimize-fantasy-football-picks-dc9d1229db81) by ml-everything. This uses PuLP to find the optimum player combination to maximise points returned. I have developed this idea further to maximise a simple metric which includes the weighted sum of points from the 2019/20 and 2020/21 seasons, which is then weighted again using the Fixture Difficulty Rating (FDR) for the next X games. The idea of this is to include:
- Some historic understanding of how the player returned last season
- Some context of how well the player has returned so far this season
- Some understanding of how difficult the upcoming games are for the player (and implicitly how well they might return, with the assumption that harder future games means they will return fewer points)

## Linear Optimisation
It would be helpful to have a brief overview of what linear optimisation is before we jump into using it. Linear optimisation is sometimes called linear programming or LP, and the objective is to find the best outcome in a model or system where relationships are linear. In this context, the relationships are things like the objective function and the constraints on variables in the system. The linear optimisation will output a 'feasible region' (like the one that you can see above) which all valid solutions sit in. The way I'll using linear optimisation here is to find all feasible solutions, and then look at those which score most highly in my chosen metric -  this might be historic FPL points, or something more complex, we'll get onto that later.

For FPL, there are two key constraints we'll apply:
- Positional: teams can only have 2 goalkeepers, 5 defenders, 5 midfielders and 3 forwards, so the model must only choose combinations of players which fit this structure
- Price: a team starts with a budget of £100.0m at the beginning of the season, and this can fluctuate as the season progresses. The prices of all players that the user buys must sum to this amount, or less. As such, the model must again keep within these parameters.

In order to solve the problem, the linear optimisation seeks to maximise a metric supplied to it. For my Fantasy Premier League example, I'll prepare a metric which tries to include past performance as well as future likelihood of scoring points (we'll see that I do that in quite an imprecise way).

For reference, the problems here are solved using PuLP's default solver which is their own version of a Branch and Cut solver.

## Data Preparation

As a first step, using the approach that David Allen took I pull the data from the official FPL. This gives me a DataFrame containing basic data on all this season’s players so far.

In order to produce a metric which I want to maximise, I need to think carefully about my aim. I mentioned earlier that using points already achieved is problematic as past performance are not directly indicative of future acheivement, but it is also a problem early on in the season as optimising based off of a handful of games is likely to produce a team which is highly sensitive to a small sample size.

For this reason, I want to produce a weighted sum of this season's performance and last season's performance. To enable this, the next step is to get data from last season, I use Vaastav’s repo (mentioned above) which already has all this for me. 

Initially, there's two metrics which I could weight across the two seasons, and they're both closely linked to one another. I could use pure Points scored by the player which is the most obvious candidate for this - I want to score the most points week-in-week-out. Another option however, would be to maximise the Value of the player. As referenced earlier, this is typically the number of points scored by a player in a season, divided by the price of the player, producing a value of Points/£ million. Maximising this would give us the players who are currently offering the best bang for our buck, but not necessarily the most points. Value is an important metric to consider when manually evaluating players, or comparing players side by side, but for this approach, we'll optimise on Points alone.

To produce a weighted sum of points we do
```
Points Weighted = W19 * P19 + W20 * P20
```
Where:
- W19 is the weighting for 2019/20 points
- P19 is the number of points scored in total across the 19/20 season
- W20 is the weighting for 2020/21 points
- P20 is the number of points scored so far in the 20/21 season

The value of W19 and W20 are intended to be set variably, depending on the stage in the season. For the stage in the season where we are at at the time of writing, I have chosen W19 = 0.2 and W20 = 0.8, meaning that most of the weight is on the current (20/21) season, but we still want to draw through some of our understanding of last year's performance.

With these values set, we are now on the path to having our metric to maximise. However, we are not there quite yet!

An important factor that I have mentioned multiple times so far, is that previous performance is not a predictor of future success. In Fantasy Premier League, this is down to many factors which, in some cases, are impossible to measure. One which we can easily account for though, is the difficulty of upcoming fixtures. It's easy to see how a good player might score very highly against poor teams, which might lead to some people choosing this player for their team. However, if the upcoming run of games is challenging, we might want to reconsider that decision.

To build this into my optimisation problem, I decided to weight my Points metric which I produced above, by the sum of the Fixture Difficult Rating (FDR) produced by the Premier League for the window of games we want to look over. This window of games will be variable depending on the user's selection when running the optimisation, allowing the user to change the horizon for which the optimisation is being performed for.

So our metric now looks like:

```
Optimisation Metric = (W19 * P19 + W20 * P20)/(SUM_FDR)
```
Where:
- W19 is the weighting for 2019/20 points (0.2)
- P19 is the number of points scored in total across the 19/20 season
- W20 is the weighting for 2020/21 points (0.8)
- P20 is the number of points scored so far in the 20/21 season
- SUM_FDR is the total fixture difficulty for the number of games configured by the user

Applied across a cut of players, this produces the following:
<p align="center">
  <img src="/assets/img/team_data_snapshot.png" />
</p>

## Identifying the optimal solution
With our metric created, the optimal combination of these assets can now be found. 

The first step towards the optimisation is probably a slight move away from producing the most optimal answer (or at least having the option not to!). The metric produced above gives us a good picture of players who have played for both the 19/20 and 20/21 seasons but what about newly promoted teams or players who have joined teams in the transfer window? Particularly early on in the season, when you might be much more heavily weighting last season's points totals, new transfers and promoted players will be heavily penalised, and therefore not included in the optimal team. To mitigate against this, I wanted to be able to force players to be included (or excluded if necessary) in my optimal solution. This could also be used to choose players who you think are essential fantasy picks, but that the optimised solution doesn’t choose for any reason.

The way this is implemented, is by pulling out the chosen players, the sum of the metric that they add, and how they impact the budget. It's important that their cost is taken from the budget available to the optimiser, as well as their slot in the relevant position, so that the optimiser produces a solution which is applicable in combination with these players.

Initialising the optimiser and forcing the inclusion of some players will make the optimiser give you some information about the choices you've made so far:
<p align="center">
  <img src="/assets/img/param_updates.png" />
</p>

Now we can solve the problem! As mentioned before, I used PuLP to do this, and wanted to maximise my weighted points metric, constrained by the budget and the position counts required in FPL. This is then solved to find the best combination of players to get the highest sum of our metric:

## Results
When PuLP runs the optimisation, it'll give you details on the solving of the problem within the Jupyter Notebook command line window. It's a straightforward problem, so I'd usually expect this to be solved, unless incompitable requirements have been applied (i.e. forcing inclusion of players who's costs mean you can't choose enough players for your team). Initially, I asked for Trent Alexander-Arnold and Patrick Bamford to be forced to be included, and used a 5 gameweek window to sum the Fixture Difficult Ratings over. Once it's solved, you'll see the following:

<p align="center">
  <img src="/assets/img/optimal_team.png" />
</p>

From this output, we can see the team selected, the budget leftover (in case the algorithm manages to find an optimal solution without using up all our cash) and the sum of the metric values. This sum doesn’t really mean anything at the moment, but allows teams to be compared when running multiple optimisations. For example, if I prevent any players from being forced to be included the team becomes:

<p align="center">
  <img src="/assets/img/5_game_window_team.png" />
</p>

Additionally, as I mentioned earlier the number of games being considered to find the FDR sum can be altered. When looking at a 10 game horizon, the optimal team becomes:

<p align="center">
  <img src="/assets/img/10_game_window_team.png" />
</p>

This team so far this season has scored 289 points, so clearly not a bad choice, although I’m sure most people can choose a high scoring team with the benefit of hindsight!
Overall, the choices from these optimisations aren’t that surprising, but at times can be seen to be biased towards this season’s performance (as expected as the weighting for this season is much higher). If I change the metric to be a 50:50 split between 19/20 points and 20/21 points, the result becomes:

<p align="center">
  <img src="/assets/img/even_weighting_team.png" />
</p>

This result highlights an issue with the algorithm in that it does not implement player number caps. This team has 4 Wolves players, which wouldn’t be permittable under FPL rules. This is an area for future development, but when using the 80/20 weighting this issue occurs less (although I expect it will become more common as the season goes on and one or two high-value teams emerge)


In terms of other steps going forward, developing the metric further to make it so that the 19/20 and 20/21 values are more comparable could be useful. Options might include calculating the Points Per Game for each player, and weighting those. I want to move away from weighting raw points, as the 19/20 season obviously dominates this summation as the score is higher thanks to that season being complete.

Overall I think this approach gives an interesting way of choosing your Fantasy team. It certainly isn’t perfect, and can still be developed, but it helps to remove cost-benefit part of thinking, which can be very difficult when there is so many options. I hope to develop this further to identify an even stronger way of finding the best possible team to select. Until then, best of luck!
