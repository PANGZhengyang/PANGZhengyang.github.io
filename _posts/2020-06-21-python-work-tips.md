---
layout: post
title: "Python工作中的小Tips"
date: 2020-06-21
description: "Python学习笔记"
tag: Python
mermaid: true
---

## 循环读取数据

在某个文件夹中有多个`Excel`，要求依次循环读取文件并形成一个`df`

```python
import os
import pandas as pd

path = '路径'
files_names = os.listdir(path) #读取该路径下的全部文件，并形成一个list
excel_names = [f for f in file_names if f.low().endwith('xlsx') ]
df_list = []
for excel_name in excel_names:
    df.list.append(pd.read_excel(path+excel_name))

df = pd.concat(d)
```







