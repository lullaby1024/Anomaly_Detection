# Anomaly Detection
## Introduction
- This project applies LSTM autoencoders in Keras to detect anomalies with the S&P 500 time series data. By detecting anomalies, this project aims to answer questions like when one should sell/buy a stock and how early can one capture the anomalies in order to make a decision.

## Data Ingestion
- **Data Source**: [S&P500 Daily Prices 1986-2018](https://www.kaggle.com/pdquant/sp500-daily-19862018).
  - The S&P 500, or simply the S&P, is a stock market index that measures the stock performance of 500 large companies listed on stock exchanges in the United States. It is one of the most commonly followed equity indices, and many consider it to be one of the best representations of the U.S. stock market ([Wikipedia](https://en.wikipedia.org/wiki/S%26P_500_Index)).
  - 8192 rows
  - 2 columns: `date` and `close`, which is the daily closing price.
  
## Data Preprocessing
### Train-test-split
- Since the time series objects are NOT independent, the training and test set were splitted in sequence, with the first 90% of data as the training set and the rest as the test set.
- The training set was then splitted to a validation set with a proportion of 10%.
### Standardization
- The closing prices are standardized to have 0 mean and unit variance.

## Temporalizing data for train-test-split
- Subsequences were created with a time step of 30 so that a 30-day window was taken into consideration.

## Model
<img src="https://github.com/lullaby1024/Anomaly_Detection/blob/master/model.png" width="30%">

- **Baseline**: 32 units in LSTM layers, 0.2 dropout rate, 0.01 learning rate
- **Tuned model**: 128 units in LSTM layers, 0.2 dropout rate, 0.001 learning rate
  - Tuning method: Hyperband
- Early stopping was applied in training to prevent overfitting. 
  - Patience was set to 3 so that no improvement in 3 epochs will terminate training.


## Detecting Anomalies
- A data point is identified as anomaly if its test loss exceeds the **threshold**.
- From the distribution of training loss (MAE), since most training losses fall below 0.7, we will set the `threshold = 0.7`.
