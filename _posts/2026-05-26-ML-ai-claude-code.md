---
layout: post
title: "CC的相关组件"
date: 2026-05-26
description: "Agent"
tag: 机器学习
katex: true  
---

# Memory

CC的记忆文件通常写在claude.md（包括用户级别和项目级别）

# MCP

CC连接外部系统

# Skill

Agent做事的方法论，其执行过程内容会占用Context（脚本、resource只有特定场景才会调用）

# Hooks

Hooks是用户自定义的 Shell 命令，强制执行的规范。会在CC生命周期的特定节点自动执行。

Hooks确保某些操作（如代码格式化、日志记录）必定触发，而非大模型自主选择执行。

## Hook事件类型说明

| 事件名称          | 触发时机                        | 核心作用                                                     |
| ----------------- | ------------------------------- | ------------------------------------------------------------ |
| PreToolUse        | 工具调用之前                    | 可拦截工具执行（如阻止修改敏感文件），并向 Claude 反馈调整建议 |
| PermissionRequest | 弹出权限请求对话框时            | 自动批准或拒绝权限申请                                       |
| PostToolUse       | 工具调用完成后                  | 执行后置操作（如格式化代码、记录日志）                       |
| UserPromptSubmit  | 用户提交提示词后、Claude 处理前 | 预处理用户输入（如补充上下文信息）                           |
| Notification      | Claude 发送通知时               | 自定义通知方式（如桌面弹窗、短信提醒）                       |
| Stop              | Claude 完成响应时               | 执行收尾工作（如清理临时文件）                               |
| SubagentStop      | 子代理任务完成时                | 处理子代理的执行结果                                         |
| PreCompact        | 即将执行上下文压缩操作时        | 自定义压缩规则                                               |
| SessionStart      | 启动新会话或恢复旧会话时        | 初始化会话环境（如加载项目配置）                             |
| SessionEnd        | 会话结束时                      | 保存会话数据、清理环境                                       |



# SubAgent

子Agent用于处理特定类型的任务，拥有独立的上下文窗口。

每个SubAgent都拥有：

- 独立的prompt；
- 独立的上下文；
- 指定的模型；
- 明确的工具访问权限；
- Hooks；

SubAgent只在主上下文窗口中返回结果，其内部执行结果不会占主上下文，这个也是上下文工程的解决方案（隔离上下文）。



# Plugin

插件（Plugin）是可复用的工具箱，里面包含：

- skills
- MCP
- hooks
- Subagent
- LSP服务（代码智能）



