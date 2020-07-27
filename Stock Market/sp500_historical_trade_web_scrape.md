# Web scrape historical stock trade data

A short script to extract S&P 500 historical trade data. The script uses Pandas data reader module to scrape data from yahoo finance. A list of ticker symbols is referenced from a text file (sp500_Components) within the programs folder. 
<br><br> sp500_Components source: https://raw.githubusercontent.com/datasets/s-and-p-500-companies/master/data/constituents.csv



```python
import pandas as pd
import pandas_datareader.data as web
from pandas import DataFrame
```


```python
def compile_historical_df(start,end):
    sp500_components = pd.read_csv("sp500_Components.txt")
    sp500_ticker = sp500_components['Symbol']
    Symbol = sp500_ticker
    historical_df = DataFrame([])
    for i in enumerate(Symbol):
        try:
            df = web.DataReader(i[1], 'yahoo', start, end)
            df.insert(1,'Symbol',i[1])

            historical_df = historical_df.append(df)
        except: 
            continue
       
    return historical_df
    historical_df.to_csv('historical_base.csv')
    
compile_historical_df('12-31-2010','12-31-2020')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Date</th>
      <th>High</th>
      <th>Symbol</th>
      <th>Low</th>
      <th>Open</th>
      <th>Close</th>
      <th>Volume</th>
      <th>Adj Close</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>2010-12-31</td>
      <td>86.980003</td>
      <td>MMM</td>
      <td>86.010002</td>
      <td>86.529999</td>
      <td>86.300003</td>
      <td>1791500.0</td>
      <td>67.057274</td>
    </tr>
    <tr>
      <td>2011-01-03</td>
      <td>87.330002</td>
      <td>MMM</td>
      <td>86.739998</td>
      <td>86.750000</td>
      <td>86.790001</td>
      <td>2632800.0</td>
      <td>67.438019</td>
    </tr>
    <tr>
      <td>2011-01-04</td>
      <td>87.279999</td>
      <td>MMM</td>
      <td>86.279999</td>
      <td>87.000000</td>
      <td>86.669998</td>
      <td>2644100.0</td>
      <td>67.344765</td>
    </tr>
    <tr>
      <td>2011-01-05</td>
      <td>87.900002</td>
      <td>MMM</td>
      <td>86.120003</td>
      <td>86.290001</td>
      <td>86.669998</td>
      <td>4081300.0</td>
      <td>67.344765</td>
    </tr>
    <tr>
      <td>2011-01-06</td>
      <td>87.190002</td>
      <td>MMM</td>
      <td>85.629997</td>
      <td>86.860001</td>
      <td>86.139999</td>
      <td>3452600.0</td>
      <td>66.932930</td>
    </tr>
    <tr>
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
      <td>2020-05-15</td>
      <td>127.959999</td>
      <td>ZTS</td>
      <td>125.309998</td>
      <td>126.440002</td>
      <td>127.489998</td>
      <td>2921700.0</td>
      <td>127.489998</td>
    </tr>
    <tr>
      <td>2020-05-18</td>
      <td>132.539993</td>
      <td>ZTS</td>
      <td>129.800003</td>
      <td>130.179993</td>
      <td>131.419998</td>
      <td>2762800.0</td>
      <td>131.419998</td>
    </tr>
    <tr>
      <td>2020-05-19</td>
      <td>135.759995</td>
      <td>ZTS</td>
      <td>130.080002</td>
      <td>131.050003</td>
      <td>134.339996</td>
      <td>3335300.0</td>
      <td>134.339996</td>
    </tr>
    <tr>
      <td>2020-05-20</td>
      <td>137.070007</td>
      <td>ZTS</td>
      <td>133.039993</td>
      <td>136.199997</td>
      <td>133.339996</td>
      <td>2303400.0</td>
      <td>133.339996</td>
    </tr>
    <tr>
      <td>2020-05-21</td>
      <td>133.889999</td>
      <td>ZTS</td>
      <td>129.899994</td>
      <td>133.789993</td>
      <td>130.330002</td>
      <td>1413100.0</td>
      <td>130.330002</td>
    </tr>
  </tbody>
</table>
<p>1105019 rows × 7 columns</p>
</div>



## Function for individual stock pull


```python
def compile_df(symbol,start,end,export_data): 
    df = web.DataReader(symbol,'yahoo',start,end)
    df.insert(1,'Symbol',symbol)
    
    if export_data == 'yes':
        df.to_csv(symbol + '.csv')
    
    else:
        pass
    
    return df.sort_index(axis = 0, ascending=False)

compile_df('MGK','12-31-2010','12-31-2020','yes')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Date</th>
      <th>High</th>
      <th>Symbol</th>
      <th>Low</th>
      <th>Open</th>
      <th>Close</th>
      <th>Volume</th>
      <th>Adj Close</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>2020-05-21</td>
      <td>154.429993</td>
      <td>MGK</td>
      <td>151.949997</td>
      <td>153.910004</td>
      <td>152.550003</td>
      <td>371500.0</td>
      <td>152.550003</td>
    </tr>
    <tr>
      <td>2020-05-20</td>
      <td>154.009995</td>
      <td>MGK</td>
      <td>152.789993</td>
      <td>152.860001</td>
      <td>153.820007</td>
      <td>387100.0</td>
      <td>153.820007</td>
    </tr>
    <tr>
      <td>2020-05-19</td>
      <td>153.080002</td>
      <td>MGK</td>
      <td>150.990005</td>
      <td>151.619995</td>
      <td>151.009995</td>
      <td>347200.0</td>
      <td>151.009995</td>
    </tr>
    <tr>
      <td>2020-05-18</td>
      <td>152.300003</td>
      <td>MGK</td>
      <td>150.380005</td>
      <td>150.929993</td>
      <td>151.619995</td>
      <td>457200.0</td>
      <td>151.619995</td>
    </tr>
    <tr>
      <td>2020-05-15</td>
      <td>148.240005</td>
      <td>MGK</td>
      <td>145.039993</td>
      <td>145.490005</td>
      <td>148.240005</td>
      <td>360300.0</td>
      <td>148.240005</td>
    </tr>
    <tr>
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
      <td>2011-01-06</td>
      <td>47.990002</td>
      <td>MGK</td>
      <td>47.750000</td>
      <td>47.990002</td>
      <td>47.880001</td>
      <td>231700.0</td>
      <td>42.349472</td>
    </tr>
    <tr>
      <td>2011-01-05</td>
      <td>47.930000</td>
      <td>MGK</td>
      <td>47.509998</td>
      <td>47.509998</td>
      <td>47.900002</td>
      <td>42100.0</td>
      <td>42.367157</td>
    </tr>
    <tr>
      <td>2011-01-04</td>
      <td>47.980000</td>
      <td>MGK</td>
      <td>47.380001</td>
      <td>47.980000</td>
      <td>47.639999</td>
      <td>53900.0</td>
      <td>42.137180</td>
    </tr>
    <tr>
      <td>2011-01-03</td>
      <td>48.020000</td>
      <td>MGK</td>
      <td>47.720001</td>
      <td>47.720001</td>
      <td>47.790001</td>
      <td>54400.0</td>
      <td>42.269867</td>
    </tr>
    <tr>
      <td>2010-12-31</td>
      <td>47.369999</td>
      <td>MGK</td>
      <td>47.209999</td>
      <td>47.349998</td>
      <td>47.310001</td>
      <td>41300.0</td>
      <td>41.845303</td>
    </tr>
  </tbody>
</table>
<p>2363 rows × 7 columns</p>
</div>


