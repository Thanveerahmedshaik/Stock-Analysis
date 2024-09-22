# Stock Analysis

In this project we will focus in exploratory data analysis of stock prices.Keep in mind, this project is just meant to practice visualizations and pandas skills, it is not meant to be a robust financial analysis or be taken as financial advice.


We will focus on some US bank stocks and see how they progressed thoughtout the financial crisis of 2016. Then do a quick comparison with last 10 years data.

### Import your important libraries:


```python
import numpy as np
import pandas as pd 
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline
import datetime as dt
import yfinance as yf


```

#### Import plotly and cufflinks for  interactive graphs
#### cufflinks connects pandas dataframe  with plotly library and helps to run the visualizations directly


```python
!pip install plotly
!pip install cufflinks
```



```python

import plotly 
import cufflinks as cf
cf.go_offline()
```


```python
sns.set_style('whitegrid')
```



## Data

We need to get data using yahoo finance.We will get stock information for the following banks:

- Bank of America
- CitiGroup
- GoldmanSachs
- JPMorgan Chase
- Morgan Stanley
- Wells Fargo

Pull th stock data from Jan 1st 2006 to Jan 1st 2016 for each of these banks. Set each bank to be a separate dataframe, with variable name for that bank being its ticker symbol. This will involve few steps:**

1. Use datetime to set start and end datetime objects.
2. Figure out ticker symbol for each bank.
3. Figure out how to use yfinance to grab info on the stock


```python
start = dt.date(year = 2006, month = 1 , day = 1)
end = dt.date(2016,1,1)
```


```python
#pull the stock information from yfinanc
#Process: create a df(BAC)->pull data using yf library

BAC = yf.download('BAC', start, end)          #Bank of America
C = yf.download('C', start, end)              #Citigroup
GS = yf.download('GS', start, end)            #Goldmansachs
JPM = yf.download('JPM', start, end)          #JPMorgan Chase
MS = yf.download('MS', start, end)            #MorganStanley
WFC = yf.download('WFC', start, end)          #WellsFargo
```

    [*********************100%***********************]  1 of 1 completed
    [*********************100%***********************]  1 of 1 completed
    [*********************100%***********************]  1 of 1 completed
    [*********************100%***********************]  1 of 1 completed
    [*********************100%***********************]  1 of 1 completed
    [*********************100%***********************]  1 of 1 completed



```python
WFC
```




<div>



<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2006-01-03</th>
      <td>31.600000</td>
      <td>31.975000</td>
      <td>31.195000</td>
      <td>31.900000</td>
      <td>18.979551</td>
      <td>11016400</td>
    </tr>
    <tr>
      <th>2006-01-04</th>
      <td>31.799999</td>
      <td>31.820000</td>
      <td>31.365000</td>
      <td>31.530001</td>
      <td>18.759415</td>
      <td>10870000</td>
    </tr>
    <tr>
      <th>2006-01-05</th>
      <td>31.500000</td>
      <td>31.555000</td>
      <td>31.309999</td>
      <td>31.495001</td>
      <td>18.738600</td>
      <td>10158000</td>
    </tr>
    <tr>
      <th>2006-01-06</th>
      <td>31.580000</td>
      <td>31.775000</td>
      <td>31.385000</td>
      <td>31.680000</td>
      <td>18.848658</td>
      <td>8403800</td>
    </tr>
    <tr>
      <th>2006-01-09</th>
      <td>31.674999</td>
      <td>31.825001</td>
      <td>31.555000</td>
      <td>31.674999</td>
      <td>18.845688</td>
      <td>5619600</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2015-12-24</th>
      <td>54.970001</td>
      <td>55.090000</td>
      <td>54.709999</td>
      <td>54.820000</td>
      <td>42.477665</td>
      <td>4999400</td>
    </tr>
    <tr>
      <th>2015-12-28</th>
      <td>54.549999</td>
      <td>54.779999</td>
      <td>54.169998</td>
      <td>54.680000</td>
      <td>42.369175</td>
      <td>8288800</td>
    </tr>
    <tr>
      <th>2015-12-29</th>
      <td>55.110001</td>
      <td>55.349998</td>
      <td>54.990002</td>
      <td>55.290001</td>
      <td>42.841831</td>
      <td>7894900</td>
    </tr>
    <tr>
      <th>2015-12-30</th>
      <td>55.270000</td>
      <td>55.310001</td>
      <td>54.790001</td>
      <td>54.889999</td>
      <td>42.531898</td>
      <td>8016900</td>
    </tr>
    <tr>
      <th>2015-12-31</th>
      <td>54.509998</td>
      <td>54.950001</td>
      <td>54.220001</td>
      <td>54.360001</td>
      <td>42.121220</td>
      <td>10929800</td>
    </tr>
  </tbody>
