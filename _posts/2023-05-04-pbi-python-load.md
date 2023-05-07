---
title:  "Efficient CSV Loading using Power BI & Python"
search: false
categories: 
  - Python
  - Power BI
last_modified_at: 2023-05-05 T08:06:00-05:00
image: "assets/images/Python_pbi_load.png"
---

Microsoft’s Power BI has proven to be a really great tool for data visualization and dashboarding. Its web app service makes data highly accessible to end users. One of my favorite things about Power BI is its various options for data loading. Currently It supports 174 data ingest options. If you have a database table where records are regularly added, you may want to see what the table looked like at a certain date.  Suppose there is a set of output CSV files exported frequently from this DB table using a consistent query. Assuming the DB table has a names that does not change, your directory would fill with copies of the same file but can contain different datasets due to the date of their query was executed.
The following python script allows you to load all CSV files from a directory, associating each record within a CSV file with the date of the file’s creation. This allows you to visualize the changes to the database table over time.

```python
import os, re
import pandas as pd
import datetime as dt

#Specify CSV file location
filePath="<directory>"

#Include all files in List
fileList=os.listdir(filePath)

#Increment through each file in directory
fileNum=0

#Filter List for all csv files 
for i in fileList:
    if ".csv" in fileList[fileNum]:
        filtered_fileList=[f for f in fileList]
    fileNum+=1

#Reset counting variable
fileNum=0

#A data frame will be created for each CSV file. In the case that the files have the same name they will be appeneded
dataframes={}
creationDates_dict={}
for f in filtered_fileList:
    csv_file=filePath+filtered_fileList[fileNum]
    #Remove any characters like '(2) - Copy' in filename, to ensure files with similar filenames are concatenated
    df_name=re.sub('\\s\\([0-9].*\\).*(?=\\.)','',filtered_fileList[fileNum])
    creationDT=dt.datetime.fromtimestamp(os.path.getctime(csv_file))
    if df_name in dataframes:
        temp_df = pd.read_csv(csv_file)
        temp_df['file_creation_date'] = creationDT   
        dataframes[df_name] = pd.concat([dataframes[df_name], temp_df], ignore_index=True)
    else:
        dataframes[df_name] = pd.read_csv(csv_file)
        dataframes[df_name]['file_creation_date'] = creationDT  
      
    fileNum+=1
    
for df_name, df in dataframes.items():
    globals()[df_name] = df
    print(df)
```