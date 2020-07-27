# Overview
The following script is used to scrape option data from Yahoo Finance.

### Modules


```python
import bs4 as bs
import requests
import pandas as pd
import numpy as np
from itertools import islice
```

For the example, the ETF SPY will be analyzed.


```python
ticker = 'SPY'
```

Requests library is used to obtain the data from the web. Beautiful Soup is used to parse the data.


```python
resp = requests.get('https://finance.yahoo.com/quote/'+ticker+'/options?straddle=true')
soup = bs.BeautifulSoup(resp.text, "lxml")
```

Start by obtaining the row headers...


```python
column_headers = []

for row in soup.findAll('th'):
    row2 = row.text
    column_headers.append(row2)
```

And then the table body...


```python
option_rows = []

for row in soup.findAll('td'):
    row2 = row.text
    option_rows.append(row2)
```

Itertools islice is used to transform the prior list in an array with a row length equal to the number of column headers so that the data fits nicely into a dataframe.


```python
length_to_split = [len(column_headers)]

number_of_rows = int(len(option_rows) / 11)
    
sliced_rows = iter(option_rows) 

sliced_rows = [list(islice(sliced_rows, elem)) 
              for elem in (length_to_split*number_of_rows)]
```

The ticker is multiplied by the number of rows within the table so the ticker can be indexed within the dataframe. The column_headers list is used to label the column headings, while the sliced_rows array is used at the table body. It should be noted call data is on the left of the strike, whereas put data is on the right. 


```python
index = [ticker]*number_of_rows

options = pd.DataFrame(sliced_rows,
                       columns = column_headers,
                       index = index)

options.to_csv(ticker+'options.csv')
options
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Last Price</th>
      <th>Change</th>
      <th>% Change</th>
      <th>Volume</th>
      <th>Open Interest</th>
      <th>Strike</th>
      <th>Last Price</th>
      <th>Change</th>
      <th>% Change</th>
      <th>Volume</th>
      <th>Open Interest</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>SPY</td>
      <td>164.79</td>
      <td>-5.43</td>
      <td>-3.19%</td>
      <td>51</td>
      <td>105</td>
      <td>140.00</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>265</td>
      <td>7,249</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>145.00</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>200</td>
      <td>537</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>150.00</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>125</td>
      <td>608</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>155.00</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>2,190</td>
      <td>2,193</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>160.00</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>500</td>
      <td>631</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>0.02</td>
      <td>0.00</td>
      <td>-</td>
      <td>27</td>
      <td>184</td>
      <td>370.00</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>100</td>
      <td>256</td>
      <td>375.00</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>3</td>
      <td>835</td>
      <td>380.00</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>1</td>
      <td>6</td>
      <td>385.00</td>
      <td>71.17</td>
      <td>0.00</td>
      <td>-</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>2</td>
      <td>3</td>
      <td>390.00</td>
      <td>80.26</td>
      <td>0.00</td>
      <td>-</td>
      <td>1</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
<p>134 rows × 11 columns</p>
</div>



### The entire process within a function...


```python
def scrape_options(ticker):
    # obtain and parse data
    resp = requests.get('https://finance.yahoo.com/quote/'+ticker+'/options?straddle=true')
    soup = bs.BeautifulSoup(resp.text, "lxml")
    
    # obtain row headers 
    column_headers = []
    for row in soup.findAll('th'):
        row2 = row.text
        column_headers.append(row2)
    
    # obtain option data 
    option_rows = []
    for row in soup.findAll('td'):
        row2 = row.text
        option_rows.append(row2)
    
    # create 11 lengthed arrays 
    length_to_split = [len(column_headers)]
    number_of_rows = int(len(option_rows) / 11)
    sliced_rows = iter(option_rows) 
    sliced_rows = [list(islice(sliced_rows, elem)) 
                   for elem in (length_to_split*number_of_rows)]
    
    # create and process dataframe 
    index = [ticker]*number_of_rows
    options = pd.DataFrame(sliced_rows,
                           columns = column_headers,
                           index = index)
    
    # send to dataframe 
    options.to_csv(ticker+'options.csv')
    
    return options

scrape_options('SPY')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Last Price</th>
      <th>Change</th>
      <th>% Change</th>
      <th>Volume</th>
      <th>Open Interest</th>
      <th>Strike</th>
      <th>Last Price</th>
      <th>Change</th>
      <th>% Change</th>
      <th>Volume</th>
      <th>Open Interest</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>SPY</td>
      <td>164.79</td>
      <td>-5.43</td>
      <td>-3.19%</td>
      <td>51</td>
      <td>105</td>
      <td>140.00</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>265</td>
      <td>7,249</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>145.00</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>200</td>
      <td>537</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>150.00</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>125</td>
      <td>608</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>155.00</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>2,190</td>
      <td>2,193</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>160.00</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>500</td>
      <td>631</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>0.02</td>
      <td>0.00</td>
      <td>-</td>
      <td>27</td>
      <td>184</td>
      <td>370.00</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>100</td>
      <td>256</td>
      <td>375.00</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>3</td>
      <td>835</td>
      <td>380.00</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>1</td>
      <td>6</td>
      <td>385.00</td>
      <td>71.17</td>
      <td>0.00</td>
      <td>-</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <td>SPY</td>
      <td>0.01</td>
      <td>0.00</td>
      <td>-</td>
      <td>2</td>
      <td>3</td>
      <td>390.00</td>
      <td>80.26</td>
      <td>0.00</td>
      <td>-</td>
      <td>1</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
<p>134 rows × 11 columns</p>
</div>