</table>
<p>2517 rows × 6 columns</p>
</div>



#### Create a list of ticker symbols(as strings) in alphabetical order. Call this list: tickers.


```python
tickers = 'BAC C GS JPM MS WFC'.split()
tickers
```




    ['BAC', 'C', 'GS', 'JPM', 'MS', 'WFC']



#### Use pd.concat to concatenate the bank dataframes together to a single dataframe called bank_stocks. Set the key argument equal to the tickers list.


```python
bank_stocks = pd.concat([BAC, C, GS, JPM, MS, WFC], axis = 1,keys=tickers)
bank_stocks
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="6" halign="left">BAC</th>
      <th colspan="4" halign="left">C</th>
      <th>...</th>
      <th colspan="4" halign="left">MS</th>
      <th colspan="6" halign="left">WFC</th>
    </tr>
    <tr>
      <th></th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>...</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2006-01-03</th>
      <td>46.919998</td>
      <td>47.180000</td>
      <td>46.150002</td>
      <td>47.080002</td>
      <td>31.544905</td>
      <td>16296700</td>
      <td>490.000000</td>
      <td>493.799988</td>
      <td>481.100006</td>
      <td>492.899994</td>
      <td>...</td>
      <td>56.740002</td>
      <td>58.310001</td>
      <td>32.661312</td>
      <td>5377000</td>
      <td>31.600000</td>
      <td>31.975000</td>
      <td>31.195000</td>
      <td>31.900000</td>
      <td>18.979551</td>
      <td>11016400</td>
    </tr>
    <tr>
      <th>2006-01-04</th>
      <td>47.000000</td>
      <td>47.240002</td>
      <td>46.450001</td>
      <td>46.580002</td>
      <td>31.209898</td>
      <td>17757900</td>
      <td>488.600006</td>
      <td>491.000000</td>
      <td>483.500000</td>
      <td>483.799988</td>
      <td>...</td>
      <td>58.349998</td>
      <td>58.349998</td>
      <td>32.683727</td>
      <td>7977800</td>
      <td>31.799999</td>
      <td>31.820000</td>
      <td>31.365000</td>
      <td>31.530001</td>
      <td>18.759415</td>
      <td>10870000</td>
    </tr>
    <tr>
      <th>2006-01-05</th>
      <td>46.580002</td>
      <td>46.830002</td>
      <td>46.320000</td>
      <td>46.639999</td>
      <td>31.250097</td>
      <td>14970700</td>
      <td>484.399994</td>
      <td>487.799988</td>
      <td>484.000000</td>
      <td>486.200012</td>
      <td>...</td>
      <td>58.020000</td>
      <td>58.509998</td>
      <td>32.773338</td>
      <td>5778000</td>
      <td>31.500000</td>
      <td>31.555000</td>
      <td>31.309999</td>
      <td>31.495001</td>
      <td>18.738600</td>
      <td>10158000</td>
    </tr>
    <tr>
      <th>2006-01-06</th>
      <td>46.799999</td>
      <td>46.910000</td>
      <td>46.349998</td>
      <td>46.570000</td>
      <td>31.203197</td>
      <td>12599800</td>
      <td>488.799988</td>
      <td>489.000000</td>
      <td>482.000000</td>
      <td>486.200012</td>
      <td>...</td>
      <td>58.049999</td>
      <td>58.570000</td>
      <td>32.806950</td>
      <td>6889800</td>
      <td>31.580000</td>
      <td>31.775000</td>
      <td>31.385000</td>
      <td>31.680000</td>
      <td>18.848658</td>
      <td>8403800</td>
    </tr>
    <tr>
      <th>2006-01-09</th>
      <td>46.720001</td>
      <td>46.970001</td>
      <td>46.360001</td>
      <td>46.599998</td>
      <td>31.223280</td>
      <td>15619400</td>
      <td>486.000000</td>
      <td>487.399994</td>
      <td>483.000000</td>
      <td>483.899994</td>
      <td>...</td>
      <td>58.619999</td>
      <td>59.189999</td>
      <td>33.154228</td>
      <td>4144500</td>
      <td>31.674999</td>
      <td>31.825001</td>
      <td>31.555000</td>
      <td>31.674999</td>
      <td>18.845688</td>
      <td>5619600</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2015-12-24</th>
      <td>17.320000</td>
      <td>17.379999</td>
      <td>17.219999</td>
      <td>17.270000</td>
      <td>14.271372</td>
      <td>29369400</td>
      <td>52.480000</td>
      <td>52.970001</td>
      <td>52.450001</td>
      <td>52.709999</td>
      <td>...</td>
      <td>32.439999</td>
      <td>32.480000</td>
      <td>25.353054</td>
      <td>2798200</td>
      <td>54.970001</td>
      <td>55.090000</td>
      <td>54.709999</td>
      <td>54.820000</td>
      <td>42.477665</td>
      <td>4999400</td>
    </tr>
    <tr>
      <th>2015-12-28</th>
      <td>17.219999</td>
      <td>17.230000</td>
      <td>16.980000</td>
      <td>17.129999</td>
      <td>14.155680</td>
      <td>41777500</td>
      <td>52.570000</td>
      <td>52.570000</td>
      <td>51.959999</td>
      <td>52.380001</td>
      <td>...</td>
      <td>31.950001</td>
      <td>32.169998</td>
      <td>25.111073</td>
      <td>5420300</td>
      <td>54.549999</td>
      <td>54.779999</td>
      <td>54.169998</td>
      <td>54.680000</td>
      <td>42.369175</td>
      <td>8288800</td>
    </tr>
    <tr>
      <th>2015-12-29</th>
      <td>17.250000</td>
      <td>17.350000</td>
      <td>17.160000</td>
      <td>17.280001</td>
      <td>14.279634</td>
      <td>45670400</td>
      <td>52.759998</td>
      <td>53.220001</td>
      <td>52.740002</td>
      <td>52.980000</td>
      <td>...</td>
      <td>32.330002</td>
      <td>32.549999</td>
      <td>25.407690</td>
      <td>6388200</td>
      <td>55.110001</td>
      <td>55.349998</td>
      <td>54.990002</td>
      <td>55.290001</td>
      <td>42.841831</td>
      <td>7894900</td>
    </tr>
    <tr>
      <th>2015-12-30</th>
      <td>17.200001</td>
      <td>17.240000</td>
      <td>17.040001</td>
      <td>17.049999</td>
      <td>14.089570</td>
      <td>35066400</td>
      <td>52.840000</td>
      <td>52.939999</td>
      <td>52.250000</td>
      <td>52.299999</td>
      <td>...</td>
      <td>32.200001</td>
      <td>32.230000</td>
      <td>25.157906</td>
      <td>5057200</td>
      <td>55.270000</td>
      <td>55.310001</td>
      <td>54.790001</td>
      <td>54.889999</td>
      <td>42.531898</td>
      <td>8016900</td>
    </tr>
    <tr>
      <th>2015-12-31</th>
      <td>17.010000</td>
      <td>17.070000</td>
      <td>16.830000</td>
      <td>16.830000</td>
      <td>13.907768</td>
      <td>47153000</td>
      <td>52.070000</td>
      <td>52.389999</td>
      <td>51.750000</td>
      <td>51.750000</td>
      <td>...</td>
      <td>31.770000</td>
      <td>31.809999</td>
      <td>24.830067</td>
      <td>8154300</td>
      <td>54.509998</td>
      <td>54.950001</td>
      <td>54.220001</td>
      <td>54.360001</td>
      <td>42.121220</td>
      <td>10929800</td>
    </tr>
  </tbody>
</table>
<p>2517 rows × 36 columns</p>
</div>



#### Set the column name levels 'Bank Tickers' and 'Stock info':


```python
bank_stocks.columns.names =['Bank Tickers', 'Stock Info']
bank_stocks.columns.names
```




    FrozenList(['Bank Tickers', 'Stock Info'])



#### Check the head of the bank_stocks dataframe


```python
bank_stocks.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th>Bank Tickers</th>
      <th colspan="6" halign="left">BAC</th>
      <th colspan="4" halign="left">C</th>
      <th>...</th>
      <th colspan="4" halign="left">MS</th>
      <th colspan="6" halign="left">WFC</th>
    </tr>
    <tr>
      <th>Stock Info</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>...</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2006-01-03</th>
      <td>46.919998</td>
      <td>47.180000</td>
      <td>46.150002</td>
      <td>47.080002</td>
      <td>31.544905</td>
      <td>16296700</td>
      <td>490.000000</td>
      <td>493.799988</td>
      <td>481.100006</td>
      <td>492.899994</td>
      <td>...</td>
      <td>56.740002</td>
      <td>58.310001</td>
      <td>32.661312</td>
      <td>5377000</td>
      <td>31.600000</td>
      <td>31.975000</td>
      <td>31.195000</td>
      <td>31.900000</td>
      <td>18.979551</td>
      <td>11016400</td>
    </tr>
    <tr>
      <th>2006-01-04</th>
      <td>47.000000</td>
      <td>47.240002</td>
      <td>46.450001</td>
      <td>46.580002</td>
      <td>31.209898</td>
      <td>17757900</td>
      <td>488.600006</td>
      <td>491.000000</td>
      <td>483.500000</td>
      <td>483.799988</td>
      <td>...</td>
      <td>58.349998</td>
      <td>58.349998</td>
      <td>32.683727</td>
      <td>7977800</td>
      <td>31.799999</td>
      <td>31.820000</td>
      <td>31.365000</td>
      <td>31.530001</td>
      <td>18.759415</td>
      <td>10870000</td>
    </tr>
    <tr>
      <th>2006-01-05</th>
      <td>46.580002</td>
      <td>46.830002</td>
      <td>46.320000</td>
      <td>46.639999</td>
      <td>31.250097</td>
      <td>14970700</td>
      <td>484.399994</td>
      <td>487.799988</td>
      <td>484.000000</td>
      <td>486.200012</td>
      <td>...</td>
      <td>58.020000</td>
      <td>58.509998</td>
      <td>32.773338</td>
      <td>5778000</td>
      <td>31.500000</td>
      <td>31.555000</td>
      <td>31.309999</td>
      <td>31.495001</td>
      <td>18.738600</td>
      <td>10158000</td>
    </tr>
    <tr>
      <th>2006-01-06</th>
      <td>46.799999</td>
      <td>46.910000</td>
      <td>46.349998</td>
      <td>46.570000</td>
      <td>31.203197</td>
      <td>12599800</td>
      <td>488.799988</td>
      <td>489.000000</td>
      <td>482.000000</td>
      <td>486.200012</td>
      <td>...</td>
      <td>58.049999</td>
      <td>58.570000</td>
      <td>32.806950</td>
      <td>6889800</td>
      <td>31.580000</td>
      <td>31.775000</td>
      <td>31.385000</td>
      <td>31.680000</td>
      <td>18.848658</td>
      <td>8403800</td>
    </tr>
    <tr>
      <th>2006-01-09</th>
      <td>46.720001</td>
      <td>46.970001</td>
      <td>46.360001</td>
      <td>46.599998</td>
      <td>31.223280</td>
      <td>15619400</td>
      <td>486.000000</td>
      <td>487.399994</td>
      <td>483.000000</td>
      <td>483.899994</td>
      <td>...</td>
      <td>58.619999</td>
      <td>59.189999</td>
      <td>33.154228</td>
      <td>4144500</td>
      <td>31.674999</td>
      <td>31.825001</td>
      <td>31.555000</td>
      <td>31.674999</td>
      <td>18.845688</td>
      <td>5619600</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 36 columns</p>
