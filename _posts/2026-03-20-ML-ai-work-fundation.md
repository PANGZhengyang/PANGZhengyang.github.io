---
layout: post
title: "大模型工作原理"
date: 2026-03-20
description: "AI思考"
tag: 机器学习
katex: true  
---

AI本质上是基于海量的数据训练，进行概率计算，推测一个最高概率的内容。

形象比喻：一个从来没有见过猫的人，在阅读了海量描述毛的文章和图片之后，能够很形象的复述毛的外形、习性。

AI“五层蛋糕”架构体系：能源-芯片（将电能转为算力）-基础设施（电力运输、冷却系统、建筑）-模型-应用



# 训练与推理

**训练**使模型“学会”某种能力，训练阶段，开发者会提供海量样本数据让模型反复学习。常见方式包括有监督学习(Supervised Learning)、自监督学习(Self-supervised Learning)和强化学习(Reinforcennent Learning)，目标是最小化预测错误(损失函数)，让模型参数收敛到良好泛化状态。

**微调**（Fine-tuning）是训练阶段的延伸，在预训练模型的基础上，基于某特定领域数据继续训练，使模型更适用于特定任务。

**推理**阶段则是模型训练完部署后，对新数据进行预测或生成。

# RLHF

人类反馈强化学习（RLHF，Reinforcement Learning from Human Feedback）：通过引入人类反馈信号，以强化学习方式进一步微调模型，使其回答更贴近人类。

# RAG

即使经过大规模训练和微调，模型仍可能编造事实，产生幻觉。检索增强生成（RAG，Retrieval-Augmented Generation）通过模型回答前，检索外部知识库，提升准确性。

具体步骤包括：

- 用户提问前：分片-索引（embedding）
- 用户提问后：召回（向量相似度：初筛）-重排（cross-encoder：精筛）-生成

# 注意力机制

注意力机制（Attention Mechanism）的核心是当前token决定自己应该关注哪些历史token。

该机制是Transformer、大模型的重要底层机制，能够让大模型：

- 理解长句子重要部分
- 理解长距离依赖
- 理解上下文



# Transformer

Transformer是大模型主流架构，包括Embedding、Encoder、Decoder等模块：

- Embedding：讲一段文本Tokenizer后，转换为向量，并添加位置，作为模型输入；
- Encoder：阅读理解，提取词间联系，上下文关系；
- Decoder：文本生成，帮助决定下一个token生成；

目前大部分大模型（GPT系列、千问）均为Decoder模型，他们在训练过程中会用到Encoder。

Transformer主要能够理解一段话中哪些信息更重要，以及并行处理海量文本。



# KV缓存

KV Cache=把历史token的Key/Value保存下来,以便解码马阶段复用,避免重复计算。















# Skills

让Agent具备可复用的做事方法论。通用agent的扩展包。

- 任务的执行 SOP 与必要背景知识（这件事大致怎么做）
- 工具的使用说明（用什么软件、怎么操作）
- 要用到的模板、素材（历史案例、格式规范）
- 可能遇到的问题、规范、解决方案（细节指引补充）

![Skills](\assets\machine learning\2026-04-20-ML-ai-thinking\1.png)

Skill 有两种加载模式：显式 / 隐式。
前者通过 user query 直接指定调用；后者根据任务与元信息描述的相关性，LLM 自动匹配。
