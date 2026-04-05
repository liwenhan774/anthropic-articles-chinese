# Anthropic 经典文章中文翻译集

本目录包含 Anthropic 公司关于 Agent（智能体）、上下文工程（Context Engineering）和 Claude Code 相关主题的经典技术文章中文翻译。

---

## 文章列表

### 1. 构建高效的AI智能体 (Building Effective Agents)
- **原文链接**: https://www.anthropic.com/engineering/building-effective-agents
- **发布日期**: 2024年12月19日
- **核心内容**: 介绍构建LLM智能体的简单可组合模式，包括工作流模式（提示链、路由、并行化、协调器-工作者、评估器-优化器）和智能体系统的核心原则
- **文件**: `01-构建高效的AI智能体-Building-Effective-Agents.md`

### 2. 面向AI智能体的有效上下文工程 (Effective Context Engineering for AI Agents)
- **原文链接**: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- **发布日期**: 2025年9月29日
- **核心内容**: 探讨上下文工程策略，包括上下文衰减、即时上下文策略、渐进式披露、长周期任务的上下文管理（压缩、结构化笔记、子智能体架构）
- **文件**: `02-面向AI智能体的有效上下文工程-Effective-Context-Engineering.md`

### 3. Claude Code 最佳实践 (Claude Code: Best Practices for Agentic Coding)
- **原文链接**: https://www.anthropic.com/engineering/claude-code-best-practices
- **发布日期**: 2025年4月18日
- **核心内容**: Claude Code 的使用指南，包括核心概念、常见工作流、平台和集成、MCP连接、高级功能
- **文件**: `03-Claude-Code最佳实践-Claude-Code-Best-Practices.md`

### 4. "Think"工具：启用Claude在复杂工具使用情况下停下来思考 (The "think" tool)
- **原文链接**: https://www.anthropic.com/engineering/claude-think-tool
- **发布日期**: 2025年3月20日
- **核心内容**: 介绍"think"工具的概念、实现、在τ-Bench和SWE-Bench上的性能评估、使用场景和最佳实践
- **文件**: `04-Think工具-启用Claude在复杂工具使用情况下停下来思考-The-Think-Tool.md`

### 5. 我们如何构建多智能体研究系统 (How we built our multi-agent research system)
- **原文链接**: https://www.anthropic.com/engineering/multi-agent-research-system
- **发布日期**: 2025年6月13日
- **核心内容**: 多智能体系统的架构设计、提示工程原则、评估方法、生产可靠性挑战和解决方案
- **文件**: `05-我们如何构建多智能体研究系统-How-We-Built-Our-Multi-Agent-Research-System.md`

### 6. 使用智能体为智能体编写有效工具 (Writing effective tools for agents — with agents)
- **原文链接**: https://www.anthropic.com/engineering/writing-tools-for-agents
- **发布日期**: 2025年9月11日
- **核心内容**: 如何编写高质量工具、创建评估、与智能体协作优化工具、编写有效工具的原则
- **文件**: `06-使用智能体为智能体编写有效工具-Writing-Effective-Tools-for-Agents.md`

### 7. 揭秘AI智能体评估 (Demystifying evals for AI agents)
- **原文链接**: https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents
- **发布日期**: 2026年1月9日
- **核心内容**: 评估结构、评分器类型、不同智能体类型的评估方法、非确定性处理、从零构建评估的路线图
- **文件**: `07-揭秘AI智能体评估-Demystifying-Evals-for-AI-Agents.md`

### 8. 长运行智能体的有效工具 (Effective harnesses for long-running agents)
- **原文链接**: https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
- **发布日期**: 2025年11月26日
- **核心内容**: 长运行智能体的挑战、初始化智能体和编码智能体的双重解决方案、环境管理、增量进展、测试策略
- **文件**: `08-长运行智能体的有效工具-Effective-Harnesses-for-Long-Running-Agents.md`

### 9. MCP代码执行：构建更高效的智能体 (Code execution with MCP)
- **原文链接**: https://www.anthropic.com/engineering/code-execution-with-mcp
- **发布日期**: 2025年11月4日
- **核心内容**: 通过代码执行而非直接工具调用来提高MCP效率，包括渐进式披露、上下文高效处理、隐私保护和状态持久化
- **文件**: `09-MCP代码执行-构建更高效的智能体-Code-Execution-with-MCP.md`

