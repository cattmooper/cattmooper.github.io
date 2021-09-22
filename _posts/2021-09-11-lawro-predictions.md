---
layout: post
title: 2-0? What is Lawro thinking?
subtitle: Analysing Mark Lawrenson's BBC Sport English Premier League match predictions
# cover-img: /assets/img/path.jpg
# thumbnail-img: /assets/img/thumb.png
# share-img: /assets/img/path.jpg
tags: [football,visualisation]
---


## Lawrenson's Predictions

Have you ever wondered if there's any rhyme or reason to the way an ex-professional footballer makes predictions about Premier League games? No, me neither, until I noticed something strange in a column on BBC sport...

Mark 'Lawro' Lawrenson's Premier League Predictions column, which is [published weekly](https://www.bbc.co.uk/sport/football/58500401) on the BBC Sport website. In these columns, Mark competes against a celebrity or pop culture figure to predict the exact scorelines of the upcoming week's slate of Premier League games. Alongside the prediction, Lawro provides some context and, at times, justification for the result he's selected for that specific fixture.

So far, so unremarkable. Myself and a friend have followed these columns for a year or two, and began to notice a trend in the way that Lawro generally dishes out predictions. To have a closer look at this, the first step was to grab some data, collected from [MyFootballFacts](https://www.myfootballfacts.com/) which contains records of Lawro's predictions back to the 2010/11 season, giving 10 seasons of data to look into our suspicions.

### The 2-0 hypothesis

While hypothesis might be a bit strong, the trend that we spotted was that Lawro seemed to disproportionately select 2-0 (in either direction) as his chosen outcome of games. This was noticeable to the point that each week after we began to suspect this was happening, it was reasonable to expect that about 50% of the games in any given week would have 2-0 in either direction.

The first check to do was to look at how actual match results are distributed.
<p align="center">
  <img src="/assets/img/raw_results.jpg" />
</p>

<div align="center">
<em>English Premier League Match raw results 2010/11 - 2020/21</em>
</div>

For seasons 2010/11 to 2020/21, there's about 4000 games over the 10 seasons, with some dropping out as unfortunately Lawro doesn't do predictions if a game is rearranged after he's made a prediction (the most recent example being the games cancelled in December 2020 due to COVID). Of these, in the above plot we can see that, as you might expect, there's been a wide variety of results. To make it easier to analyse at the outset, if we standardise results we still get a wide variety.

<p align="center">
  <img src="/assets/img/standardised_results.jpg" />
</p>

<div align="center">
<em>English Premier League Match standardised results 2010/11 - 2020/21. Results standardised so that mirrored results are counted in the same bucket i.e. 2-1 and 1-2 are included in a single bar</em>
</div>

In terms of working out where the 2-0 hypothesis might stem from, the thing that jumps out is that 2-0 is certainly not the most common result in football, in fact it's only 3rd most common, occurring about 13% of the time.

Comparing that to a raw version of Lawro's predictions is striking

<p align="center">
  <img src="/assets/img/raw_predictions.jpg" />
</p>

<div align="center">
<em>English Premier League Match raw predictions 2010/11 - 2020/21</em>
</div>


And becomes even more so in the standardised version

<p align="center">
  <img src="/assets/img/standardised_predictions.jpg" />
</p>

<div align="center">
<em>English Premier League Match standardised predictions 2010/11 - 2020/21</em>
</div>

Firstly, clearly the range of predictions is must narrower, which is perhaps to be expected. It'd probably be unreasonable to think that Lawro would predict a 6-3 where he could just go for a similarly large win.

However, the real standout is the frequency of 2-0 predictions. This can be emphasised by looking at differential between actual and predicted result proportions for the results which Lawro has made predictions with

<p align="center">
  <img src="/assets/img/result_prediction_comparison.jpg" />
</p>

<div align="center">
<em>Comparison of the ratio of Predictions to ratio of Results, where a positive value means that Lawro predicted more off that result than actually occurred. Data covers EPL 2010/11 - 2020/21</em>
</div>

This plot illustrates what has already been discussed in that Lawro over-predicts 2-0 substantially, but interestingly he also under-predicts 1-0, although not as much. This might be one factor as to why he's so hot on 2-0 but it doesn't make up the whole over prediction.

Looking at the bars side-by-side, rather than the difference between them, we can see that Lawro really only favours a handful of results. when in reality the spread is much wider

<p align="center">
  <img src="/assets/img/result_prediction_comparison_bars.jpg" />
</p>

<div align="center">
<em>Side-by-side bar chart comparison of the ratio of Predictions to ratio of Results. Data covers EPL 2010/11 - 2020/21</em>
</div>

Looking at % distributions of outcomes rather than results, we can see that Lawro over-favours Home teams compared to away, but is generally about right with Draws.


<p align="center">
  <img src="/assets/img/outcome_%_plot.jpg" />
</p>

<div align="center">
<em>Comparison of match outcomes</em>
</div>

Overall Lawro seems to do much better with the outcome rather than the specific result. This largely seems to be down to the fact that he prefers to use 2-0, 1-1 and 2-1 as his outcomes almost exclusively. By taking this skewed approach, he limits his ability to correctly predict scores because results in football are much more evenly distributed than the way he predicts games.

### Historical performance
The other avenue which can be visualised is how Lawro has changed his predictions over time, to see if current day Lawro has learnt anything in his 10 year journey of making Premier League predictions. To do this, the results have been standardised as discussed above. This means that 2-0 and 0-2 both go into the same bucket.

The question on everyone's lips is "Has Lawro always absolutely loved a 2-0?"

To get an overall view, the proportions of results over the past 11 seasons can be visualised:

<p align="center">
  <img src="/assets/img/results_area_plot.jpg" />
</p>

<div align="center">
<em>Distribution of results from 2010/11 to 2019/20</em>
</div>

However, it's a bit difficult to see exactly what this says about Lawro's propensity to select 2-0, so focussing specifically on that outcome and well...

<p align="center">
  <img src="/assets/img/result_proportion_lines_2-0.jpg" />
</p>

<div align="center">
<em>Comparison of %s of actual and predicted 2-0s</em>
</div>

The answer appears to be yes and no? While 2-0 has always been Lawro's bread and butter, in recent years he's only been increasing how much he uses 2-0 and is well on his way to double the volume of them used in his articles.


When this is contrasted with the rates of other results over the years, the rise in 2-0s becomes more eyecatching, as not all results follow this path

Some results, such as 0-0 and 1-0, Lawro always has and (it seems) always will be uninterested in

<p float="left">
  <img src="/assets/img/result_proportion_lines_0-0.jpg" />
  <img src="/assets/img/result_proportion_lines_1-0.jpg" /> 
</p>


<div align="center">
<em>Comparison of %s of actual and predicted 0-0s and 1-0s</em>
</div>


The drastic fall of 1-1 predictions is interesting, particularly as this trend has continued for the past 3 seasons

<p align="center">
  <img src="/assets/img/result_proportion_lines_1-1.jpg" />
</p>

<div align="center">
<em>Comparison of %s of actual and predicted 1-1s</em>
</div>

<p align="center">
  <img src="/assets/img/result_proportion_lines_4-0.jpg" />
</p>

<div align="center">
<em>Comparison of %s of actual and predicted 4-0s</em>
</div>



### Focussing on specific teams
