## FFIEC Call Report Data Analysis: Creating Bank Dictionary

### Requirements 
This script requires the program ffiec_call_report_analysis_process_zip_files.ipynb to be executed.
<br><br>
In order to properly identify each bank and their geography, we will create a reference table within this script. The files FFIEC CDR Call Bulk POR MMDDYYYY are sourced. 

### Modules


```python
import pandas as pd
import numpy as np
import glob
import plotly.graph_objects as go
```

### Reading and processing raw data

A for loop is used with glob to parse the text files retreived from the ffiec_call_report_analysis_process_zip_files.ipynb script and aggregating each quarters data.


```python
dataframe  = []
for infile in glob.glob("call_report_unzipped/*.txt"):
    
    if infile[-16:-13] == 'POR':                                      # parse the file name to determine if it's the file type required
        data = pd.read_csv(infile,sep='\t')                           # import data as tab delimited 
        data['date'] = pd.to_datetime(infile[-12:-4],format='%m%d%Y') # extract the date from the file name and create a column 
        dataframe.append(data)                                        # append file to dataframe
    else:
        pass
    
dataframe= pd.concat(dataframe, sort = True) # concatenate the data     
dataframe = dataframe[dataframe.IDRSSD.notnull()] # remove all (if any) records with null IDRSSDs
dataframe = dataframe.loc[:, ~dataframe.columns.str.contains('^Unnamed')] # remove any "unnamed" columns 
```

Once the base dataframe is parsed and filtered, we limit to just fields we may potentially need for future analysis. It should be noted some bank's names, addresses, etc are subject to change over time, so the full record count and date fields are retained to ensure a complete merge with analysis files. 


```python
bank_list = dataframe[['IDRSSD','Financial Institution Address','Financial Institution City'
                       ,'Financial Institution Filing Type','Financial Institution Name'
                       ,'Financial Institution State','Financial Institution Zip Code','date']]
bank_list
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>IDRSSD</th>
      <th>Financial Institution Address</th>
      <th>Financial Institution City</th>
      <th>Financial Institution Filing Type</th>
      <th>Financial Institution Name</th>
      <th>Financial Institution State</th>
      <th>Financial Institution Zip Code</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>37</td>
      <td>321 BROAD STREET</td>
      <td>SPARTA</td>
      <td>41</td>
      <td>BANK OF HANCOCK COUNTY</td>
      <td>GA</td>
      <td>31087</td>
      <td>2001-03-31</td>
    </tr>
    <tr>
      <td>1</td>
      <td>242</td>
      <td>FRONT STREET</td>
      <td>XENIA</td>
      <td>41</td>
      <td>FIRST NATIONAL BANK OF XENIA, THE</td>
      <td>IL</td>
      <td>62899</td>
      <td>2001-03-31</td>
    </tr>
    <tr>
      <td>2</td>
      <td>279</td>
      <td>215 W BROAD</td>
      <td>MINEOLA</td>
      <td>41</td>
      <td>MINEOLA COMMUNITY BANK, SSB</td>
      <td>TX</td>
      <td>75773</td>
      <td>2001-03-31</td>
    </tr>
    <tr>
      <td>3</td>
      <td>354</td>
      <td>MAIN AND WALNUT</td>
      <td>BISON</td>
      <td>41</td>
      <td>BISON STATE BANK</td>
      <td>KS</td>
      <td>67520</td>
      <td>2001-03-31</td>
    </tr>
    <tr>
      <td>4</td>
      <td>439</td>
      <td>MAIN STREET</td>
      <td>BLACKSHEAR</td>
      <td>41</td>
      <td>PEOPLES BANK</td>
      <td>GA</td>
      <td>31516</td>
      <td>2001-03-31</td>
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
      <td>5222</td>
      <td>5397639</td>
      <td>800 DELAWARE AVENUE</td>
      <td>WILMINGTON</td>
      <td>51</td>
      <td>ADP TRUST COMPANY NATIONAL ASSOCIATION</td>
      <td>DE</td>
      <td>19801</td>
      <td>2019-12-31</td>
    </tr>
    <tr>
      <td>5223</td>
      <td>5401273</td>
      <td>2356 MAIN STREET</td>
      <td>TUCKER</td>
      <td>51</td>
      <td>TANDEM BANK</td>
      <td>GA</td>
      <td>30084</td>
      <td>2019-12-31</td>
    </tr>
    <tr>
      <td>5224</td>
      <td>5412457</td>
      <td>350 SOUTH RAMPART BOULEVARD, SUITE 180</td>
      <td>LAS VEGAS</td>
      <td>51</td>
      <td>LEXICON BANK</td>
      <td>NV</td>
      <td>89145</td>
      <td>2019-12-31</td>
    </tr>
    <tr>
      <td>5225</td>
      <td>5427684</td>
      <td>11675 MEDLOCK BRIDGE RD</td>
      <td>JOHNS CREEK</td>
      <td>51</td>
      <td>LOYAL TRUST BANK</td>
      <td>GA</td>
      <td>30097</td>
      <td>2019-12-31</td>
    </tr>
    <tr>
      <td>5226</td>
      <td>5448915</td>
      <td>20 COTTON ROAD 1ST FLOOR</td>
      <td>NASHUA</td>
      <td>41</td>
      <td>MILLYARD BANK, THE</td>
      <td>NH</td>
      <td>3063</td>
      <td>2019-12-31</td>
    </tr>
  </tbody>
</table>
<p>539955 rows × 8 columns</p>
</div>



Here is a simple function that can be used to search the bank list for specific firms.


```python
def search_bank_list(element,value):
    bank = bank_list[bank_list[element] == value].sort_values(by=['date'], ascending=False)
    return bank

