### Import Modules


```python
import pandas as pd
import bs4 as bs
import requests 
import numpy as np
```

### Extract HTML from websight


```python
resp = requests.get('https://www.wellsfargo.com/savings-cds/rates/')
soup = bs.BeautifulSoup(resp.text, "lxml")
```

### Term Length


```python
cd_term_standard = []
for row in soup.findAll('th', {'headers': 'cdratestdterm'}):
    cd_term_standard.append([row.text] * 6)
    
cd_term_standard = cd_term_standard[0] + cd_term_standard[1] + cd_term_standard[2]

cd_term_standard
```




    ['3 months',
     '3 months',
     '3 months',
     '3 months',
     '3 months',
     '3 months',
     '6 months',
     '6 months',
     '6 months',
     '6 months',
     '6 months',
     '6 months',
     '1 Year',
     '1 Year',
     '1 Year',
     '1 Year',
     '1 Year',
     '1 Year']



### Obtain Interest Rate


```python
cd_int_standard = []
for row in soup.findAll('td', {'headers': 'cdratestdintrate specterm'}):
    cd_int_standard.append(row.text)
    
cd_int_standard
```




    ['0.05%',
     '0.05%',
     '0.05%',
     '0.05%',
     '0.05%',
     '0.05%',
     '0.10%',
     '0.10%',
     '0.10%',
     '0.10%',
     '0.10%',
     '0.10%',
     '0.15%',
     '0.15%',
     '0.15%',
     '0.15%',
     '0.15%',
     '0.15%']



### Obtain APY


```python
cd_apy_standard = []
for row in soup.findAll('td', {'headers': 'cdratestdapy specterm'}):
    cd_apy_standard.append(row.text)
    
cd_apy_standard
```




    ['0.05%',
     '0.05%',
     '0.05%',
     '0.05%',
     '0.05%',
     '0.05%',
     '0.10%',
     '0.10%',
     '0.10%',
     '0.10%',
     '0.10%',
     '0.10%',
     '0.15%',
     '0.15%',
     '0.15%',
     '0.15%',
     '0.15%',
     '0.15%']



### Bonus Interest


```python
cd_b_int_standard = []
for row in soup.findAll('td', {'headers': 'cdratestdbintrate specterm'}):
    cd_b_int_standard.append(row.text)
    
cd_b_int_standard
```




    ['0.08%',
     '0.08%',
     '0.08%',
     '0.08%',
     '0.08%',
     '0.08%',
     '0.13%',
     '0.13%',
     '0.13%',
     '0.13%',
     '0.13%',
     '0.13%',
     '0.18%',
     '0.18%',
     '0.18%',
     '0.18%',
     '0.18%',
     '0.18%']



### Bonus APY


```python
cd_b_apy_standard = []
for row in soup.findAll('td', {'headers': 'cdratestdbbapy specterm'}):
    cd_b_apy_standard.append(row.text)
    
cd_b_apy_standard
```




    ['0.08%',
     '0.08%',
     '0.08%',
     '0.08%',
     '0.08%',
     '0.08%',
     '0.13%',
     '0.13%',
     '0.13%',
     '0.13%',
     '0.13%',
     '0.13%',
     '0.18%',
     '0.18%',
     '0.18%',
     '0.18%',
     '0.18%',
     '0.18%']



### balance tier


```python
cd_tier_standard = []
for row in soup.findAll('td', {'headers': 'cdratestdbbaltier specterm'}):
    row = row.text.strip()
    row = row.replace('\r','')
    row = row.replace('\n','')
    row = row.replace('\t','')
    row = row.replace(' ','')
    cd_tier_standard.append(row)
    
cd_tier_standard
```




    ['$0-$4999',
     '$5000-$9999',
     '$10000-$24999',
     '$25000-$49999',
     '$50000-$99999',
     '$100,000+',
     '$0-$4999',
     '$5000-$9999',
     '$10000-$24999',
     '$25000-$49999',
     '$50000-$99999',
     '$100,000+',
     '$0-$4999',
     '$5000-$9999',
     '$10000-$24999',
     '$25000-$49999',
     '$50000-$99999',
     '$100,000+']



### Create Final Dataset


