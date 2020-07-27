## Bureau of Labor Statistics API - State Unemployment Rates (S. Ajd.)

The following script pulls seasonally adjusted unemployment rates from the BLS's API. The web scraping portion of the code is available on the BLS website. The current script pulls state and national rates from 2001 - 2020. 

State Codes: https://download.bls.gov/pub/time.series/sm/sm.state
<br>BLS API code: https://www.bls.gov/developers/api_python.htm
<br>BLS Series ID formats: https://www.bls.gov/help/hlpforma.htm#AP



```python
import pandas as pd
import requests
import json
import prettytable
import math
```

I editted the source file to include state abbriviations. The editted file is posted within the repository. The state unemployment rate series id's are calculated fields based on the state code within the source file.


```python
states = pd.read_csv('bls_state_codes.txt',dtype={'state_code':object})
states['state_umemployment_seriesid'] = 'LASST'+states['state_code']+'0000000000003' 
states.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>state_code</th>
      <th>state_name</th>
      <th>state_short</th>
      <th>state_umemployment_seriesid</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>01</td>
      <td>Alabama</td>
      <td>AL</td>
      <td>LASST010000000000003</td>
    </tr>
    <tr>
      <td>1</td>
      <td>02</td>
      <td>Alaska</td>
      <td>AK</td>
      <td>LASST020000000000003</td>
    </tr>
    <tr>
      <td>2</td>
      <td>04</td>
      <td>Arizona</td>
      <td>AS</td>
      <td>LASST040000000000003</td>
    </tr>
    <tr>
      <td>3</td>
      <td>05</td>
      <td>Arkansas</td>
      <td>AZ</td>
      <td>LASST050000000000003</td>
    </tr>
    <tr>
      <td>4</td>
      <td>06</td>
      <td>California</td>
      <td>AR</td>
      <td>LASST060000000000003</td>
    </tr>
  </tbody>
</table>
</div>



The state_umemployment_seriesid column is turned into a reference list in the API pull.


```python
seriesid_ls = states['state_umemployment_seriesid'].tolist()
seriesid_ls.append('LNS14000000')
seriesid_ls[0:5]
```




    ['LASST010000000000003',
     'LASST020000000000003',
     'LASST040000000000003',
     'LASST050000000000003',
     'LASST060000000000003']



This is a slightly modified version of the Python API made public by the BLS. It should be noted the API limits a year range of 10 years and 25 series for each request. Modifications include adding an output folder and specifying a start index. Even though a range of 10 years for 25 series each request is stated by the BLS, you can actually extract multiple 10 year ranges for each 25 series. Asside from defining a function, modifications to the original include:
1. Added API key
2. Iterations for 10 year intervals 
3. Indexed seriesid_ls reference in json.dumps function
4. Error processing for request limit
5. Output file name to include date


```python
def get_bls(folder,startyear,endyear,lower_index,api_key):
    key = f'?=registrationkey={api_key}'                    # modification 1
    iterations = math.ceil((endyear-startyear)/10)          # modification 2
    for i in range(1,iterations+1):
        start = startyear+(10*(i-1))
        end = startyear+(10*i)
        headers = {'Content-type': 'application/json'}
        data = json.dumps({"seriesid": seriesid_ls[lower_index:],"startyear": str(start)  # modification 3
                                                                ,"endyear": str(end)})         
        p = requests.post('https://api.bls.gov/publicAPI/v2/timeseries/data/'+key, data=data, headers=headers)
        json_data = json.loads(p.text)
        if json_data['message'] == 'Request could not be serviced, as the daily threshold for total number of requests allocated to the user has been reached.':
            print(json_data['message']) # modification 4
            pass
        else:
            for series in json_data['Results']['series']:
                x=prettytable.PrettyTable(["seriesid","year","period","value","footnotes"])
                seriesId = series['seriesID']
                for item in series['data']:
                    year = item['year']
                    period = item['period']
                    value = item['value']
                    footnotes=""
                    if 'M01' <= period <= 'M12':
                        x.add_row([seriesId,year,period,value,footnotes[0:-1]])
                    else:
                        pass
                output = open('data\\' + folder + '\\' + seriesId + str(start) + '-' + str(end)+'.txt','w') # modification 4
                output.write (x.get_string())
                output.close()  
                                    # enter API key here
get_bls('unemployment',2001,2020,0,'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX') 
```

### Processing the raw text files

This part of the script stitches together the individual text files output from the prior get_bls(function) and outputs a processed file for analysis.


```python
import glob
import pandas as pd
from pandas.tseries.offsets import MonthEnd
from datetime import date
```


```python
def process_bls(metric):
    dataframe  = []
    for infile in glob.glob("data/"+metric+"/*.txt"):
        data = pd.read_csv(infile,header = 1, sep = '|')
        data.columns = data.columns.str.replace(' ','')
        data = data[data['seriesid'].notnull()]
        data = data.loc[:, ~data.columns.str.contains('^Unnamed')]
        dataframe.append(data)
    
    dataframe = pd.concat(dataframe)
    # extract month from period string
    dataframe['month'] = dataframe['period'].str[3:5].astype(int)
    # create date from year and month. The MonthEnd function assigns the last day of the month to the date column
    dataframe['date'] = pd.to_datetime(dataframe[['year', 'month']].assign(DAY=1)) + MonthEnd(1)
    # drop unecessary columns
    dataframe = dataframe.drop(columns=['footnotes', 'period'])
    # set date to index and sort in descending order
    dataframe = dataframe.set_index('date').sort_index(ascending = False)
    # export to csv file
    dataframe.to_csv('data/'+metric+'/'+metric+'processed.csv')
        
    return dataframe

process_bls('unemployment')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>date</th>
      <th>seriesid</th>
      <th>year</th>
      <th>value</th>
      <th>month</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>2020-04-30</td>
      <td>LNS14000000</td>
      <td>2020.0</td>
      <td>14.7</td>
      <td>4</td>
    </tr>
    <tr>
      <td>2020-03-31</td>
      <td>LASST360000000000003</td>
      <td>2020.0</td>
      <td>4.5</td>
      <td>3</td>
    </tr>
    <tr>
      <td>2020-03-31</td>
      <td>LASST490000000000003</td>
      <td>2020.0</td>
      <td>3.6</td>
      <td>3</td>
    </tr>
    <tr>
      <td>2020-03-31</td>
      <td>LASST080000000000003</td>
      <td>2020.0</td>
      <td>4.5</td>
      <td>3</td>
    </tr>
    <tr>
      <td>2020-03-31</td>
      <td>LASST550000000000003</td>
      <td>2020.0</td>
      <td>3.4</td>
      <td>3</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>2001-01-31</td>
      <td>LASST380000000000003</td>
      <td>2001.0</td>
      <td>2.8</td>
      <td>1</td>
    </tr>
    <tr>
      <td>2001-01-31</td>
      <td>LASST200000000000003</td>
      <td>2001.0</td>
      <td>3.9</td>
      <td>1</td>
    </tr>
    <tr>
      <td>2001-01-31</td>
      <td>LASST090000000000003</td>
      <td>2001.0</td>
      <td>2.6</td>
      <td>1</td>
    </tr>
    <tr>
      <td>2001-01-31</td>
      <td>LASST500000000000003</td>
      <td>2001.0</td>
      <td>3</td>
      <td>1</td>
    </tr>
    <tr>
      <td>2001-01-31</td>
      <td>LASST290000000000003</td>
      <td>2001.0</td>
      <td>4.4</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>12244 rows × 4 columns</p>
</div>


