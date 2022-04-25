---
layout: post
title: "Python Base Study——Plotly"
date: 2020-06-12
description: "Python学习笔记"
tag: Python
---

This is my note for Python for Plotly. When I face some tricky problems, I'll record them.
Let's start!

想说一下高度封装好的Plotly Express

```python
import pandas as pd
import numpy as np
import plotly.express as px

qq = pd.DataFrame(
    {
        "Predicted": np.sort(np.random.uniform(3, 15, 4)),
        "real": np.sort(np.random.uniform(3, 15, 4)),
        "Category": ["A", "B", "C", "D"],
        "new_val": np.random.uniform(3, 15, 4),
    }
)

'''
px.bar(data,x,y,title,template,color_discrete_sequence)
1.color_discrete_sequence=plotly.colors.colorbrewer.Set1

.update_traces() 更新一下trace的格式
'''

fig = px.bar(qq, x="Category", y=["Predicted", "real", "new_val"], title="Long-Form Input",template="plotly_dark",color_discrete_sequence=['#A21C1C'])

# 在bar图里面再加入line图，同事yaxis='y2'和.update_layout添加second——axis
fig.add_traces( px.line(qq, x="Category", y="real",markers=True,color_discrete_sequence=['blue']).update_traces(showlegend=True, name="real", yaxis="y2").data)

fig.update_layout(yaxis2={"side":"right", "overlaying":"y"})
```