</div>



## Exploratory Data Analysis

#### _Let's explore the data a bit! What is the max Close price for each bank's stock throughout the time period_


```python
# use cross section for multiindex data frames

bank_stocks.xs('Close',axis=1,level=1) # Returns the dataframe with Close columns

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Bank Tickers</th>
      <th>BAC</th>
      <th>C</th>
      <th>GS</th>
      <th>JPM</th>
      <th>MS</th>
      <th>WFC</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2006-01-03</th>
      <td>47.080002</td>
      <td>492.899994</td>
      <td>128.869995</td>
      <td>40.189999</td>
      <td>58.310001</td>
      <td>31.900000</td>
    </tr>
    <tr>
      <th>2006-01-04</th>
      <td>46.580002</td>
      <td>483.799988</td>
      <td>127.089996</td>
      <td>39.619999</td>
      <td>58.349998</td>
      <td>31.530001</td>
    </tr>
    <tr>
      <th>2006-01-05</th>
      <td>46.639999</td>
      <td>486.200012</td>
      <td>127.040001</td>
      <td>39.740002</td>
      <td>58.509998</td>
      <td>31.495001</td>
    </tr>
    <tr>
      <th>2006-01-06</th>
      <td>46.570000</td>
      <td>486.200012</td>
      <td>128.839996</td>
      <td>40.020000</td>
      <td>58.570000</td>
      <td>31.680000</td>
    </tr>
    <tr>
      <th>2006-01-09</th>
      <td>46.599998</td>
      <td>483.899994</td>
      <td>130.389999</td>
      <td>40.669998</td>
      <td>59.189999</td>
      <td>31.674999</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2015-12-24</th>
      <td>17.270000</td>
      <td>52.709999</td>
      <td>182.470001</td>
      <td>66.599998</td>
      <td>32.480000</td>
      <td>54.820000</td>
    </tr>
    <tr>
      <th>2015-12-28</th>
      <td>17.129999</td>
      <td>52.380001</td>
      <td>181.619995</td>
      <td>66.379997</td>
      <td>32.169998</td>
      <td>54.680000</td>
    </tr>
    <tr>
      <th>2015-12-29</th>
      <td>17.280001</td>
      <td>52.980000</td>
      <td>183.529999</td>
      <td>67.070000</td>
      <td>32.549999</td>
      <td>55.290001</td>
    </tr>
    <tr>
      <th>2015-12-30</th>
      <td>17.049999</td>
      <td>52.299999</td>
      <td>182.009995</td>
      <td>66.589996</td>
      <td>32.230000</td>
      <td>54.889999</td>
    </tr>
    <tr>
      <th>2015-12-31</th>
      <td>16.830000</td>
      <td>51.750000</td>
      <td>180.229996</td>
      <td>66.029999</td>
      <td>31.809999</td>
      <td>54.360001</td>
    </tr>
  </tbody>
</table>
<p>2517 rows × 6 columns</p>
</div>




```python
bank_stocks.xs('Close',axis=1,level=1).max() #Returns the max value of each column
```




    Bank Tickers
    BAC     54.900002
    C      564.099976
    GS     247.919998
    JPM     70.080002
    MS      89.300003
    WFC     58.520000
    dtype: float64



#### Create a new empty DataFrame called returns. The dataframe will contain the returns for each bank's stock. returns are typically defined by 
$$
r_t = \frac{P_t - P_{t-1}}{ P_t-1} = \frac{P_t}{P_{t-1}}-1 
$$



```python
returns = pd.DataFrame()
print(returns)
```

    Empty DataFrame
    Columns: []
    Index: []


#### Use pandas pct_change() method on the Close column to create a column representing this return value. Create a for loop that goes and for each Bank Stock Ticker creates this returns column and sets it as a column in the returns DataFrame.


```python
for i in tickers: 
    returns[i + ' Returns'] = bank_stocks.xs('Close', axis = 1, level = 1)[i].pct_change() 
    
