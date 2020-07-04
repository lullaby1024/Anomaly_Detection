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
<img src="https://github.com/lullaby1024/Anomaly_Detection/blob/master/img/model.png" width="35%">

- **Baseline**: 32 units in LSTM layers, 0.2 dropout rate, 0.01 learning rate
- **Tuned model**: 128 units in LSTM layers, 0.3 dropout rate, 0.001 learning rate
  - Tuning method: Hyperband
- Early stopping was applied in training to prevent overfitting. 
  - Patience was set to 3 so that no improvement in 3 epochs will terminate training.

## Detecting Anomalies
- A data point is identified as anomaly if its test loss exceeds the **threshold**.
- From the distribution of training loss (MAE), since most training losses (90%) fall below 0.7, threshold was set to 0.7.
<img src="https://github.com/lullaby1024/Anomaly_Detection/blob/master/img/test_loss.png" width="65%">
<img src="https://github.com/lullaby1024/Anomaly_Detection/blob/master/img/anomalies.png" width="65%">
<img src="https://github.com/lullaby1024/Anomaly_Detection/blob/master/img/anomalies_zoomed.png" width="65%">

## Historical Research and Business implications
- Anomalies occurred at early February, 2018 and late March, 2018.

### February, 2018
- There was a steady increasing trend in the closing prices until the sharp drop in February.
  - **Historical evidence**: "In early February, the runaway train stock market ran smack into spiking bond rates that were pricing in the threat of inflation. Investors suddenly became worried the economy, boosted by huge tax cuts, could overheated and force the Federal Reserve to raise interest rates." Such inflation fears caused the S&P Index to decline drastically.
    - *Reference: [February was an insane month for the stock market](https://money.cnn.com/2018/02/28/investing/stock-market-february-dow-jones/index.html)*
    
### March, 2018
- A high votality was observed at the end of March.
  - **Historical evidence**: March 2018 was marked as the worst March in 17 years for the S&P Index. "Investors wrestled with import tariffs announced by President Donald Trump’s administration on China and jitters around the Federal Reserve’s ability to avoid pushing the economy into recession as it normalizes monetary policy from crisis-era levels, against a backdrop of fiscal stimulus that risks overheating an economy that is in its ninth year of expansion."
    - *Reference: [March Madness: The Dow is on the brink of logging its ugliest March loss in nearly 40 years](https://www.marketwatch.com/story/dow-industrials-threaten-to-log-their-ugliest-march-loss-in-17-years-2018-03-22)*

### Insights
- The LSTM network seems to capture the anomalies in S&P Index well as evidenced by historical data.
- The predictions by the network was trained on a 30-day window.
  - In other words, we can utilize the network to forecast a drastic change in the stock market as early as 30 days before the change takes place.