search_bank_list("IDRSSD",675332)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>IDRSSD</th>
      <th>Financial Institution Address</th>
      <th>Financial Institution City</th>
      <th>Financial Institution Filing Type</th>
      <th>Financial Institution Name</th>
      <th>Financial Institution State</th>
      <th>Financial Institution Zip Code</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>2818</td>
      <td>675332</td>
      <td>303 PEACHTREE STREET NE</td>
      <td>ATLANTA</td>
      <td>31</td>
      <td>SUNTRUST BANK</td>
      <td>GA</td>
      <td>30308</td>
      <td>2019-09-30</td>
    </tr>
    <tr>
      <td>2846</td>
      <td>675332</td>
      <td>303 PEACHTREE STREET NE</td>
      <td>ATLANTA</td>
      <td>31</td>
      <td>SUNTRUST BANK</td>
      <td>GA</td>
      <td>30308</td>
      <td>2019-06-30</td>
    </tr>
    <tr>
      <td>2876</td>
      <td>675332</td>
      <td>303 PEACHTREE STREET NE</td>
      <td>ATLANTA</td>
      <td>31</td>
      <td>SUNTRUST BANK</td>
      <td>GA</td>
      <td>30308</td>
      <td>2019-03-31</td>
    </tr>
    <tr>
      <td>2897</td>
      <td>675332</td>
      <td>303 PEACHTREE STREET NE</td>
      <td>ATLANTA</td>
      <td>31</td>
      <td>SUNTRUST BANK</td>
      <td>GA</td>
      <td>30308</td>
      <td>2018-12-31</td>
    </tr>
    <tr>
      <td>2932</td>
      <td>675332</td>
      <td>303 PEACHTREE STREET NE</td>
      <td>ATLANTA</td>
      <td>31</td>
      <td>SUNTRUST BANK</td>
      <td>GA</td>
      <td>30308</td>
      <td>2018-09-30</td>
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
      <td>4613</td>
      <td>675332</td>
      <td>25 PARK PLACE NE</td>
      <td>ATLANTA</td>
      <td>31</td>
      <td>SUNTRUST BANK</td>
      <td>GA</td>
      <td>30302</td>
      <td>2002-03-31</td>
    </tr>
    <tr>
      <td>4656</td>
      <td>675332</td>
      <td>25 PARK PLACE NE</td>
      <td>ATLANTA</td>
      <td>31</td>
      <td>SUNTRUST BANK</td>
      <td>GA</td>
      <td>30302</td>
      <td>2001-12-31</td>
    </tr>
    <tr>
      <td>4710</td>
      <td>675332</td>
      <td>25 PARK PLACE NE</td>
      <td>ATLANTA</td>
      <td>31</td>
      <td>SUNTRUST BANK</td>
      <td>GA</td>
      <td>30302</td>
      <td>2001-09-30</td>
    </tr>
    <tr>
      <td>4736</td>
      <td>675332</td>
      <td>25 PARK PLACE NE</td>
      <td>ATLANTA</td>
      <td>31</td>
      <td>SUNTRUST BANK</td>
      <td>GA</td>
      <td>30302</td>
      <td>2001-06-30</td>
    </tr>
    <tr>
      <td>4791</td>
      <td>675332</td>
      <td>25 PARK PLACE NE</td>
      <td>ATLANTA</td>
      <td>31</td>
      <td>SUNTRUST BANK</td>
      <td>GA</td>
      <td>30302</td>
      <td>2001-03-31</td>
    </tr>
  </tbody>
