---
layout: post
title: "Context Engineering"
date: 2026-05-21
description: "Agent"
tag: 机器学习
katex: true  
---

Context是模型输入；

Context Window是模型输入容量上限；

Context engineering 是精心设计给模型的输入内容，不改变模型结构，只改变模型能够“看到”什么。

# 解决的问题

1. Context Window有容量上限；新输入的内容会覆盖旧内容（失忆）
2. 上下文过长会影响模型输入；
3. 输入越多，成本越高；

# 实现方法

Langchain设计框架：

## 保存Context

把Context做筛选；总结在内存、硬盘进行储存

解决信息持久化。

## 选择Context

静态选择：所有内容全部放入Context中，如Claude.md，Cursor rules。

动态选择：选择与用户问题最相关的内容放入Context中，如openai的挑选记忆、RAG。



## 压缩Context

压缩的目标是在不丢失关键信息的前提下减少占用的token。常用方法：

- 摘要总结（Summarization）：定期将对话内容进行提炼总结成简短段落。比如每进行10轮对话,就让模型或规则脚本将之前的对话要点压缩成一段摘要。摘要应尽量客观准确地记录关键信息和结论。后续对话中,可将摘要作为上下文的一部分提供给模型。这样模型能"记住"之前的讨论重点,而细节省略。这种方法需要注意持续更新摘要,或维护多层摘要(如逐段汇总)。

- 向量记忆库（Embedding Memmory）：将每轮对话内容通过Embedding 映射为向量，存入向量数据库。

- 滑动窗口截断：保留最近N轮对话作为上下文提供，较早的直接丢弃。因此鲳鱼摘要总结结合，即“最近对话与就对话摘要”一并提供，以兼顾近期细节和长期背景。

  再具体实现时，往往多种策略配合。

## 隔离Context

不同模块的Context是隔离的互不干扰，通常发生在multi-Agent
