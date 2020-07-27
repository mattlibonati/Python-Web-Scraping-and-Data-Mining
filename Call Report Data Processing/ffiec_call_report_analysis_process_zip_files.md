## FFIEC Call Report Data Analysis: Processing Zip Files

The first step to analyzing call report data is obtaining the raw data from: https://cdr.ffiec.gov/public//PWS/DownloadBulkData.aspx
<br><br> Available Product: Call Reports -- Single Period
<br>File Format: Tab Delimited
<br><br> The data is available by quarter back to 03/31/2001. The data is downloaded in zipped format, containing about 35 text files per period pertinent to different sections within the call report (referred to as "schedules"). Once the desired periods have been pulled, run the following code to extact the text files from each zipped folder.

Required modules for the program include glob and zipfiles36.


```python
import glob
import zipfile36
```

glob is used to determine all zip files with data requiring extraction. The glob identified files are then passed through zipfile36 functions to extract the data and output them in the desired folder. 
<br><br> Notice two folders are used within the program:
<br> call_report_zip_files containing the zipped files
<br> call_report_unzipped for extracted files to be output


```python
# unzip all call report files
for infile in glob.glob("call_report_zip_files/*.zip"):
    handle = zipfile36.ZipFile(infile)
    handle.extractall("call_report_unzipped/")
```
