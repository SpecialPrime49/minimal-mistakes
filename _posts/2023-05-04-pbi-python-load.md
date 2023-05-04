---
title:  "Efficient CSV Loading using Power BI & Python"
search: false
categories: 
  - Python
  - Power BI
last_modified_at: 2023-05-05 T08:06:00-05:00
image: "assets/images/Python_pbi_load.png"
---



```python
import os, re
import pandas as pd
import datetime as dt

#Specify CSV file location
filePath='<directory>'

#Include all files in List
fileList=os.listdir(filePath)

#Increment through each file in directory
fileNum=0

#Filter List for all files named LDT_[...].csv
for i in fileList:
    if "*" in fileList[fileNum]:
        filtered_fileList=[f for f in fileList]
    fileNum+=1

#Reset counting variable
fileNum=0

#Establish dataframe with windows file name for each dataframe
dataframes={}
for f in filtered_fileList:
    df_name=re.sub('\s\([0-9]\)\.csv','',filtered_fileList[fileNum])
    csv_file=filePath+filtered_fileList[fileNum]
    creationDT=dt.datetime.fromtimestamp(os.path.getctime(csv_file))
    dataframes[df_name]=pd.read_csv(csv_file)
    dataframes[df_name]['file_load_date']=creationDT
    fileNum+=1

for df_name, df in dataframes.items():
    globals()[df_name] = df
    print(df)
```