</table>
<p>74 rows × 8 columns</p>
</div>



An initial look at the data displays a state with the value of '01'. This represents BANK OF THE FEDERATED STATES OF MICRONESIA, a bank located on The Federated States of Micronesia, a small island to the east of the Philippines. This record will be removed from future analysis. 


```python
search_bank_list("Financial Institution State",'0 ')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>IDRSSD</th>
      <th>Financial Institution Address</th>
      <th>Financial Institution City</th>
      <th>Financial Institution Filing Type</th>
      <th>Financial Institution Name</th>
      <th>Financial Institution State</th>
      <th>Financial Institution Zip Code</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>2949</td>
      <td>718275</td>
      <td>98 KASELEHLIE STREET</td>
      <td>POHNPEI</td>
      <td>51</td>
      <td>BANK OF THE FEDERATED STATES OF MICRONESIA</td>
      <td>0</td>
      <td>96941</td>
      <td>2019-12-31</td>
    </tr>
    <tr>
      <td>2989</td>
      <td>718275</td>
      <td>98 KASELEHLIE STREET</td>
      <td>POHNPEI</td>
      <td>51</td>
      <td>BANK OF THE FEDERATED STATES OF MICRONESIA</td>
      <td>0</td>
      <td>96941</td>
      <td>2019-09-30</td>
    </tr>
    <tr>
      <td>3019</td>
      <td>718275</td>
      <td>98 KASELEHLIE STREET</td>
      <td>POHNPEI</td>
      <td>51</td>
      <td>BANK OF THE FEDERATED STATES OF MICRONESIA</td>
      <td>0</td>
      <td>96941</td>
      <td>2019-06-30</td>
    </tr>
    <tr>
      <td>3050</td>
      <td>718275</td>
      <td>98 KASELEHLIE STREET</td>
      <td>POHNPEI</td>
      <td>51</td>
      <td>BANK OF THE FEDERATED STATES OF MICRONESIA</td>
      <td>0</td>
      <td>96941</td>
      <td>2019-03-31</td>
    </tr>
    <tr>
      <td>3073</td>
      <td>718275</td>
      <td>98 KASELEHLIE STREET</td>
      <td>POHNPEI</td>
      <td>51</td>
      <td>BANK OF THE FEDERATED STATES OF MICRONESIA</td>
      <td>0</td>
      <td>96941</td>
      <td>2018-12-31</td>
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
      <td>4893</td>
      <td>718275</td>
      <td>100 WATERFRONT STREET</td>
      <td>POHNPEI</td>
      <td>41</td>
      <td>BANK OF THE FEDERATED STATES OF MICRONESIA</td>
      <td>0</td>
      <td>96941</td>
      <td>2002-03-31</td>
    </tr>
    <tr>
      <td>4943</td>
      <td>718275</td>
      <td>100 WATERFRONT STREET</td>
      <td>POHNPEI</td>
      <td>41</td>
      <td>BANK OF THE FEDERATED STATES OF MICRONESIA</td>
      <td>0</td>
      <td>96941</td>
      <td>2001-12-31</td>
    </tr>
    <tr>
      <td>5002</td>
      <td>718275</td>
      <td>100 WATERFRONT STREET</td>
      <td>POHNPEI</td>
      <td>41</td>
      <td>BANK OF THE FEDERATED STATES OF MICRONESIA</td>
      <td>0</td>
      <td>96941</td>
      <td>2001-09-30</td>
    </tr>
    <tr>
      <td>5032</td>
      <td>718275</td>
      <td>100 WATERFRONT STREET</td>
      <td>POHNPEI</td>
      <td>41</td>
      <td>BANK OF THE FEDERATED STATES OF MICRONESIA</td>
      <td>0</td>
      <td>96941</td>
      <td>2001-06-30</td>
    </tr>
    <tr>
      <td>5089</td>
      <td>718275</td>
      <td>100 WATERFRONT STREET</td>
      <td>POHNPEI</td>
      <td>41</td>
      <td>BANK OF THE FEDERATED STATES OF MICRONESIA</td>
      <td>0</td>
      <td>96941</td>
      <td>2001-03-31</td>
    </tr>
  </tbody>
</table>
<p>75 rows × 8 columns</p>
</div>



### Bank List EDA

Here is a function determine some very basic eda on the bank list data. Multiple fields can be specified in the groupby function creating a list in the input statement under element.


```python
def bank_list_eda(date,element):
    eda = bank_list[element+['date']][bank_list['date'] == date].groupby(element).count()
    return eda

