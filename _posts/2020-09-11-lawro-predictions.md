---
layout: post
title: 2-0? What is Lawro thinking?
subtitle: Analysing Mark Lawrenson's BBC Sport English Premier League match predictions
# cover-img: /assets/img/path.jpg
# thumbnail-img: /assets/img/thumb.png
# share-img: /assets/img/path.jpg
tags: [football,visualisation]
---


# Lawrenson's Predictions

Have you ever wondered if there's any rhyme or reason to the way an ex-professional footballer makes predictions about Premier League games? No, me neither, until I noticed something strange in a column on BBC sport...

Mark 'Lawro' Lawrenson's Premier League Predictions column, which is [published weekly](https://www.bbc.co.uk/sport/football/58500401) on the BBC Sport website. In these columns, Mark competes against a celebrity or pop culture figure to predict the exact scorelines of the upcoming week's slate of Premier League games. Alongside the prediction, Lawro provides some context and, at times, justification for the result he's selected for that specific fixture.

So far, so unremarkable. Myself and a friend have followed these columns for a year or two, and began to notice a trend in the way that Lawro generally dishes out predictions. To have a closer look at this, the first step was to grab some data, collected from [MyFootballFacts](https://www.myfootballfacts.com/) which contains records of Lawro's predictions back to the 2010/11 season, giving 10 seasons of data to look into our suspicions.

### The 2-0 hypothesis

While hypothesis might be a bit strong, the trend that we spotted was that Lawro seemed to disproportionately select 2-0 (in either direction) as his chosen outcome of games. This was noticeable to the point that each week after we began to suspect this was happening, it was reasonable to expect that about 50% of the games in any given week would have 2-0 in either direction.

The first check to do was to look at how actual match results are distributed.

<p align="center">
  <img src="raw_results.png" />
</p>

<div align="center">
<em>English Premier League Match raw results 2010/11 - 2020/21</em>
</div>

For seasons 2010/11 to 2020/21, there's about 4000 games over the 10 seasons, with some dropping out as unfortunately Lawro doesn't do predictions if a game is rearranged after he's made a prediction (the most recent example being the games cancelled in December 2020 due to COVID). Of these, in the above plot we can see that, as you might expect, there's been a wide variety of results. To make it easier to analyse at the outset, if we standardise results we still get a wide variety.

<p align="center">
  <img src="standardised_results.png" />
</p>

<div align="center">
<em>English Premier League Match standardised results 2010/11 - 2020/21. Results standardised so that mirrored results are counted in the same bucket i.e. 2-1 and 1-2 are included in a single bar</em>
</div>

In terms of working out where the 2-0 hypothesis might stem from, the thing that jumps out is that 2-0 is certainly not the most common result in football, in fact it's only 3rd most common, occurring about 13% of the time.

Comparing that to a raw version of Lawro's predictions is striking

<p align="center">
  <img src="raw_predictions.png" />
</p>

<div align="center">
<em>English Premier League Match raw predictions 2010/11 - 2020/21</em>
</div>


And becomes even more so in the standardised version

<p align="center">
  <img src="standardised_predictions.png" />
</p>

<div align="center">
<em>English Premier League Match standardised predictions 2010/11 - 2020/21</em>
</div>

Firstly, clearly the range of predictions is must narrower, which is perhaps to be expected. It'd probably be unreasonable to think that Lawro would predict a 6-3 where he could just go for a similarly large win.

However, the real standout is the frequency of 2-0 predictions. This can be emphasised by looking at differential between actual and predicted result proportions for the results which Lawro has made predictions with

<p align="center">
  <img src="result_prediction_comparison.png" />
</p>

<div align="center">
<em>Comparison of the ratio of Predictions to ratio of Results, where a positive value means that Lawro predicted more off that result than actually occurred. Data covers EPL 2010/11 - 2020/21</em>
</div>

This plot illustrates what has already been discussed in that Lawro over-predicts 2-0 substantially, but interestingly he also under-predicts 1-0, although not as much. This might be one factor as to why he's so hot on 2-0 but it doesn't make up the whole over prediction.

### Historical performance



### Focussing on specific teams



