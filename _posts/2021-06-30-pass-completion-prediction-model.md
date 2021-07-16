---
layout: post
title: Exploring how well a Pass Completion Prediction model can work
subtitle: An (unsuccessful) adventure into identifying whether a model can acheive acceptable accuracy for pass prediction
# cover-img: /assets/img/path.jpg
# thumbnail-img: /assets/img/thumb.png
# share-img: /assets/img/path.jpg
tags: [sports analytics, football, xgboost, classification, supervised learning]
---


# Exploration of whether Pass Completion can be successfully predicted
Pass completion is inherently uncertain with many factors outside of a passer's sheer ability affecting whether or not the ball reaches an intended target. I wanted to investigate how well pass completion can be predicted from a minimal set of variables, to understand whether an ML algorithm can identify factors which are important for passing compared to factors which are traditionally prioritised.

## Data
The data used here is taken from Statsbomb, and in particular I've focussed on the Messi biographical dataset. This dataset includes every single game that Messi has played in in La Liga, from 2004 to present, comprising 459 matches and about 480,000 passes. 
