---
layout: post
title: "Python工作中的小Tips"
date: 2020-06-21
description: "Python学习笔记"
tag: Python
mermaid: true
---

## 循环读取数据

1. 多个Excel合并到一个Excel，且每个Excel只有一个Sheet

```python
import os
import pandas as pd

path = '路径'
files_names = os.listdir(path) #读取该路径下的全部文件，并形成一个list
excel_names = [f for f in file_names if f.lower().endwith('xlsx') ]
df_list = []
for excel_name in excel_names:
    df_list.append(pd.read_excel(path+'/'+excel_name))

df = pd.concat(df_list, ignore_index=True)
df.to_excel('路径')
```

2. 一个Excel有多个Sheet，合并到一个Sheet里面

```python
import os
import pandas as pd
import xlwings as xw

file_name = '文件路径'
app=xw.App(visible=False,add_book=False)
wb = app.books.open(path)

df_list = []
for i in range(1,len(wb.sheets)+1):
    df_list.append(pd.read_excel(path,sheet_name=f'Sheet{i}'))
df = pd.concat(df_list,ignore_index=True)

app.quit()
df.to_excel('路径')
```

3. 多个Excel有多个Sheet，合并到一个Excel里面

```python
import os
import pandas as pd
import xlwings as xw

path = '路径'
file_names = os.listdir(path)
excel_names = [path + '/'+ f for f in file_names if f.lower().endswith('xlsx') ]
excel_names
app=xw.App(visible=True,add_book=False)
df_list = []
for file in excel_names:
    wb = app.books.open(file)
    for i in range(1,len(wb.sheets)+1):
        df_list.append(pd.read_excel(file,sheet_name=f'Sheet{i}'))
    wb.close()
df = pd.concat(df_list,ignore_index=True)
app.quit()
df.to_excel('路径')
```

## PDF

### 抽取PDF里面的表格

```python
import tabula

df = tabula.read_pdf('xxx.pdf',pages=1) # 表示第一页 'all'表示所有
```

### 合并PDF

```python
'''
1导入PyPDF2合并模块PdfFileMerger;
2读取需要处理和合并的PDF文档；
3从第一个PDF文档中取出需要合并的前3页；
4把第二个PDF文档的第一页插入到文档中；
5把第三个PDF文档附到输出文档末尾；
'''
from PyPDF2 import PdfFileMerger #// 1

merger = PdfFileMerger()

input1 = open("document1.pdf", "rb") #// 2
input2 = open("document2.pdf", "rb")
input3 = open("document3.pdf", "rb")

merger.append(fileobj = input1, pages = (0,3))# // 3

merger.merge(position = 2, fileobj = input2, pages = (0,1))# // 4

merger.append(input3)# // 5

output = open("document-output.pdf", "wb")
merger.write(output)

```

### 拆分PDF

```python
from PyPDF2 import PdfFileWriter, PdfFileReader

def pdf_split(pdf_in,pdf_out,start,end):
    # 初始化一个pdf
    output = PdfFileWriter()
    # 读取pdf
    with open(pdf_in, 'rb') as in_pdf:
        pdf_file = PdfFileReader(in_pdf)
        # 从pdf中取出指定页
        for i in range(start, end):
            output.addPage(pdf_file.getPage(i))
        # 写出pdf
        with open(pdf_out, 'ab') as out_pdf:
            output.write(out_pdf)

if __name__ == '__main__':
    pdf_in = 'BS218_10.pdf'
    pdf_out = 'BS218_10_副本2.pdf'
    s, e = 4, 6  # 拆分的起始位置和结束位置 0表示第一页
    pdf_split(pdf_in, pdf_out, s, e) # 插入代码片
```