### 10. Claude开发者平台上的高级工具使用 (Introducing advanced tool use)
- **原文链接**: https://www.anthropic.com/engineering/advanced-tool-use
- **发布日期**: 2025年11月24日
- **核心内容**: 三个新功能：工具搜索工具、程序化工具调用、工具使用示例，让Claude能够动态发现、学习和执行工具
- **文件**: `10-Claude开发者平台上的高级工具使用-Introducing-Advanced-Tool-Use.md`

### 11. Claude 3.5 Sonnet在SWE-bench Verified上创造新纪录 (Raising the bar on SWE-bench)
- **原文链接**: https://www.anthropic.com/engineering/swe-bench-sonnet
- **发布日期**: 2025年1月6日
- **核心内容**: Claude 3.5 Sonnet在SWE-bench Verified上达到49%的通过率，介绍智能体支架设计和工具优化
- **文件**: `11-Claude-3.5-Sonnet在SWE-bench-Verified上创造新纪录-Raising-the-Bar-on-SWE-bench.md`

### 12. 引入上下文检索 (Introducing Contextual Retrieval)
- **原文链接**: https://www.anthropic.com/engineering/contextual-retrieval
- **发布日期**: 2024年9月19日
- **核心内容**: 上下文检索技术，包括上下文嵌入和上下文BM25，可将检索失败率降低49%（结合重排序可达67%）
- **文件**: `12-引入上下文检索-Introducing-Contextual-Retrieval.md`

### 13. 超越权限提示：让Claude Code更安全更自主 (Beyond permission prompts)
- **原文链接**: https://www.anthropic.com/engineering/claude-code-sandboxing
- **发布日期**: 2025年10月20日
- **核心内容**: Claude Code的沙盒功能，包括文件系统隔离和网络隔离，减少84%的权限提示同时提高安全性
- **文件**: `13-超越权限提示-让Claude-Code更安全更自主-Beyond-Permission-Prompts.md`

### 14. 用并行Claude团队构建C编译器 (Building a C compiler with parallel Claudes)
- **原文链接**: https://www.anthropic.com/engineering/building-c-compiler
- **发布日期**: 2026年2月5日
- **核心内容**: 16个并行Claude智能体构建Rust C编译器的实验，生成100,000行代码，可编译Linux 6.9内核
- **文件**: `14-用并行Claude团队构建C编译器-Building-a-C-Compiler-with-Parallel-Claudes.md`

### 15. 长运行应用程序开发的工具设计 (Harness design for long-running apps)
- **原文链接**: https://www.anthropic.com/engineering/harness-design-long-running-apps
- **发布日期**: 2026年3月24日
- **核心内容**: 三智能体架构（规划器、生成器、评估器）用于长运行自主编码，前端设计和全栈开发的应用
- **文件**: `15-长运行应用程序开发的工具设计-Harness-Design-for-Long-Running-Apps.md`

---

## 主题分类

### Agent（智能体）架构与设计
- 01-构建高效的AI智能体
- 05-我们如何构建多智能体研究系统
- 08-长运行智能体的有效工具
- 14-用并行Claude团队构建C编译器
- 15-长运行应用程序开发的工具设计

### 上下文工程 (Context Engineering)
- 02-面向AI智能体的有效上下文工程
- 12-引入上下文检索

### Claude Code 相关
- 03-Claude Code 最佳实践
- 04-"Think"工具
- 13-超越权限提示

### 工具设计与评估
- 06-使用智能体为智能体编写有效工具
- 07-揭秘AI智能体评估
- 09-MCP代码执行
- 10-高级工具使用

### 性能评估与基准测试
- 11-Claude 3.5 Sonnet在SWE-bench Verified上创造新纪录

---

## 免责声明

本文档中的所有翻译仅供学习参考使用。原文版权归 Anthropic 公司所有。如需引用或商业使用，请参考原文并遵守相关版权规定。

---

*翻译整理日期: 2026年4月*