```


```python
returns

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>BAC Returns</th>
      <th>C Returns</th>
      <th>GS Returns</th>
      <th>JPM Returns</th>
      <th>MS Returns</th>
      <th>WFC Returns</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2006-01-03</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2006-01-04</th>
      <td>-0.010620</td>
      <td>-0.018462</td>
      <td>-0.013812</td>
      <td>-0.014183</td>
      <td>0.000686</td>
      <td>-0.011599</td>
    </tr>
    <tr>
      <th>2006-01-05</th>
      <td>0.001288</td>
      <td>0.004961</td>
      <td>-0.000393</td>
      <td>0.003029</td>
      <td>0.002742</td>
      <td>-0.001110</td>
    </tr>
    <tr>
      <th>2006-01-06</th>
      <td>-0.001501</td>
      <td>0.000000</td>
      <td>0.014169</td>
      <td>0.007046</td>
      <td>0.001025</td>
      <td>0.005874</td>
    </tr>
    <tr>
      <th>2006-01-09</th>
      <td>0.000644</td>
      <td>-0.004731</td>
      <td>0.012030</td>
      <td>0.016242</td>
      <td>0.010586</td>
      <td>-0.000158</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2015-12-24</th>
      <td>-0.004037</td>
      <td>0.001520</td>
      <td>-0.002624</td>
      <td>-0.001948</td>
      <td>-0.003681</td>
      <td>-0.003997</td>
    </tr>
    <tr>
      <th>2015-12-28</th>
      <td>-0.008107</td>
      <td>-0.006261</td>
      <td>-0.004658</td>
      <td>-0.003303</td>
      <td>-0.009544</td>
      <td>-0.002554</td>
    </tr>
    <tr>
      <th>2015-12-29</th>
      <td>0.008757</td>
      <td>0.011455</td>
      <td>0.010516</td>
      <td>0.010395</td>
      <td>0.011812</td>
      <td>0.011156</td>
    </tr>
    <tr>
      <th>2015-12-30</th>
      <td>-0.013310</td>
      <td>-0.012835</td>
      <td>-0.008282</td>
      <td>-0.007157</td>
      <td>-0.009831</td>
      <td>-0.007235</td>
    </tr>
    <tr>
      <th>2015-12-31</th>
      <td>-0.012903</td>
      <td>-0.010516</td>
      <td>-0.009780</td>
      <td>-0.008410</td>
      <td>-0.013031</td>
      <td>-0.009656</td>
    </tr>
  </tbody>