eda = bank_list_eda('2019-12-31',['Financial Institution State','Financial Institution Filing Type'])
eda
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Financial Institution State</th>
      <th>Financial Institution Filing Type</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>51</td>
      <td>1</td>
    </tr>
    <tr>
      <td rowspan="2" valign="top">AK</td>
      <td>41</td>
      <td>2</td>
    </tr>
    <tr>
      <td>51</td>
      <td>3</td>
    </tr>
    <tr>
      <td rowspan="2" valign="top">AL</td>
      <td>31</td>
      <td>1</td>
    </tr>
    <tr>
      <td>41</td>
      <td>31</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>WI</td>
      <td>51</td>
      <td>146</td>
    </tr>
    <tr>
      <td rowspan="2" valign="top">WV</td>
      <td>41</td>
      <td>11</td>
    </tr>
    <tr>
      <td>51</td>
      <td>39</td>
    </tr>
    <tr>
      <td rowspan="2" valign="top">WY</td>
      <td>41</td>
      <td>3</td>
    </tr>
    <tr>
      <td>51</td>
      <td>27</td>
    </tr>
  </tbody>
</table>
<p>129 rows × 1 columns</p>
</div>



### Create csv

After executing the following block of code, rerunning the the bank_list_eda(fn) will produce results without BANK OF THE FEDERATED STATES OF MICRONESIA included. 


```python
# remove BANK OF THE FEDERATED STATES OF MICRONESIA
bank_list = bank_list[bank_list['Financial Institution State'] != '0 ']
# output file to csv
bank_list.to_csv('processed/bank_list.csv')
```

### Heatmap U.S. Financial Institution Headquarters by State

A really neat library for visualizing heatmaps by state is Plotly. The following script takes a look at financial institution headquarters by state as of 12/31/2019.


```python
bank_list = pd.read_csv('processed/bank_list.csv',usecols = ['date','Financial Institution State'])
bank_list = bank_list[bank_list['date'] == '2019-12-31'].groupby('Financial Institution State').count()
bank_list = bank_list.reset_index()
```


```python
fig = go.Figure(data=go.Choropleth(
    locations=bank_list['Financial Institution State'], # spatial coordinates
    z = bank_list['date'].astype(float),                # data to be color-coded
    locationmode = 'USA-states',                 # state abbreviations map to states
    colorscale = 'Reds',
    colorbar_title = "Millions USD",
))

fig.update_layout(
    title_text = 'Financial Institution Headquarters - By State',
    geo_scope='usa'                              # limit map scope to USA
)

fig.show()
```

![alt text](https://github.com/mattlibonati/Call-Report-Data-Analysis/blob/master/images/heatmap_banklist.PNG)