## Improving a pass completion model and assessing what the model shows (Work In Progress)

This piece builds on an earlier article [Exploring how well a Pass Completion Prediction model can work](https://cattmooper.github.io/2021-06-30-pass-completion-prediction-model/) written in June 2021 where I developed a pass completion model using Statbomb's Messi Biography data.

Since writing that article, a number of material changes have been made to the approach taken in order to improve the clarity of the model's outputs, as well as to try and improve model performance. I'll run through them briefly here, before looking at what the model tells us about players in the dataset.

### Updates to approach
The following updates were made to the model and the approach:
1. The most substantial was to flip what was being predicted from predicting unsuccessful passes, to predicting successful passes, meaning the majority class is what you are trying to predict. Although this doesn't make much difference to the metrics, it makes looking at player performance a bit more logical
2. I removed Player Position as a feature following some feedback. On reflection I wanted to try and generalise as much as possible, as well as to explore how this model might be used in a practical sense (i.e. tactical insight) and having Player Position doesn't offer much from a tactical interpretation point of view, and it was also a feature with a very low feature importance, so wasn't improving performance of the model either.
3. I changed the train/test split approach from using 2004/05 to 2015/16 as the training set and 2016/17-2019/20 as the testing set, to implementing a stratified random 70/30 split. The hope was that this would produce more generalised results where playing style has varied over the seasons.
4. I introduced the last action prior to the pass as a feature. I saw a slight performance increase using this, and has potential to add tactical benefit by providing insight on the types of pass that work best after a given event.
5. I also implemented a Multi Layer Perceptron model into the suite of models tested, to explore the value that some basic Deep Learning offered to this problem. The initial article will be updated to include the accuracy results of this, but XGBoost remained the best performing model.

These changes haven't made a huge difference to the performance of the model, but should help improve robustness of the approach (item #3 in particular), and change #1 will make the next section easier to interpret.

### What does the model show

So the whole point of producing a model like this is to try and identify players who are performing above (or below!) the level of accuracy which the model expects for their passing.

The way that the model assesses performance is as follows:
1. The model makes a prediction about whether a given pass is going to be completed as well as a probability
2. Find the difference between the outcome (either a 0 or a 1) and the probability of completion given by the model (a float between 0 and 1). Since implementing this I've subsequently found that Opta's pass completion model [uses the exact same approach](https://theanalyst.com/eu/2021/09/expected-pass-completion-explained/) to evaluate the success of their model (called Passes Completed Above Expectation in their model). What's nice about this approach is that it rewards hard passes which are completed unexpectedly (Outcome of 1 - a low probability of say 0.1 = 0.9) and penalises easy passes which are missed (Outcome of 0 - a high probability of say 0.75 = -0.75). In my work this field is called Pass Completion Reward (PCR)
3. For each player, we then sum PCR to find out overall how many passes they complete which are above the expectation of the model

Analysing the full testing set, and sorting by PCR Sum per 100 passes, we get the following results. These results are the top 15 players by PCR per 100 passes, and the list is filtered to exclude any player with less than 250 passes in the data we're using, to prevent any one-pass-wonders topping the charts:

| Player Name | Team Name | Passes Attempted | Actual Passes Completed | Predicted Passes Completed | Raw sum of Pass Completion Reward (PCR) | PCR per 100 passes attempted |
| :----- | :----- | :----- | :----- | :------ | :----- | :----- |
| Lionel Messi | Barcelona | 7093 | 6054 |	6113	| 1685.498563 | 	23.762845 |
| Antoine Griezmann |Barcelona | 410 | 342 | 345 |	95.881281 | 23.385678 |
| Ronaldinho | Barcelona | 433 |	 351 | 362 | 98.512247 |	22.751096 |
| Arda Turan | Barcelona | 417 | 371 | 368 | 93.826063 | 22.500255 |
| Neymar Junior | Barcelona |	1530	| 1271 | 1320 | 322.467408 | 21.076301 |
| Frenkie de Jong | Barcelona |	401 | 380 | 380 | 84.305775 | 21.023884 |
| Dani Alves  | Barcelona | 4966 | 4328 | 4393 | 1036.986024 | 20.881716 |
| Sergi Roberto | Barcelona | 2483 | 2320 | 2303 | 516.886395 | 20.817011 |
| Jordi Alba | Barcelona | 4267 | 3853 |3871 | 881.036320 | 20.647676 |
| Cesc Fàbregas i Soler | Barcelona | 1559 | 1402 | 1415 | 321.39970 | 20.615760 |
| Rafael Márquez | Barcelona | 1137 | 1025 | 1023 | 233.158047 | 20.506425 |
| Andrés Iniesta | Barcelona |	5384 | 4873 | 4957 | 1097.203306 | 20.378962 |
| Arturo Vidal | Barcelona | 695	| 644 |644 | 141.626727 | 20.377946 |
| Eric Abidal | Barcelona |	1936 | 1748 | 1744 | 390.942470 | 20.193309 |
| Denis Suárez	| Barcelona | 337 | 293 | 298 |	67.533659 | 20.039661 |


* Note that some names in the above have been cleaned slightly for readability

Immediately this flags out an issue. As we've had to filter out anything below 250 passes, the only players included are Barcelona players, so the insight that this model can provide is limited from a scouting-type perspective.

However, top of our list is Messi, which is usually reassuring. I'm not convinced that it's neccesarily reassuring for a passing model, but given that players like Iniesta are also near the top of the list, it's probably not a bad sign.

### Analysing Messi's passing
To understand why Messi is top of the list, it's useful to dive into what the model things of some of his passing, so understand when and where he overperforms expectations

[FROM HERE, THE INTENTION IS TO INCLUDE SOME PLOTS OF WHERE HIS RISKIEST PASSES OCCUR WHICH ARE BOTH SUCCESSFUL AND UNSUCCESSFUL]
