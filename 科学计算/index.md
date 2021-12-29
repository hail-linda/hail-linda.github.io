# 科学计算

## pandas excel文件读写

写：

```python
excelWriter = pd.ExcelWriter('filename.xlsx')
pd.DataFrame(df).to_excel(excelWriter)
excelWriter.save()
```

写：

```python
pd.read_excel(io=r'文件名.xlsx', engine='openpyxl',sheet_name = 'sheet名',header=None)
```

openpyxl能读写xlsx ，default是xlrd，仅支持xls

## 断点续传：使用磁盘存储中间结果

### sqlite + pandas

```python
from sqlalchemy import create_engine
import pandas as pd
import sqlite3

# before func
engine= create_engine('sqlite:///local.db')
con = sqlite3.connect("local.db")
cur = con.cursor()
keyPointDf = pd.read_sql("select * from tableName",engine)

# after func
# drop if exist
cur.execute("DROP TABLE IF EXISTS `tableName`")
DF = pd.DataFrame(srcPythonVar)
DF.to_sql('tableName', engine)
```

### json 文件

写：

```python
with open('./vaccinesDescription.json', 'wb') as f:
    json.dump(json.dumps(vaccinesDescriptionList), f)
```

读：

```python
with open('./vaccinesDescription.json') as file_obj:
    vaccinesDescriptionList = json.loads(json.load(file_obj))
```

### csv 文件



读写在一块，断点续传：

```python
try:
    detailListDF = pd.read_csv('detailList.csv', encoding='utf8',index_col=0) # 读缓存文件
    existMovieUrl = detailListDF['url'].to_list() # 把已经爬完的url拿出来 用于后续去重
    print(len(existMovieUrl))
except:
    detailListDF = pd.DataFrame() # 如果try里面的代码报错了（缓存文件不存在），就建一个新的DF

    
detailListDF = pd.concat([detailListDF,pd.DataFrame(detailList)])#  把旧的缓存文件和这一批爬到的内容拼在一起
detailListDF.to_csv('detailList.csv', encoding='utf8') # 存缓存文件
detailListDF = detailListDF.drop_duplicates(subset='title', keep='first', inplace=False).reset_index()
```

subset是为了防止csv自己加index