</table>
<p>2517 rows × 6 columns</p>
</div>



**Observations**

- _What pct_change() does here is it computes the fractional change from the immidiately previous row by default. It is useful for comparing the fraction of change in a time series of elements. Remeber here it is a fraction if yoou want to change it to percentage u want to multiply it with 100_
- _So we have got the columns for each of the stock tickers for each trading day_
- _For the first day the return is obiviously **Nan** as there was no prior day element to calculate it_
- _From the second row we can see the positive and negative returns , negatives are the days where it went down and positives are the days when it went up_

#### Create a pair plot using seaborn of the returns dataframe. Do any stocks stands out ? Why or why not?


```python
sns.pairplot(returns)
```




    <seaborn.axisgrid.PairGrid at 0x3392183a0>




    
![png](output_29_1.png)
    


**Observations**
- _Diagonal elements are univariant data i.e, just for that particular column_
- _Off diagonal elements are bivariant data_
- _What pairplot does here is it creates a group of scatter plots for each pair of the numerical data in the data frame,For exit will create a 'BAC' vs 'C' scatterplot, 'BAC' vs 'GS' scatter plot and so on._
- _So we have all of these tickers/banks on X-axis and the same banks as well as on the Y-axis_.
- _So if you compare 'WFC Returns' to the 'WFC Returns' it is a univariant data so therefore we have a histogram._
- _If you compare 'WFC Returns' to the other banks we have the scatter plots to analyse the data_
- _Its very important to check the scales for it , almost everything are in decimals on both X and Y axis._
- _Sometimes based on the data you can get different scales for one of the columns and that graph will look very different._
- _ 



```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python

```


```python
$$
r_t = \frac{P_t - P_t-1}{ P_t-1} -
$$
```


      Cell In[124], line 1
        $$
        ^
    SyntaxError: invalid syntax




```python

```
