### Modules


```python
import bs4 as bs
import requests
import pandas as pd
import numpy as np
from itertools import islice
```

### Specify Ticker


```python
ticker = 'AAPL'
```

### Obtain and Parse hmtl


```python
resp = requests.get('https://finance.yahoo.com/quote/'+ticker+'/cash-flow?p='+ticker)
soup = bs.BeautifulSoup(resp.text, "html")
```

### Extract Column Headers 


```python
column_headers = []

for row in soup.findAll('div', {'class':'D(tbhg)'}):
    for row2 in row.findAll('span'):
        column_headers+=[row2.text]

column_headers
```




    ['Breakdown', 'ttm', '9/30/2019', '9/30/2018', '9/30/2017', '9/30/2016']



### Extract Rows


```python
rows = []

for row in soup.findAll('span'):
    row2 = row.text
    rows.append(row2) #

first = rows.index(column_headers[-1])+1 # uses last column header
last = rows.index('Learn more',first)    # first Learn more after last column header to specify rows
 
rows = rows[first:last]
```

### Create Table Body


```python
# itertools islice for transforming list to array

length_to_split = [len(column_headers)]
number_of_rows = int(len(rows) / len(column_headers))  
sliced_rows = iter(rows) 
sliced_rows = [list(islice(sliced_rows, elem)) 
              for elem in (length_to_split*number_of_rows)]
```

### Create and Output DataFrame


```python
df = pd.DataFrame(sliced_rows,
                  columns=column_headers,
                  index=[ticker]*int(len(sliced_rows)))

df.to_csv('cash_flows_'+ticker+'.csv')
```


```python
df
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Breakdown</th>
      <th>ttm</th>
      <th>9/30/2019</th>
      <th>9/30/2018</th>
      <th>9/30/2017</th>
      <th>9/30/2016</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>AAPL</td>
      <td>Operating Cash Flow</td>
      <td>75,373,000</td>
      <td>69,391,000</td>
      <td>77,434,000</td>
      <td>63,598,000</td>
      <td>65,824,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Investing Cash Flow</td>
      <td>22,049,000</td>
      <td>45,896,000</td>
      <td>16,066,000</td>
      <td>-46,446,000</td>
      <td>-45,977,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Financing Cash Flow</td>
      <td>-94,190,000</td>
      <td>-90,976,000</td>
      <td>-87,876,000</td>
      <td>-17,347,000</td>
      <td>-20,483,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>End Cash Position</td>
      <td>43,049,000</td>
      <td>50,224,000</td>
      <td>25,913,000</td>
      <td>20,289,000</td>
      <td>20,484,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Income Tax Paid Supplemental Data</td>
      <td>13,271,000</td>
      <td>15,263,000</td>
      <td>10,417,000</td>
      <td>11,591,000</td>
      <td>10,444,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Interest Paid Supplemental Data</td>
      <td>3,350,000</td>
      <td>3,423,000</td>
      <td>3,022,000</td>
      <td>2,092,000</td>
      <td>1,316,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Capital Expenditure</td>
      <td>-8,737,000</td>
      <td>-10,495,000</td>
      <td>-13,313,000</td>
      <td>-12,795,000</td>
      <td>-13,548,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Issuance of Capital Stock</td>
      <td>821,000</td>
      <td>781,000</td>
      <td>669,000</td>
      <td>555,000</td>
      <td>495,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Issuance of Debt</td>
      <td>13,247,000</td>
      <td>6,963,000</td>
      <td>6,969,000</td>
      <td>28,662,000</td>
      <td>24,954,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Repayment of Debt</td>
      <td>-11,555,000</td>
      <td>-8,805,000</td>
      <td>-6,500,000</td>
      <td>-3,500,000</td>
      <td>-2,500,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Repurchase of Capital Stock</td>
      <td>-73,679,000</td>
      <td>-66,897,000</td>
      <td>-72,738,000</td>
      <td>-32,900,000</td>
      <td>-29,722,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Free Cash Flow</td>
      <td>66,636,000</td>
      <td>58,896,000</td>
      <td>64,121,000</td>
      <td>50,803,000</td>
      <td>52,276,000</td>
    </tr>
  </tbody>
</table>
</div>



### Cash Flows Function


```python
def yahoo_cashflows(ticker,out):

    resp = requests.get('https://finance.yahoo.com/quote/'+ticker+'/cash-flow?p='+ticker)
    soup = bs.BeautifulSoup(resp.text, "html")

    column_headers = []
    for row in soup.findAll('div', {'class':'D(tbhg)'}):
        for row2 in row.findAll('span'):
            column_headers+=[row2.text]

    rows = []
    for row in soup.findAll('span'):
        row2 = row.text
        rows.append(row2) 
    first = rows.index(column_headers[-1])+1 
    last = rows.index('Learn more',first) 
    rows = rows[first:last]

    length_to_split = [len(column_headers)]
    number_of_rows = int(len(rows) / len(column_headers))
    sliced_rows = iter(rows) 
    sliced_rows = [list(islice(sliced_rows, elem)) 
                   for elem in (length_to_split*number_of_rows)]

    df = pd.DataFrame(sliced_rows,
                  columns=column_headers,
                  index=[ticker]*int(len(sliced_rows)))

    if out == 'y':
        df.to_csv('cash_flows_'+ticker+'.csv')
    else:
        pass

    return df

