# Claude Code 最佳实践 (Claude Code: Best Practices for Agentic Coding)

> **原文链接**: https://www.anthropic.com/engineering/claude-code-best-practices  
> **发布日期**: 2025年4月18日

---

## 摘要

Claude Code 是一个智能体编码工具，可以读取你的代码库、编辑文件、运行命令，并与你的开发工具集成。它在终端、IDE、桌面应用和浏览器中可用。

本文介绍使用 Claude Code 的最佳实践，帮助你更有效地构建功能、修复错误和自动化开发任务。

---

## 什么是 Claude Code？

Claude Code 是一个AI驱动的编码助手，帮助你构建功能、修复错误和自动化开发任务。它理解你的整个代码库，可以跨多个文件和工具工作以完成工作。

---

## 核心概念

### 工作原理

Claude Code 通过以下方式工作：
- 读取和理解你的代码库
- 跨多个文件编辑代码
- 运行命令和测试
- 与你的开发工具集成

### 扩展 Claude Code

Claude Code 可以通过以下方式扩展：
- **MCP (Model Context Protocol)**: 连接外部数据源和工具
- **自定义命令**: 打包可重复的工作流
- **Hooks**: 在 Claude Code 操作前后运行shell命令

---

## 使用 Claude Code

### 存储指令和记忆

**CLAUDE.md** 是你添加到项目根目录的markdown文件，Claude Code 在每次会话开始时读取。用它来设置：
- 编码标准
- 架构决策
- 首选库
- 审查清单

Claude 还会在工作时构建**自动记忆**，保存构建命令和调试见解等学习成果，跨会话保留。

### 权限模式

Claude Code 提供不同的权限模式来控制自主性级别：
- 需要权限才能执行某些操作
- 可以配置自动批准某些操作

### 常见工作流

#### 自动化你一直在拖延的工作

Claude Code 处理消耗你一天时间的繁琐任务：
```bash
claude "为auth模块编写测试，运行它们，并修复任何失败"
```

#### 构建功能和修复错误

用 plain language 描述你想要的内容。Claude Code 规划方法，跨多个文件编写代码，并验证它是否有效。

对于错误，粘贴错误消息或描述症状。Claude Code 会追踪代码库中的问题，识别根本原因，并实施修复。

#### 创建提交和拉取请求

Claude Code 直接与git配合工作。它可以暂存更改、编写提交消息、创建分支和打开拉取请求。

```bash
claude "用描述性消息提交我的更改"
```

---

## 平台和集成

### 终端 CLI

功能完整的CLI，可直接在终端中使用 Claude Code：
```bash
cd your-project
claude
```

### VS Code

VS Code 扩展提供内联差异、@-提及、计划审查和对话历史记录。

### 桌面应用

独立的应用程序，用于在IDE或终端外运行 Claude Code。功能包括：
- 可视化审查差异
- 并排运行多个会话
- 安排定期任务
- 启动云端会话

### Web

在浏览器中运行 Claude Code，无需本地设置：
- 启动长时间运行的任务，完成后回来查看
- 处理你本地没有的仓库
- 并行运行多个任务

---

## 高级功能

### 运行智能体团队和构建自定义智能体

生成多个 Claude Code 智能体，同时处理任务的不同部分。主智能体协调工作、分配子任务并合并结果。

### 使用 CLI 进行管道、脚本和自动化

Claude Code 是可组合的，遵循Unix哲学：
```bash
# 分析最近的日志输出
tail -200 app.log | claude -p "如果发现任何异常，请Slack通知我"

# 在CI中自动化翻译
claude -p "将新字符串翻译成法语并提交PR以供审查"

# 跨文件批量操作
git diff main --name-only | claude -p "审查这些更改的文件是否存在安全问题"
```

### 安排定期任务

按计划运行 Claude 来自动化重复工作：
- 早晨PR审查
- 夜间CI故障分析
- 每周依赖审计
- PR合并后同步文档

### 随处工作

会话不绑定到单个界面。根据上下文变化在工作环境之间移动：
- 使用**Remote Control**从手机或任何浏览器继续工作
- 从手机向**Dispatch**发送任务
- 在web或iOS应用上启动长时间运行的任务，然后使用 `claude --teleport` 拉入终端
- 使用 `/desktop` 将终端会话交给桌面应用进行可视化差异审查

---

## 与工具连接 (MCP)

Model Context Protocol (MCP) 是一个开放标准，用于将AI工具连接到外部数据源。使用MCP，Claude Code 可以：
- 读取Google Drive中的设计文档
- 在Jira中更新工单
- 从Slack拉取数据
- 使用你自己的自定义工具

---

## 最佳实践总结

1. **使用 CLAUDE.md 提供项目上下文**：记录编码标准、架构决策和常用命令
2. **从清晰的描述开始**：用 plain language 描述你想要的内容
3. **迭代和审查**：审查Claude的更改，提供反馈以改进结果
4. **使用版本控制**：让Claude创建提交，以便你可以轻松回滚更改
5. **利用MCP扩展功能**：连接你的团队使用的工具和服务
6. **安排重复任务**：自动化常规维护工作
7. **尝试不同模式**：找到适合你工作流的权限级别

---

*原文版权归 Anthropic 所有。本文为中文翻译，仅供学习参考。*
