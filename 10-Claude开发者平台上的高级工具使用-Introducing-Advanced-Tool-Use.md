# Claude开发者平台上的高级工具使用 (Introducing advanced tool use on the Claude Developer Platform)

> **原文链接**: https://www.anthropic.com/engineering/advanced-tool-use  
> **发布日期**: 2025年11月24日

---

## 摘要

我们添加了三个新的测试版功能，让Claude能够动态发现、学习和执行工具。以下是它们的工作原理。

AI智能体的未来是模型在数百或数千个工具之间无缝工作的未来。一个IDE助手，集成git操作、文件操作、包管理器、测试框架和部署管道。一个运营协调员，同时连接Slack、GitHub、Google Drive、Jira、公司数据库和数十个MCP服务器。

为了构建有效的智能体，它们需要能够在不预先将所有定义塞入上下文的情况下处理无限的工具库。智能体还需要能够从代码中调用工具。代码是编排逻辑的自然选择，如循环、条件语句和数据转换。

今天，我们发布了三个使这成为可能的功能：

1. **工具搜索工具** - 允许Claude使用搜索工具访问数千个工具而不消耗其上下文窗口
2. **程序化工具调用** - 允许Claude在代码执行环境中调用工具，减少对模型上下文窗口的影响
3. **工具使用示例** - 提供演示如何有效使用给定工具的通用标准

---

## 工具搜索工具

### 挑战

MCP工具定义提供重要的上下文，但随着连接的服务器增多，这些token会累积。考虑一个五服务器设置：

- GitHub: 35个工具 (~26K token)
- Slack: 11个工具 (~21K token)
- Sentry: 5个工具 (~3K token)
- Grafana: 5个工具 (~3K token)
- Splunk: 2个工具 (~2K token)

这是58个工具，在对话开始之前就消耗大约55K token。添加更多服务器（如Jira，仅一个就使用~17K token），你很快就会接近100K+的token开销。

### 我们的解决方案

工具搜索工具让Claude按需发现工具，而不是预先加载所有工具定义。Claude只 sees 它实际需要的工具。

**传统方法**：
- 所有工具定义预先加载 (~72K token用于50+个MCP工具)
- 对话历史和系统提示竞争剩余空间
- 总上下文消耗：在任何工作开始之前~77K token

**使用工具搜索工具**：
- 只有工具搜索工具预先加载 (~500 token)
- 按需发现工具 (3-5个相关工具，~3K token)
- 总上下文消耗：~8.7K token，保留95%的上下文窗口

这代表了token使用量减少85%，同时保持对完整工具库的访问。

---

## 程序化工具调用

### 挑战

传统工具调用随着工作流变得更复杂会产生两个根本问题：

1. **来自中间结果的上下文污染**：当Claude分析10MB日志文件查找错误模式时，整个文件进入其上下文窗口，即使Claude只需要错误频率的摘要。
2. **推理开销和手动综合**：每个工具调用需要完整的模型推理。在收到结果后，Claude必须通过自然语言处理"审视"数据来提取相关信息。

### 我们的解决方案

程序化工具调用使Claude能够通过代码而不是单个API往返来编排工具。Claude编写代码调用多个工具，处理它们的输出，并控制什么信息实际进入其上下文窗口。

**示例：预算合规检查**

考虑一个常见的业务任务："哪些团队成员超过了他们的Q3差旅预算？"

**传统方法**：
- 获取团队成员 → 20人
- 为每个人获取Q3费用 → 20个工具调用，每个返回50-100行项目
- 获取员工级别的预算限制
- 所有这些进入Claude的上下文：2,000+行项目 (50 KB+)

**使用程序化工具调用**：

Claude编写Python脚本来编排整个工作流：

```python
team = await get_team_members("engineering")

# 为每个唯一级别获取预算
levels = list(set(m["level"] for m in team))
budget_results = await asyncio.gather(*[
    get_budget_by_level(level) for level in levels
])

# 创建查找字典
budgets = {level: budget for level, budget in zip(levels, budget_results)}

# 并行获取所有费用
expenses = await asyncio.gather(*[
    get_expenses(m["id"], "Q3") for m in team
])

# 找出超过差旅预算的员工
exceeded = []
for member, exp in zip(team, expenses):
    budget = budgets[member["level"]]
    total = sum(e["amount"] for e in exp)
    if total > budget["travel_limit"]:
        exceeded.append({
            "name": member["name"],
            "spent": total,
            "limit": budget["travel_limit"]
        })

print(json.dumps(exceeded))
```

Claude的上下文只接收最终结果：两到三个超过预算的人。2,000+行项目、中间总和和预算查找不会影响Claude的上下文。

---

## 工具使用示例

### 挑战

JSON Schema擅长定义结构——类型、必填字段、允许的枚举——但它无法表达使用模式：何时包含可选参数、哪些组合有意义，或你的API期望什么约定。

### 我们的解决方案

工具使用示例让你在工具定义中直接提供示例工具调用。Claude从示例中学习：

- **格式约定**：日期使用YYYY-MM-DD，用户ID遵循USR-XXXXX格式
- **嵌套结构模式**：如何构建reporter对象及其嵌套的contact对象
- **可选参数相关性**：关键bug有完整的联系信息+升级，功能请求有reporter但没有联系/升级

在我们的内部测试中，工具使用示例将复杂参数处理的准确性从72%提高到90%。

---

## 最佳实践

### 分层策略性地使用功能

并非每个智能体都需要在给定任务中使用所有三个功能。从你最大的瓶颈开始：

- 来自工具定义的上下文膨胀 → 工具搜索工具
- 污染上下文的大型中间结果 → 程序化工具调用
- 参数错误和格式错误的调用 → 工具使用示例

### 设置工具搜索工具以更好地发现

工具搜索根据名称和描述匹配，因此清晰、描述性的定义提高发现准确性。

### 设置程序化工具调用以正确执行

由于Claude编写代码来解析工具输出，清晰地记录返回格式。这有助于Claude编写正确的解析逻辑。

### 设置工具使用示例以提高参数准确性

为行为清晰制作示例：
- 使用真实数据（真实城市名称、合理价格）
- 用最少、部分和完整规范模式展示多样性
- 保持简洁：每个工具1-5个示例
- 关注歧义（只在正确用法从schema看不明显时添加示例）

---

*原文版权归 Anthropic 所有。本文为中文翻译，仅供学习参考。*
