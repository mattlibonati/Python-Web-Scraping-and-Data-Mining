### Overview
The following script scrapes stock summary data from: https://finance.yahoo.com/quote/MGK?p=MGK.
Please note, the class names are subject to change over time, so they may need to be re-mapped were Yahoo to edit their HTML. 

### Modules


```python
import bs4 as bs              # Parse HTML/LXML
import requests               # Request html from internet
import pandas as pd           # Create dataframe
```

### Specify ticker/ URL, and parse request

The requests library is used to obtain the HTML from the target website(Yahoo Finance). Once the request has been processed, BeautifulSoup is used to parse the data. 


```python
symbol = 'MGK'
resp = requests.get(f'https://finance.yahoo.com/quote/{symbol}?p={symbol}&.tsrc=fin-srch')
soup = bs.BeautifulSoup(resp.text, "lxml")
```

### Obtain list of values

The values are found in the class "Ta(end) Fw(600) Lh(14px)". BeautifulSoup's findAll(fn) allows us to obtain data subject to the specified class. Once obtained, the text elements are stripped and appended to the list "row_values".


```python
row_values = []
for row in soup.findAll('td', {'class': 'Ta(end) Fw(600) Lh(14px)'}):
    row2 = row.text
    row_values.append(row2)
    
row_values
```




    ['157.74',
     '158.41',
     '159.04 x 800',
     '158.78 x 800',
     '157.69 - 159.20',
     '108.60 - 162.49',
     '384,899',
     '588,120',
     '6.6B',
     '157.63',
     'N/A',
     '1.00%',
     '8.44%',
     '1.00',
     '0.07%',
     '2007-12-17']



### Obtain list of value names

The previous process is completed again, this time referencing the row names which are identified by class "C($primaryColor) W(51%)".


```python
row_indexes = []
for row in soup.findAll('td', {'class': "C($primaryColor) W(51%)"}):
    row2 = row.text
    row_indexes.append(row2)
    
row_indexes
```




    ['Previous Close',
     'Open',
     'Bid',
     'Ask',
     "Day's Range",
     '52 Week Range',
     'Volume',
     'Avg. Volume',
     'Net Assets',
     'NAV',
     'PE Ratio (TTM)',
     'Yield',
     'YTD Daily Total Return',
     'Beta (5Y Monthly)',
     'Expense Ratio (net)',
     'Inception Date']



### Create dataframe

To organize the data, a dataframe is created from Pandas library.


```python
MGK = pd.DataFrame(row_values,
                   columns = ['values'],
                   index = row_indexes)
MGK['symbol'] = 'MGK'
MGK
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>values</th>
      <th>symbol</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Previous Close</td>
      <td>157.74</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Open</td>
      <td>158.41</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Bid</td>
      <td>159.04 x 800</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Ask</td>
      <td>158.78 x 800</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Day's Range</td>
      <td>157.69 - 159.20</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>52 Week Range</td>
      <td>108.60 - 162.49</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Volume</td>
      <td>384,899</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Avg. Volume</td>
      <td>588,120</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Net Assets</td>
      <td>6.6B</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>NAV</td>
      <td>157.63</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>PE Ratio (TTM)</td>
      <td>N/A</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Yield</td>
      <td>1.00%</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>YTD Daily Total Return</td>
      <td>8.44%</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Beta (5Y Monthly)</td>
      <td>1.00</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Expense Ratio (net)</td>
      <td>0.07%</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Inception Date</td>
      <td>2007-12-17</td>
      <td>MGK</td>
    </tr>
  </tbody>
</table>
</div>



### Functionalize Code

Now that the code is understood, here is a function which returns the same results for any ticker.


```python
def scrape_summary(symbol):
    resp = requests.get(f'https://finance.yahoo.com/quote/{symbol}?p={symbol}&.tsrc=fin-srch')
    soup = bs.BeautifulSoup(resp.text, "lxml")
    
    row_values = []
    for row in soup.findAll('td', {'class': 'Ta(end) Fw(600) Lh(14px)'}):
        row2 = row.text
        row_values.append(row2)
        
    row_indexes = []
    for row in soup.findAll('td', {'class': "C($primaryColor) W(51%)"}):
        row2 = row.text
        row_indexes.append(row2)
    
    df = pd.DataFrame(row_values,
                      columns = ['values'],
                      index = row_indexes)
    
    df['symbol'] = symbol
    
    return df

scrape_summary('MGK')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>values</th>
      <th>symbol</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Previous Close</td>
      <td>157.74</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Open</td>
      <td>158.41</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Bid</td>
      <td>159.04 x 800</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Ask</td>
      <td>158.78 x 800</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Day's Range</td>
      <td>157.69 - 159.20</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>52 Week Range</td>
      <td>108.60 - 162.49</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Volume</td>
      <td>384,899</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Avg. Volume</td>
      <td>588,120</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Net Assets</td>
      <td>6.6B</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>NAV</td>
      <td>157.63</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>PE Ratio (TTM)</td>
      <td>N/A</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Yield</td>
      <td>1.00%</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>YTD Daily Total Return</td>
      <td>8.44%</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Beta (5Y Monthly)</td>
      <td>1.00</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Expense Ratio (net)</td>
      <td>0.07%</td>
      <td>MGK</td>
    </tr>
    <tr>
      <td>Inception Date</td>
      <td>2007-12-17</td>
      <td>MGK</td>
    </tr>
  </tbody>
</table>
</div>


