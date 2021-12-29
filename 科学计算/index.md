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

