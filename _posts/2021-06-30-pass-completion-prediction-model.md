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
[Add statsbomb logo]
The data used here is taken from Statsbomb, and in particular I've focussed on the Messi biographical dataset. This dataset includes every single game that Messi has played in in La Liga, from 2004 to present, comprising 459 matches and about 480,000 passes. 

[ADD HEATMAP/PASSMAP of all passes]

The data used is clearly centred on Barcelona, but I wanted to limit this study to a single league so that factors like style of play, disparities in capability of opponents, etc. were consistent across the full dataset. Using a single division made it more consistent than it might have been using multiple leagues/competitions. An extension of this work might be to understand whether generalising across all passes held in the Statsbomb dataset might produce a more robust model, but that is out of scope for here!

The data increases fairly incrementally from 2004/05 to 2006/07 (CHECK THESE DATES) when Messi became a first team mainstay (and therefore more games are included each season), peaking at about 38,000 passes per season. 

[Add nice line plot/bar chart of passes over seasons

## Cleaning up the place
Statsbomb data is pretty clean out of the box, and really the main work is re-structuring it into a format that I can use for a classification problem. The target variable to predict is the Pass Outcome, which in Statsbomb data is pre-coded, making our lives much easier.

[Add bar chart/pie chart showing distribution of outcome]

To make my problem a binary classification, I adapted the Pass Outcome field to either be Success (pretty self explanatory) or Unsuccessful, which encapsulated Incomplete and Out passes. I discarded Injury Clearance, Pass Offside and Unknown for a range of reasons:
- Injury Clearance is a deliberately failed pass (and there's a very small number of them, 8) so these were discarded to prevent appearing as noise. 
- Pass Offside are successful passes where the receiver is positioned incorrectly relative to defensive opponents. I wasn't confident the model would appreciate the nuance of this so removed from consideration
- Unknown were removed to ensure a clean division between Success and Unsuccessful. Unknown outcome is indicative of some data quality issue/ambiguity, which wouldn't be helpful for the model

With this encoding in place, the split of the data is more clear: there's about 450,000 passes left, with about 395,000 a success, and about 55,000 incomplete passes overall. This represnts a split of about 15% incomplete to 85% complete, which while not enormously problematic, needs consideration if only in the performance metrics selected to assess the model.

Before moving onto training the model, feature selection needed to take place as the Statsbomb data included a variety of fields many of which are not relevant for passing. The fields selected to train the model on were:
- position_name
- pass_length
- pass_angle
- pass_start
- pass_end
- pass_height_name
- pass_body_part_name
- pass_type_name
- under_pressure
- pass_switch
- pass_outswinging
- pass_through_ball
- pass_inswinging
- pass_no_touch
- pass_cutback
- pass_straight

The following pre-processing was carried out on these fields:
- The vast majority of these fields were Categorical, so were One Hot Encoded to produce a field which is easily used with a variety of classifiers.
- pass_start and pass_end were split into two more fields each, one for each of the X and Y coordinates.
- pass_start and pass_end X and Y values were scaled between 0 and 1. This change made no material difference to the model performance, but improved readability of the data both before and after training, without needing to know the specific pitch dimensions that were used in the data.

A field which was debated for engineering was the under_pressure field, however this is usefully provided by Statsbomb. To sense check the implementation of this column, a manual check was carried out. This involved:
- Taking one season of data (2004/05 here)
- Selecting all passes from this season, 786 in total
- Exploding out the 'related_events' column, so that a join can be done on the event_id values which are exploded
- Join on event ID with the non-pass events data, and filter where the event which has been joined on is 'Pressure'
- Check that the 'under_pressure' field is True for all of these events

This check is clearly not exhaustive, but gives an indication that the under_pressure field has been constructed in a similar way to how it would be manually generated otherwise. The converse was not checked (Under_pressure being False when no pressure joined) as there can be multiple related events which made this check more tricky. If there was concern about the quality of the field then additional checks would be possible, but were not warranted here.

The final step is to prepare the data for train and test. In order to emulate how this type of approach might (with a massive emphasis on might) be used in a professional capacity by a club, the data split has been to use all seasons up to the most recent season available (2019/20) for training, and then test the model on 2019/20. This has an obvious benefit of giving the model more data to train on but means that the robustness of the model should be checked as the test dataset is smaller than ideally. Ultimately this is a simple experiment, so this split setup will work fine for these purposes.

## Model training
The model training stage here is an example of a classic supervised ML model selection approach. A range of supervised classifiers were trialled, including:
- Logistic Regression
- Linear SVC
- Decision Tree
- Random Forest
- XGBoost

Each of these models was cross-validated with 5 folds to try and get a robust impression of classifier performance. Each of these classifiers was tried with the default hyperparameters with fine-tuning to occur later

The results of this search were as follows:
| Logistic Regression	| Support Vector Classifier	| Decision Tree | Random Forest	| XGBoost	| Best Performing Classifer |
| ------ | ------ | ------ | ------ | ------ | ------ | ------ |
| Precision | 0.897975	| 0.889951	| 0.919356	| 0.920504	| 0.921915	| XGBoost |
| Recall	| 0.974013	| 0.983700	| 0.913391	| 0.966392	| 0.969396	| Support Vector Classifier |
| F1 Score	| 0.934442	| 0.934476	| 0.916358	| 0.942883	| 0.945053	| XGBoost |

Perhaps unsurprisingly XGBoost performs best in both Precision and F1 Score, and is third placed for Recall. We'll move forward with XGBoost, especially given its known pedigree.

The final training step before assessing model performance is then a hyperparameter search. This was carried out in two stages:
- An initial Stratified 3-fold Grid Search to determine learning rate and number of estimators - this identified X estimators and a learning rate of N as being the best approach
- A second Stratified 3-fold Randomized Search over a number of other hyperparameters (min_child_weight, gamma, subsample, colsample_bytree, max_depth) with learning rate and number of estimators fixed at the values found in the first search. This method is preferred as it helps identify the most important hyperparameters first and then gets the best out of them with tweaks on the additional variables.