```python
col_ls = {0:'Interest Rate',
          1:'APY',
          2:'Bonus Interest Rate',
          3:'Bonus APY',
          4:'Balance'}


base_ls = [cd_int_standard] + [cd_apy_standard] + [cd_b_int_standard] + [cd_b_apy_standard] + [cd_tier_standard]
dataFrame = pd.DataFrame(base_ls).T.set_index([cd_term_standard]).rename(columns = col_ls)

dataFrame.to_csv('WellsFargo.csv')

dataFrame
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Interest Rate</th>
      <th>APY</th>
      <th>Bonus Interest Rate</th>
      <th>Bonus APY</th>
      <th>Balance</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>3 months</td>
      <td>0.05%</td>
      <td>0.05%</td>
      <td>0.08%</td>
      <td>0.08%</td>
      <td>$0-$4999</td>
    </tr>
    <tr>
      <td>3 months</td>
      <td>0.05%</td>
      <td>0.05%</td>
      <td>0.08%</td>
      <td>0.08%</td>
      <td>$5000-$9999</td>
    </tr>
    <tr>
      <td>3 months</td>
      <td>0.05%</td>
      <td>0.05%</td>
      <td>0.08%</td>
      <td>0.08%</td>
      <td>$10000-$24999</td>
    </tr>
    <tr>
      <td>3 months</td>
      <td>0.05%</td>
      <td>0.05%</td>
      <td>0.08%</td>
      <td>0.08%</td>
      <td>$25000-$49999</td>
    </tr>
    <tr>
      <td>3 months</td>
      <td>0.05%</td>
      <td>0.05%</td>
      <td>0.08%</td>
      <td>0.08%</td>
      <td>$50000-$99999</td>
    </tr>
    <tr>
      <td>3 months</td>
      <td>0.05%</td>
      <td>0.05%</td>
      <td>0.08%</td>
      <td>0.08%</td>
      <td>$100,000+</td>
    </tr>
    <tr>
      <td>6 months</td>
      <td>0.10%</td>
      <td>0.10%</td>
      <td>0.13%</td>
      <td>0.13%</td>
      <td>$0-$4999</td>
    </tr>
    <tr>
      <td>6 months</td>
      <td>0.10%</td>
      <td>0.10%</td>
      <td>0.13%</td>
      <td>0.13%</td>
      <td>$5000-$9999</td>
    </tr>
    <tr>
      <td>6 months</td>
      <td>0.10%</td>
      <td>0.10%</td>
      <td>0.13%</td>
      <td>0.13%</td>
      <td>$10000-$24999</td>
    </tr>
    <tr>
      <td>6 months</td>
      <td>0.10%</td>
      <td>0.10%</td>
      <td>0.13%</td>
      <td>0.13%</td>
      <td>$25000-$49999</td>
    </tr>
    <tr>
      <td>6 months</td>
      <td>0.10%</td>
      <td>0.10%</td>
      <td>0.13%</td>
      <td>0.13%</td>
      <td>$50000-$99999</td>
    </tr>
    <tr>
      <td>6 months</td>
      <td>0.10%</td>
      <td>0.10%</td>
      <td>0.13%</td>
      <td>0.13%</td>
      <td>$100,000+</td>
    </tr>
    <tr>
      <td>1 Year</td>
      <td>0.15%</td>
      <td>0.15%</td>
      <td>0.18%</td>
      <td>0.18%</td>
      <td>$0-$4999</td>
    </tr>
    <tr>
      <td>1 Year</td>
      <td>0.15%</td>
      <td>0.15%</td>
      <td>0.18%</td>
      <td>0.18%</td>
      <td>$5000-$9999</td>
    </tr>
    <tr>
      <td>1 Year</td>
      <td>0.15%</td>
      <td>0.15%</td>
      <td>0.18%</td>
      <td>0.18%</td>
      <td>$10000-$24999</td>
    </tr>
    <tr>
      <td>1 Year</td>
      <td>0.15%</td>
      <td>0.15%</td>
      <td>0.18%</td>
      <td>0.18%</td>
      <td>$25000-$49999</td>
    </tr>
    <tr>
      <td>1 Year</td>
      <td>0.15%</td>
      <td>0.15%</td>
      <td>0.18%</td>
      <td>0.18%</td>
      <td>$50000-$99999</td>
    </tr>
    <tr>
      <td>1 Year</td>
      <td>0.15%</td>
      <td>0.15%</td>
      <td>0.18%</td>
      <td>0.18%</td>
      <td>$100,000+</td>
    </tr>
  </tbody>
</table>
</div>


