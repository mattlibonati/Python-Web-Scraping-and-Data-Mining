# Nerd Wallet CD Rates

### Overview

One type of investment is a certificate of deposit (CD). Due to the volatilty of interest rates, banks are constantly adjusting their CD yields in-line with the federal funds rate to maintain a competative advantage. Nerd Wallet is one of many great resources for this type of information. The following code obtains data from Nerd Wallet for a few top competitors's 1, 3, and 5 year CD yields.

### Import Modules


```python
import pandas as pd
import bs4 as bs
import requests 
import numpy as np
from itertools import islice
```

### Extract HTML from website

The get.(function) from Requests is used to extract the html data from the internet. Beautiful Soup is used to parse the html.


```python
resp = requests.get('https://www.nerdwallet.com/best/banking/cd-rates')
soup = bs.BeautifulSoup(resp.text, "lxml")
```

### Obtain bank CD list

Start by extracting the list of banks included within Nerd Wallet's analysis. Beautiful Soup is used to loop through each paragraph of the html (indicated by the 'p') for the peer name class.


```python
cd_rows = []

# _1_sEy _2kbwt is the peer name class
for row in soup.findAll('p', {'class': '_1_sEy _2kbwt'}):
    row2 = row.text
    cd_rows.append(row2)

cd_rows = [e for e in cd_rows if e not in ('3-year APY','5-year APY','Why we like it')]

cd_rows
```




    ['Marcus by Goldman Sachs CD',
     'Popular Direct CD',
     'Barclays CD',
     'Alliant Credit Union CD',
     'TIAA Bank CD',
     'CIT Bank CD',
     'Citizens Access CD',
     'Synchrony Bank CD',
     'Ally Bank CD',
     'Capital One CD',
     'Discover Bank CD',
     'BMO Harris Bank National Association CD']



### Obtain list of column heads 

The column headings for the bank are obtained in a similar fashion as the prior.


```python
cd_ls_cols = []

cd_table = soup.find('table', {'class': 'B6cJf _3aHLX'})

for row in cd_table.findAll('col'):
    row2 = row["class"][0]
    cd_ls_cols.append(row2)
    
cd_ls_cols = [e for e in cd_ls_cols if e not in ('bank','learnMore')]
  
cd_ls_cols
```




    ['apy1Year', 'apy3Year', 'apy5Year', 'minimumDeposit']



### Data on rates and minimum deposits 

Rates are obtained in a similar fashion as the prior. A list of cd rate data is created from the loop. Itertools iter(fn) and islice(fn) are used transform the list into an array with rows of four(determined from the prior step).


```python
cd_data = []
length_to_split = [4]

for row in soup.findAll('p', {'class': '_3VQ15'}):
    row2 = row.text
    cd_data.append(row2)

# creates new row ever 4 records    
cd_data = iter(cd_data) 

cd_data = [list(islice(cd_data, elem)) 
          for elem in (length_to_split*len(cd_rows))]

cd_data
```




    [['1.60%', '1.60%', '1.65%', '$500'],
     ['0.95%', '1.05%', '1.65%', '$10,000'],
     ['1.40%', '1.40%', '1.40%', '$0'],
     ['1.40%', '1.45%', '1.50%', '$1,000'],
     ['1.45%', '1.55%', '1.65%', '$5,000'],
     ['1.45%', '1.30%', '1.70%', '$1,000'],
     ['1.50%', '1.55%', '1.65%', '$5,000'],
     ['1.50%', '1.55%', '1.65%', '$2,000'],
     ['1.50%', '1.55%', '1.60%', '$0'],
     ['1.50%', '1.40%', '1.40%', '$1'],
     ['1.50%', '1.55%', '1.60%', '$2,500'],
     ['1.65%', '1.35%', '1.50%', '$5,000']]



### Create data frame and send to csv

The last step is to put the index(peers), columns(CD classes), and body(rates) together with Pandas DataFrame(fn) and export the dataframe to a cvs file with the to_csv(fn).


```python
nerdWallet = pd.DataFrame(cd_data,
                          columns = cd_ls_cols,
                          index = cd_rows)
nerdWallet.to_csv('nerdWallet Rates.csv')
nerdWallet
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>apy1Year</th>
      <th>apy3Year</th>
      <th>apy5Year</th>
      <th>minimumDeposit</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Marcus by Goldman Sachs CD</td>
      <td>1.60%</td>
      <td>1.60%</td>
      <td>1.65%</td>
      <td>$500</td>
    </tr>
    <tr>
      <td>Popular Direct CD</td>
      <td>0.95%</td>
      <td>1.05%</td>
      <td>1.65%</td>
      <td>$10,000</td>
    </tr>
    <tr>
      <td>Barclays CD</td>
      <td>1.40%</td>
      <td>1.40%</td>
      <td>1.40%</td>
      <td>$0</td>
    </tr>
    <tr>
      <td>Alliant Credit Union CD</td>
      <td>1.40%</td>
      <td>1.45%</td>
      <td>1.50%</td>
      <td>$1,000</td>
    </tr>
    <tr>
      <td>TIAA Bank CD</td>
      <td>1.45%</td>
      <td>1.55%</td>
      <td>1.65%</td>
      <td>$5,000</td>
    </tr>
    <tr>
      <td>CIT Bank CD</td>
      <td>1.45%</td>
      <td>1.30%</td>
      <td>1.70%</td>
      <td>$1,000</td>
    </tr>
    <tr>
      <td>Citizens Access CD</td>
      <td>1.50%</td>
      <td>1.55%</td>
      <td>1.65%</td>
      <td>$5,000</td>
    </tr>
    <tr>
      <td>Synchrony Bank CD</td>
      <td>1.50%</td>
      <td>1.55%</td>
      <td>1.65%</td>
      <td>$2,000</td>
    </tr>
    <tr>
      <td>Ally Bank CD</td>
      <td>1.50%</td>
      <td>1.55%</td>
      <td>1.60%</td>
      <td>$0</td>
    </tr>
    <tr>
      <td>Capital One CD</td>
      <td>1.50%</td>
      <td>1.40%</td>
      <td>1.40%</td>
      <td>$1</td>
    </tr>
    <tr>
      <td>Discover Bank CD</td>
      <td>1.50%</td>
      <td>1.55%</td>
      <td>1.60%</td>
      <td>$2,500</td>
    </tr>
    <tr>
      <td>BMO Harris Bank National Association CD</td>
      <td>1.65%</td>
      <td>1.35%</td>
      <td>1.50%</td>
      <td>$5,000</td>
    </tr>
  </tbody>
</table>
</div>



# Source

Data for this script was sourced from Nerd Wallet('https://www.nerdwallet.com/best/banking/cd-rates')
