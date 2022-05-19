---
layout: post
title: "Streamlit"
date: 2022-04-10
description: "Python学习笔记"
tag: Python
---

This is my note for Python for Streamlit. When I face some tricky problems, I'll record them.
Let's start!

关于第三方插件AgGrid：可以使用[GridOptionsBuilder]([GridOptionsBuilder — streamlit-aggrid 0.2.3 documentation](https://streamlit-aggrid.readthedocs.io/en/docs/GridOptionsBuilder.html))配置表格样式：

```python
from st_aggrid import AgGrid, DataReturnMode, GridUpdateMode, GridOptionsBuilder

options_builder = GridOptionsBuilder.from_dataframe(df)
options_builder.configure_default_column(groupable=True, value=True, enableRowGroup=True, aggFunc='sum', editable=True, wrapText=True, autoHeight=True)
options_builder.configure_column('col1',  pinned='left')
grid_options = options_builder.build()
AgGrid(df, grid_options, theme='blue')

# AgGrid相关参数：
# height: int =400,
# width=None,
# fit_columns_on_grid_load: bool=False,  将调整列以适应网格加载时的网格宽度，默认情况下为 False
# update_mode: GridUpdateMode= 'value_changed' ,  # 定义gird如何将结果发送回 streamlit,可以组合使用 ：GridUpdateMode = VALUE_CHANGED | SELECTION_CHANGED | FILTERING_CHANGED | SORTING_CHANGED
# data_return_mode: DataReturnMode= 'as_input' ,  # 定义如何从组件客户端检索数据。AS_INPUT/FILTERED/FILTERED_AND_SORTED
# allow_unsafe_jscode: bool=False,          # 允许在 gridOptions 中注入 jsCode
# enable_enterprise_modules: bool=False,   #Loads Ag-Grid enterprise modules (check licensing)
# license_key: str=None,            #见上
# try_to_convert_back_to_original_types: bool=True,    #尝试将从gid检索到的数据转换为原始类型
# conversion_errors: str='coerce',     #解析失败时的行为。raise：抛出异常，coerce：设置为NaN/NaT， ignore：返回input
# reload_data:bool=False,            #用了这个，就发现，编辑单元格就是虚的，立马给你返回原来的样子。
# theme:str='light',         #streamlit、light、dark、blue、fresh、material，请大家自行测试。

```