yahoo_cashflows('AAPL','y')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Breakdown</th>
      <th>ttm</th>
      <th>9/30/2019</th>
      <th>9/30/2018</th>
      <th>9/30/2017</th>
      <th>9/30/2016</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>AAPL</td>
      <td>Operating Cash Flow</td>
      <td>75,373,000</td>
      <td>69,391,000</td>
      <td>77,434,000</td>
      <td>63,598,000</td>
      <td>65,824,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Investing Cash Flow</td>
      <td>22,049,000</td>
      <td>45,896,000</td>
      <td>16,066,000</td>
      <td>-46,446,000</td>
      <td>-45,977,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Financing Cash Flow</td>
      <td>-94,190,000</td>
      <td>-90,976,000</td>
      <td>-87,876,000</td>
      <td>-17,347,000</td>
      <td>-20,483,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>End Cash Position</td>
      <td>43,049,000</td>
      <td>50,224,000</td>
      <td>25,913,000</td>
      <td>20,289,000</td>
      <td>20,484,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Income Tax Paid Supplemental Data</td>
      <td>13,271,000</td>
      <td>15,263,000</td>
      <td>10,417,000</td>
      <td>11,591,000</td>
      <td>10,444,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Interest Paid Supplemental Data</td>
      <td>3,350,000</td>
      <td>3,423,000</td>
      <td>3,022,000</td>
      <td>2,092,000</td>
      <td>1,316,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Capital Expenditure</td>
      <td>-8,737,000</td>
      <td>-10,495,000</td>
      <td>-13,313,000</td>
      <td>-12,795,000</td>
      <td>-13,548,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Issuance of Capital Stock</td>
      <td>821,000</td>
      <td>781,000</td>
      <td>669,000</td>
      <td>555,000</td>
      <td>495,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Issuance of Debt</td>
      <td>13,247,000</td>
      <td>6,963,000</td>
      <td>6,969,000</td>
      <td>28,662,000</td>
      <td>24,954,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Repayment of Debt</td>
      <td>-11,555,000</td>
      <td>-8,805,000</td>
      <td>-6,500,000</td>
      <td>-3,500,000</td>
      <td>-2,500,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Repurchase of Capital Stock</td>
      <td>-73,679,000</td>
      <td>-66,897,000</td>
      <td>-72,738,000</td>
      <td>-32,900,000</td>
      <td>-29,722,000</td>
    </tr>
    <tr>
      <td>AAPL</td>
      <td>Free Cash Flow</td>
      <td>66,636,000</td>
      <td>58,896,000</td>
      <td>64,121,000</td>
      <td>50,803,000</td>
      <td>52,276,000</td>
    </tr>
  </tbody>
</table>
</div>


