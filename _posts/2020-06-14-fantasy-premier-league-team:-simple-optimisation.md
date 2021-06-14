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

Fantasy Premier League (FPL) is a notoriously unpredictable game of strategy, footballing knowledge, and above all, luck. This fact has never been more true than in the 2020/2021 season, with one of the shortest pre-seasons ever and empty stadiums meaning that knowing which players and teams will do well has been extremely difficult. The first 3 Game Weeks have demonstrated this excellently, with great teams falling to their “lesser” opponents, defending being abandoned in favour of scoring plenty of goals, and penalties being awarded left, right, and centre. This chaotic start to the FPL season has also meant that plenty of managers have already played their wildcards, and not seen the returns they might have hoped for from playing the precious chip at an early game week.

With this in mind, I wanted to see if there was a way to try and make the selection of my wildcard team a little bit more logical, and less clouded by the blind panic that my high value assets haven’t returned the value I would hope for across the small sample size of the season so far.

As a small tangent, if you are looking for ways to begin using data to look at your FPL team, or just the game more broadly, there are plenty of great resources to get you started. David Allen has a great tutorial on how to start using the [FPL API](https://towardsdatascience.com/fantasy-premier-league-value-analysis-python-tutorial-using-the-fpl-api-8031edfe9910) which will go a long way to helping you produce some visualisations and insight. For a dataset which goes a little bit deeper, Vaastav has put together a great resource with current and historic FPL data, as well as some data from [Understat](https://github.com/vaastav/Fantasy-Premier-League). I’ll be using content from [Vaastav’s repo](https://github.com/vaastav/Fantasy-Premier-League) for my work, and I think it’s a really well structured source of FPL data.

## How can we do it?
Moving on to the interesting stuff. FPL can be thought of as a risk/reward problem, where you risk spending more on high-value assets to try and yield the best possible reward. The risk of spending on high value players is increased by the innate random-ness that a much cheaper player can easily outscore the most expensive players week-in week-out. To some extent, this is not dis-similar from an investment portfolio, where you can risk putting too much capital in a blue-chip tech company, and miss out on the firm which is rising quick and that those who are watching closely will identify before anyone else. Taking the analogy further, I wanted to see if I could optimise my portfolio (squad) by maximising the returns (points).

A simple approach to this might be something like taking a common metric in FPL like value (typically defined as points gained so far divided by the cost of the player) and choosing the top value players such that the position count and budget constraints are adhered to. My problem with this approach was:
- It relies on historic data, so might not include things like those players could have had an easy start to the season, and are destined to return fewer points as the season goes on
- It also limits the capability of a fully optimal solution to be found. If a player which is valued highly is very expensive, a saving will need to be made later on in the team, whereas it’s possible that some combination of more middle priced assets could actually yield more points in total.

<p align="center">
  <img src="/assets/img/linear_opt_image.png" />
</p>

Python has a package called PuLP which allows exactly the sort of Linear Optimisation that I’m looking for to be done. I was inspired to turn to this approach after reading a very similar approach applied to [Fantasy (American) Football](https://medium.com/ml-everything/using-python-and-linear-programming-to-optimize-fantasy-football-picks-dc9d1229db81) by ml-everything. This uses PuLP to find the optimum player combination to maximise points returned. I have developed this idea further to maximise a simple metric which includes the weighted sum of points from the 2019/20 and 2020/21 seasons, which is then weighted again using the Fixture Difficulty Rating (FDR) for the next X games. The idea of this is to include:
- Some historic understanding of how the player returned last season
- Some context of how well the player has returned so far this season
- Some understanding of how difficult the upcoming games are for the player (and implicitly how well they might return, with the assumption that harder future games means they will return fewer points)

## Data Preparation

As a first step, using the approach that David Allen took I pull the data from the official FPL. This gives me a DataFrame containing basic data on all this season’s players so far.

The next step is to prepare this data for optimisation, and create my weighted points metric. To get data from last season, I use Vaastav’s repo (mentioned above) which already has all this for me. This leaves me with a DataFrame containing basic information about the players and my two metrics “opt_value” and “opt_points” (as well as the fields used to construct these metrics). These metrics are yet to be weighted by FDR, but you can see that I heavily weighted player returns this season (20_points) and gave a relatively low weight to last season (19_points).

Next I pull in the fixtures for this season, again from Vaastav’s repo. This gives me a DataFrame containing all fixtures for the coming season, with some difficulty metrics as contained on the FPL website.
Next up is defining my function for identifying the difficulty of the next X matches. I’ve written this as a function to allow transplanting across my various notebooks and scripts

### ADD IN EXPLANATION OF HOW THE SCORE IS WORKED OUT

<p align="center">
  <img src="/assets/img/team_data_snapshot.png" />
</p>

## Identifying the optimal solution
With our metric created, the optimal combination of these assets can now be found. The first step is getting a DataFrame with just the essentials in, renamed for the optimisation script to use (renaming them here rather than earlier on allows different metrics to be used for the optimisation, such as value or raw points):

The first step towards the optimisation is probably a slight move away from producing the most optimal answer. The metric produced above gives us a good picture of players who have played for both the 19/20 and 20/21 seasons but what about newly promoted teams or players who have joined teams in the transfer window? While a mitigation within the metric is yet to be produced, I wanted to be able to force players to be included in my optimal solution. This also extends to players who you think are essential fantasy picks, but that the optimised solution doesn’t choose.

Running this script gives us some basic information about what the optimiser has left to work with:

<p align="center">
  <img src="/assets/img/param_updates.png" />
</p>

To solve the optimisation problem, a similar approach to ml-everything was used to establish a PuLP maximisation problem, constrained by the budget and the position counts required in FPL. This is then solved to find the best combination of players to get the highest sum of our metric:

## Results
If running this through Jupyter Notebook, you’ll see some information about the how the solver has tackled the problem, including whether an optimal solution has been reached. As this is a pretty straightforward problem, I would always expect a optimum to be found, unless you add additional constraints which complicate the problem further. With our problem solved, it’d now be good to see what it’s found!

<p align="center">
  <img src="/assets/img/optimal_team.png" />
</p>

From this print we can see the team selected, the budget leftover (in case the algorithm manages to find an optimal solution without using up all our cash) and the sum of the metric values. This sum doesn’t really mean anything, but allows teams to be compared. For example, if I prevent any players from being forced to be included the team becomes:

<p align="center">
  <img src="/assets/img/5_game_window_team.png" />
</p>

Additionally, the number of games being considered to find the FDR sum can be altered. When looking at a 10 game horizon, the optimal team becomes:

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
