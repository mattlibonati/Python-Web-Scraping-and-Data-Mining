# Overview
A typical barrier data miners face is limited request to servers. Many servers use an IP address to distinguish simultanious requests. A simple work can be done by iterating through a list of proxy IPs and ports along with your requests. 
<br><br>This script pulld a list of free public domain proxies from https://free-proxy-list.net as a reference list.

#### Modules


```python
import requests
from bs4 import BeautifulSoup
import bs4 as bs
import pandas as pd
import numpy as np
from itertools import islice
```

#### Request and Parse Data


```python
resp = requests.get('https://free-proxy-list.net/') # The website used here is free-proxy-list.net
soup = bs.BeautifulSoup(resp.text, 'lxml')          # The data is parsed in lxml format
```


```python
columns=[]
for row in soup.findAll('th'):
    columns_=row.text
    columns.append(columns_)
    
columns = columns[0:8]                              # Last column header within list
```


```python
# Optimize this
split_len = [len(columns)]                          # reference the row width as the length of columns
num_rows  = 300                                     # Number proxies available

body=[]
for row in soup.findAll('td'):
    body_=row.text
    body.append(body_)

print(len(body))                                    # observe length of list
    
body = iter(body)

body = [list(islice(body, elem))                    # Itertools islice Fn used to create an array for the body
        for elem in (split_len*num_rows)]
```

    2549
    


```python
proxies = pd.DataFrame(body,                        # Create dataframe
                       columns = columns)
proxies                                           
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>IP Address</th>
      <th>Port</th>
      <th>Code</th>
      <th>Country</th>
      <th>Anonymity</th>
      <th>Google</th>
      <th>Https</th>
      <th>Last Checked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>23.31.218.226</td>
      <td>80</td>
      <td>US</td>
      <td>United States</td>
      <td>anonymous</td>
      <td>no</td>
      <td>no</td>
      <td>2 seconds ago</td>
    </tr>
    <tr>
      <td>1</td>
      <td>202.150.143.59</td>
      <td>54397</td>
      <td>ID</td>
      <td>Indonesia</td>
      <td>elite proxy</td>
      <td>no</td>
      <td>yes</td>
      <td>2 seconds ago</td>
    </tr>
    <tr>
      <td>2</td>
      <td>203.202.245.58</td>
      <td>80</td>
      <td>BD</td>
      <td>Bangladesh</td>
      <td>anonymous</td>
      <td>no</td>
      <td>no</td>
      <td>2 seconds ago</td>
    </tr>
    <tr>
      <td>3</td>
      <td>103.83.116.3</td>
      <td>55443</td>
      <td>ID</td>
      <td>Indonesia</td>
      <td>elite proxy</td>
      <td>no</td>
      <td>yes</td>
      <td>2 seconds ago</td>
    </tr>
    <tr>
      <td>4</td>
      <td>147.91.111.133</td>
      <td>37979</td>
      <td>RS</td>
      <td>Serbia</td>
      <td>elite proxy</td>
      <td>no</td>
      <td>yes</td>
      <td>2 seconds ago</td>
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
    </tr>
    <tr>
      <td>295</td>
      <td>202.166.196.34</td>
      <td>61426</td>
      <td>NP</td>
      <td>Nepal</td>
      <td>elite proxy</td>
      <td>no</td>
      <td>yes</td>
      <td>22 minutes ago</td>
    </tr>
    <tr>
      <td>296</td>
      <td>3.226.57.156</td>
      <td>80</td>
      <td>US</td>
      <td>United States</td>
      <td>elite proxy</td>
      <td>no</td>
      <td>no</td>
      <td>22 minutes ago</td>
    </tr>
    <tr>
      <td>297</td>
      <td>103.227.147.142</td>
      <td>37581</td>
      <td>ID</td>
      <td>Indonesia</td>
      <td>elite proxy</td>
      <td>no</td>
      <td>no</td>
      <td>22 minutes ago</td>
    </tr>
    <tr>
      <td>298</td>
      <td>202.142.153.181</td>
      <td>38865</td>
      <td>PK</td>
      <td>Pakistan</td>
      <td>elite proxy</td>
      <td>no</td>
      <td>yes</td>
      <td>22 minutes ago</td>
    </tr>
    <tr>
      <td>299</td>
      <td>36.89.193.109</td>
      <td>31390</td>
      <td>ID</td>
      <td>Indonesia</td>
      <td>elite proxy</td>
      <td>no</td>
      <td>yes</td>
      <td>22 minutes ago</td>
    </tr>
  </tbody>
</table>
<p>300 rows × 8 columns</p>
</div>


