## Money Rates High Yield Savings

### Overview

The following script collects high-yielding saving accounts from Money Rates within the 50-100K range. It should be noted Money Rates changes the accounts included within the list regularly, so the sample will vary.

### Import Modules


```python
import pandas as pd
import bs4 as bs
import requests 
import numpy as np
```

### Extract HTML from website


```python
resp = requests.get('https://accounts.money-rates.com/savings-and-money-markets')
soup = bs.BeautifulSoup(resp.text, "lxml")
```

### Obtain bank CD list


```python
cd_rows = []
for row in soup.findAll('p', {'class': 'company_name'}):
    row2 = row.text.strip() # string removes blanks
    cd_rows.append(row2)
    
cd_rows
```




    ['Simple Bank', 'American Express National Bank', 'Marcus by Goldman Sachs']



### Data on APY Rates


```python
cd_rates_ls = []
for row in soup.findAll('div', {'class': 'rate_apy'}):
    cd_rates_ls.append(row.text.strip()[0:4])
    
cd_rates_ls
```




    ['1.40', '1.30', '1.30']



### Data on Rate Specifications

##### Data on APY Rates Type


```python
rate_type_ls = []
for row in soup.findAll('span', {'class': 'rate_type_value'}):
    rate_type_ls.append(row.text.strip())

rate_type_ls
```




    ['Checking', 'Savings', 'Savings']



##### Data on APY Monthly Fees


```python
rate_fee_ls = []
for row in soup.findAll('span', {'class': 'monthly_fee_value'}):
    rate_fee_ls.append(row.text.strip())
    
rate_fee_ls
```




    ['$0.00', '$0.00', '$0.00']



##### Data on APY Min To Open


```python
min_dep_ls = []
for row in soup.findAll('span', {'class': 'min_to_open_value'}):
    min_dep_ls.append(row.text.strip())

min_dep_ls
```




    ['$0', '$1', '$0']



##### Data on Rate Date


```python
rate_date = []
for row in soup.findAll('div', {'class': 'rates_date'}):
    rate_date.append(row.text.strip().split()[-1])
    
rate_date
```




    ['5/16/2020', '5/16/2020', '5/16/2020']



### Merge and Finalize DataFrame


```python
fullList = [cd_rates_ls] + [rate_type_ls] + [rate_fee_ls] + [min_dep_ls] + [rate_date]
fullList
```




    [['1.40', '1.30', '1.30'],
     ['Checking', 'Savings', 'Savings'],
     ['$0.00', '$0.00', '$0.00'],
     ['$0', '$1', '$0'],
     ['5/16/2020', '5/16/2020', '5/16/2020']]




```python
col_ls = {0:'Rate APY',
          1:'Type',
          2:'Monthly Fee',
          3:'Min to Earn APY',
          4:'Rate as Of'}

money_rates= pd.DataFrame(fullList).T.set_index([cd_rows]).rename(columns = col_ls)
money_rates.to_csv('money-rates.csv')

money_rates
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Rate APY</th>
      <th>Type</th>
      <th>Monthly Fee</th>
      <th>Min to Earn APY</th>
      <th>Rate as Of</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Simple Bank</td>
      <td>1.40</td>
      <td>Checking</td>
      <td>$0.00</td>
      <td>$0</td>
      <td>5/16/2020</td>
    </tr>
    <tr>
      <td>American Express National Bank</td>
      <td>1.30</td>
      <td>Savings</td>
      <td>$0.00</td>
      <td>$1</td>
      <td>5/16/2020</td>
    </tr>
    <tr>
      <td>Marcus by Goldman Sachs</td>
      <td>1.30</td>
      <td>Savings</td>
      <td>$0.00</td>
      <td>$0</td>
      <td>5/16/2020</td>
    </tr>
  </tbody>
</table>
</div>



SOURCE: https://accounts.money-rates.com/savings-and-money-markets
