---
layout: post
title: "Agent"
date: 2026-05-18
description: "Agent"
tag: 机器学习
katex: true  
---

# Agent

具备大脑（大模型）+四肢（工具：函数等）

# 运行模式

## ReAct（Reasoning and Acting）

thought - action - observation - final answer

![React](\assets\machine learning\ML-ai-agent\1.png)

ReAct时序图：

![React时序图](\assets\machine learning\ML-ai-agent\2.png)

## Plan-and-Execute

先规划再执行（Claude code、manus）

时序图：

![React时序图](\assets\machine learning\ML-ai-agent\2.png)

Plan模型和Re-plan模型可以是一个；

这种模式属于是内部在嵌套一个Agent（可能是ReAct执行方式）
