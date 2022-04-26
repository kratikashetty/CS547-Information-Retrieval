# Automated Essay Scoring


## Team Members: 
Vedhas Vinjamuri, Kratika Shetty

## Background and Motivation
In any language exam, the ability to write composition is an essential indicator of academic performance. However, assessing these essays is a difficult task. The demand for objective and speedy scoring has prompted the development of a tool that can evaluate essays.

There has been a substantial amount of study on the topic of automated essay scoring. One of the early publications uses logistic regression and SVMs on essay representations to get a decision boundary. These models treat the problem as a multi-class classification task. Due to the recent developments in deep learning models and word embedding techniques, there has been significant improvement in the performance of Automated Essay Scoring tools.

## Dataset
### The Hewlett Foundation Data
We used the data available on [Kaggle](https://www.kaggle.com/competitions/asap-aes/data) Competition conducted by The Hewlett Foundation. There are eight essay sets available for this competition. A single prompt was used to produce each batch of essays. 

<p align="middle">
  <img src="Images/essay_overview.png" width="450" />
  <img src="Images/promtps.png" width="450" /> 
</p>

### Columns

| Column |Description|
|-------|--------|
| essay_set | 1-8, an id for each set of essays |
| domain1_score |  Resolved score between the raters; all essays have this | 

## Exploratory Data Analysis

### Essay Set Distribution 

Essays are not equally distributed in the dataset. Different essay sets have different number of essays available. 

The scores for each prompt have different scoring criteria. Minimum and Maximum score that can be graded for each essay is different.

<p align="middle">
  <img src="Images/essay_boxplot.png" width="450" />
  <img src="Images/essay_pie.png" width="450" /> 
</p>


## Feature Engineering

Pressure is a function of past valve settings: p[i] = f(u_in[:i]). But u_in is not an independent variable, u_in is the output of a controller, and the inputs of the controller are the past measured pressures: u_in[i] = g(p[:i+1]). Hence in order to get data from previous time steps data was preprocessed as follows. Following features were added - 

 * New Lag features for u_in
 * Exponential Moving Mean, Standard Deviation and correlation of u_in for each breath ID 
 * Rolling Mean, Standard Deviation and Maximum of u_in for each breath. Here the size of the moving window is 10
 * Expanding Mean, Standard Deviation and Maximum of u_in for each breath ID where size of minimum period is 2
 * R and C after converting into indicator variables



## Implementation

Since we are using a Dataset from a kaggle competition, we were unable to to get the true Y values for the test data. We split the trainng data as follows to get the training and test data - 

**Training Data** 70% of the Total Breath IDs = 52,815 Breath IDs

**Test Data** 30% of the Total Breath IDs = 22,635 Breath IDs


### XGBOOST Using XGBRegressor

XGBoost was first considered for modeling the training data since it can be used for regression predictive modeling. We also used repeated 5-fold cross-validation to evaluate and pressure was found out by averaging pressure across multiple runs. After the training data fit into the XGBoost model, the result is generated shown below:

<p align="middle">
  <img src="Images/xgboost.png" width="450" />
</p>

**Feature Importance** Feature Importance provides a score that indicates how useful or valuable each feature was in the construction of the boosted decision trees within the model. Here, the features are automatically named according to their index in the input array. Columns id, time_step and u_out have the top three importance score. 

<p align="middle">
  <img src="Images/feature_importance.png" width="450" />
 <img src="Images/xgboost_index.png" width="450" />
</p>

Here top 3 features fO, f2, f5 corresponds to id, time_step and u_out.

### Bi-LSTM Model 

Stacked Bi-LSTMs Model was implemented in Keras. Bidirectional Long Short-Term Memory (Bi-LSTM) networks was implemented as they are capable of learning order dependence in sequence prediction problems. LSTM networks are well-suited to classifying, processing and making predictions based on time series data.

5-fold cross validation was performed and avaerage pressure was calculated after 5 runs.

Model parameters - 
 * Bi-LSTM - 4 Layers
 * 2 Dense Layers in the output layer with ReLu activation

Following Mean Absolute Error, R-squared and Mean Squared Error were obtained - 

<p align="middle">
  <img src="Images/lstm_error.jpeg" width="450" />
</p>


## Conclusion

1. Bi-LSTM Model performed  better than Xgboost.
2. MAE and MSE of Bi-LSTM model was better than that of Xgboost

| | Bi-LSTM |XGBoost|
|-------|--------|--------|
| Mean Squared Error | 0.1403 | 0.4471 |
| Mean Absolute Error | 0.1903 | 0.387 |

4. Good scores were obtained with 3 to 5 layers Bi-LSTM layers.

## GitHub Repository -  

Here is the link for the [repository](https://github.com/anamika1302/CS539-Ventilator-Pressure-Prediction) 

### References
1. https://machinelearningmastery.com/xgboost-for-time-series-forecasting/
2. https://www.kaggle.com/theoviel/deep-learning-starter-simple-lstm 
3. https://medium.com/geekculture/10-hyperparameters-to-keep-an-eye-on-for-your-lstm-model-and-other-tips-f0ff5b63fcd4
4. https://www.kaggle.com/ranjeetshrivastav/ventilator-pressure-prediction-xgboost/notebook
