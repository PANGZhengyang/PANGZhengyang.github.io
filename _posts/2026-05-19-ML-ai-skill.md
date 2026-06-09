---
layout: post
title: "Skill"
date: 2026-05-19
description: "Agent"
tag: 机器学习
katex: true  
---

让Agent具备可复用的做事方法论。通用agent的扩展包。

- 任务的执行 SOP 与必要背景知识（这件事大致怎么做）
- 工具的使用说明（用什么软件、怎么操作）
- 要用到的模板、素材（历史案例、格式规范）
- 可能遇到的问题、规范、解决方案（细节指引补充）

![Skills](/assets\machine learning\ML-ai-skill\1.png)

# 加载模式

Skill 有两种加载模式：显式 / 隐式。

- 显示：通过 user query 直接指定调用；
- 隐式：根据任务与元信息描述的相关性，LLM 自动匹配。

# 核心运行机制——渐进式披露

Skill本质是上下文工程（上下文过长容易导致模型能力下降），所以Skill运作采取渐进式披露。

Skill.md包含Metadata和Body部分。

## 第一部分：Metadata

元数据（YMAL格式）始终加载，包含name + description

```
YMAL:标准化格式表示结构化数据，让人类容易理解 机器便于解析。
```

AI通过理解用户的消息与元数据进行匹配情况，判断是否使用该Skill。

## 第二部分：Body

正文部分（Markdown）触发加载，主要的指令，包括工作流程、最佳实践等。

当判断使用该Skill时，Agent会用bash去读取文档正文，将其加载到Context Window中。

## 第三部分：Resources

resources包括Reference参考文档、Scripts代码脚本、Assets可用资源。按需加载。

在特定场景才会加载，且Agent在其虚拟机中直接调用脚本，脚本代码本身不进Context Window，只有脚本运行完成后的输出才会进入Agent的Context。

附录没有内容大小限制。

