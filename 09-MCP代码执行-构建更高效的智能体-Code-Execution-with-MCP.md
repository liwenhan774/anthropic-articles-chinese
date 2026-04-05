# MCP代码执行：构建更高效的智能体 (Code execution with MCP: Building more efficient agents)

> **原文链接**: https://www.anthropic.com/engineering/code-execution-with-mcp  
> **发布日期**: 2025年11月4日  
> **作者**: Adam Jones 和 Conor Kelly

---

## 摘要

直接工具调用会为每个定义和结果消耗上下文。智能体通过编写代码来调用工具而不是直接调用，可以更好地扩展。本文介绍如何使用MCP实现这一点。

Model Context Protocol (MCP) 是一个开放标准，用于将AI智能体连接到外部系统。传统上，将智能体连接到工具和数据需要为每个配对进行自定义集成，造成碎片化和重复工作，使扩展真正连接的系统变得困难。MCP提供了一个通用协议——开发者在他们的智能体中实现一次MCP，就能解锁整个集成生态系统。

自2024年11月推出MCP以来，采用速度迅速：社区已经构建了数千个MCP服务器，所有主要编程语言都有SDK可用，行业已将MCP采纳为将智能体连接到工具和数据的实际标准。

如今，开发者 routinely 构建可以访问数十个MCP服务器上的数百或数千个工具的智能体。然而，随着连接工具数量的增长，预先加载所有工具定义并将中间结果通过上下文窗口传递会降低智能体的速度并增加成本。

本文探讨代码执行如何使智能体更有效地与MCP服务器交互，在处理更多工具的同时使用更少的token。

---

## 工具过度消耗token使智能体效率降低

随着MCP使用规模的扩大，有两种常见模式会增加智能体的成本和延迟：

### 1. 工具定义使上下文窗口超载

大多数MCP客户端将所有工具定义预先加载到上下文中，使用直接工具调用语法将它们暴露给模型。这些工具定义可能如下所示：

```
gdrive.getDocument
     描述: 从Google Drive检索文档
     参数:
                documentId (必需, 字符串): 要检索的文档ID
                fields (可选, 字符串): 要返回的特定字段
     返回: 包含标题、正文内容、元数据、权限等的文档对象
```

工具描述占据更多上下文窗口空间，增加响应时间和成本。在智能体连接到数千个工具的情况下，它们需要在读取请求之前处理数十万个token。

### 2. 中间工具结果消耗额外token

大多数MCP客户端允许模型直接调用MCP工具。例如，你可能会要求你的智能体："从Google Drive下载我的会议记录并附加到Salesforce线索中。"

模型将进行如下调用：

```
工具调用: gdrive.getDocument(documentId: "abc123")
        → 返回 "讨论了Q4目标...\n[完整记录文本]"
           (加载到模型上下文中)

工具调用: salesforce.updateRecord(
			objectType: "SalesMeeting",
			recordId: "00Q5f000001abcXYZ",
  			data: { "Notes": "讨论了Q4目标...\n[完整记录文本再次写入]" }
		)
		(模型需要再次将整个记录写入上下文)
```

每个中间结果都必须通过模型。在这个例子中，完整的调用记录流经两次。对于2小时的销售会议，这可能意味着处理额外的50,000个token。

---

## 使用MCP的代码执行提高上下文效率

随着代码执行环境对智能体变得越来越普遍，一个解决方案是将MCP服务器呈现为代码API而不是直接工具调用。然后智能体可以编写代码与MCP服务器交互。这种方法解决了两个挑战：智能体可以只加载它们需要的工具，并在将结果传递回模型之前在执行环境中处理数据。

### 渐进式披露

模型擅长导航文件系统。将工具作为文件系统上的代码呈现，允许模型按需读取工具定义，而不是预先读取所有定义。

### 上下文高效的工具结果

在处理大型数据集时，智能体可以在返回结果之前过滤和转换结果。考虑获取一个10,000行的电子表格：

```javascript
// 不使用代码执行 - 所有行都流经上下文
工具调用: gdrive.getSheet(sheetId: 'abc123')
        → 在上下文中返回10,000行以手动过滤

// 使用代码执行 - 在执行环境中过滤
const allRows = await gdrive.getSheet({ sheetId: 'abc123' });
const pendingOrders = allRows.filter(row =>
  row["Status"] === 'pending'
);
console.log(`找到 ${pendingOrders.length} 个待处理订单`);
console.log(pendingOrders.slice(0, 5)); // 只记录前5个以供审查
```

智能体只看到5行而不是10,000行。

### 更强大和上下文高效的控制流

循环、条件语句和错误处理可以使用熟悉的代码模式完成，而不是链接单个工具调用。例如，如果你需要Slack中的部署通知，智能体可以编写：

```javascript
let found = false;
while (!found) {
  const messages = await slack.getChannelHistory({ channel: 'C123456' });
  found = messages.some(m => m.text.includes('部署完成'));
  if (!found) await new Promise(r => setTimeout(r, 5000));
}
console.log('收到部署通知');
```

### 隐私保护操作

当智能体使用MCP进行代码执行时，中间结果默认保留在执行环境中。这样，智能体只看到你明确记录或返回的内容。

### 状态持久化和技能

具有文件系统访问权限的代码执行允许智能体跨操作维护状态。智能体可以将中间结果写入文件，使它们能够恢复工作并跟踪进度：

```javascript
const leads = await salesforce.query({
  query: 'SELECT Id, Email FROM Lead LIMIT 1000'
});
const csvData = leads.map(l => `${l.Id},${l.Email}`).join('\n');
await fs.writeFile('./workspace/leads.csv', csvData);

// 稍后执行从上次中断的地方继续
const saved = await fs.readFile('./workspace/leads.csv', 'utf-8');
```

---

## 总结

MCP为智能体连接许多工具和系统提供了基础协议。然而，一旦连接了太多服务器，工具定义和结果可能会消耗过多的token，降低智能体效率。

尽管这里的许多问题感觉新颖——上下文管理、工具组合、状态持久化——但它们有来自软件工程的已知解决方案。代码执行将这些既定模式应用于智能体，让它们使用熟悉的编程结构更有效地与MCP服务器交互。

---

*原文版权归 Anthropic 所有。本文为中文翻译，仅供学习参考。*
