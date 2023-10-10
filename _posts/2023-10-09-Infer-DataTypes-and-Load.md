## Script for quickly loading flat files of a specified file Type into a SQL Server
This script maps the pandas data types to an appropriate SQL data type.It allows you to insert the contents of ***N*** flat fat files into a specified SQL Database. I used this script to create destination tables for CSV files with varying schemas.   


```python
'''SQL Automated Load: To create tables with inferred data types using pandas, and subsquently loading data from source files into respective DB tables'''

import pandas as pd
import os
import datetime
import re
import pyodbc

#Load files from directory filter for a given filetype
filePath="Your:\\Directory\\"
fileType=".csv"
fileList=os.listdir(filePath)
matched_fileList=[f for f in fileList if fileType in f]

#Load the first file into a dataframe. Index will be field names from CSV
df=pd.read_csv(filePath+matched_fileList[0])
df['FileName']=str(matched_fileList[0])
df['FileDate']=datetime.datetime.fromtimestamp(os.path.getmtime(filePath+matched_fileList[0]))
#Create secondary dataframe to set index as column name and the 
df_dtypes=pd.DataFrame(df.infer_objects().dtypes,columns=["Python_dataType"]).astype(str)

#Dictionary relating Pandas Data types to T-SQL data types 
py_to_sql_dtypes={'object':'NVARCHAR(MAX)','string':'NVARCHAR(MAX)','int64':'BIGINT','int32':'INT','int16':'SMALLINT','int8':'TINYINT','uint64':'BIGINT','uint32':'INT','uint16':'SMALLINT','uint8':'TINYINT','float64':'FLOAT(53)','float32':'REAL','bool':'BIT','datetime64[ns]':'DATETIME','timedelta[ns]':'TIME','categorical':'NVARCHAR(MAX)','object + datetime64':'NVARCHAR(MAX)','object + timedelta':'NVARCHAR(MAX)','period':'NVARCHAR(MAX)','interval':'NVARCHAR(MAX)','mixed':'NVARCHAR(MAX)','nullable integer':'INT','nullable boolean':'BIT','nullable datetime':'DATETIME','nullable timedelta':'TIME','nullable mixed':'NVARCHAR(MAX)','nullable category':'NVARCHAR(MAX)'}

#Add column pairing a given python dataType with an appropriate SQL data type
df_dtypes['Sql_dataType']=df_dtypes["Python_dataType"].map(py_to_sql_dtypes)

#Function used for formatting field names used for create table SQL statement. Last field in statement does not include a ',' char
def concat_field_desc(row, max_row):
    if row.name != max_row:
        return str(row.name)+' ' +row['Sql_dataType']+','
    else:
        return str(row.name)+' ' +row['Sql_dataType']

fileNumber=0
#Loop through matched file list, incrementally loading contents of flat file
for i in matched_fileList:
    #Set the max row to dataframe's last row
    max_row = df_dtypes.index[-1]
    #Apply the string formatting to the field names
    df_dtypes['concat']=df_dtypes.apply(lambda row: concat_field_desc(row,max_row), axis=1)
    #Convert values from formatted text to single string
    createFields=df_dtypes['concat'].to_string(header=False, index=False)

    #Prepare 'Create if' statement
    Create_table_query= "IF OBJECT_ID('[CFB_Challenge].[dbo].[{0}]') IS NULL CREATE TABLE [dbo].[{1}] ({2})".format(str(matched_fileList[fileNumber]).replace(fileType,''),str(matched_fileList[fileNumber]).replace(fileType,''),createFields.replace('      ',''))

    #Format field names in one line of text to use in insert statement
    plain_filedNames=str(re.findall('[a-z].*(?<=\s)',createFields,re.IGNORECASE)).replace("'",'').replace(',,',',')

    # Configure DB Connection
    connection_string =("Driver={<>};Server=<>;Database=<>;Trusted_Connection=<>;")
    connection_string_Alchemy = "mssql+pyodbc://<ServerName>/<DBName>?driver=<Your+Driver+Name>"

    #Open query, insert data 
    connection=pyodbc.connect(connection_string)
    cursor = connection.cursor()
    cursor.execute(Create_table_query)
    connection.commit()
    df.to_sql(str(matched_fileList[fileNumber]).replace(fileType,''),connection_string_Alchemy,if_exists='append',index=False)
    fileNumber+=1
```