---
title: MCP 和 Agent 是如何实现的？
category: interview-question
topic: agent
subtopics: [mcp, agent, react, tool-schema, observability]
question_type: emerging
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/mcp-protocol]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260511-京东-ai后端实习.md
source_questions:
  - 怎么实现的 MCP 和 Agent？
summary: 考察 MCP 协议实现细节（Server/Client 架构、工具 schema 设计）和 ReAct Agent 工程实现（规划层/执行层/记忆层/可观测层）。
created: 2026-05-11T17:00:00+08:00
updated: 2026-05-11T17:00:00+08:00
---

# MCP 和 Agent 是如何实现的？

## Variants

- MCP 协议是怎么实现的？
- 你们的 Agent 是基于什么架构？
- ReAct 循环是怎么工程实现的？
- Agent 的工具调用失败怎么处理？

## Short Answer

MCP Server 端用 SDK 定义工具元数据（name、description、inputSchema），SDK 负责协议封装；Client 端通过 stdio/SSE 连接 Server，把工具列表注入 LLM tools 参数。Agent 基于 ReAct 模式（Thought→Action→Observation 循环），分为规划层、执行层、记忆层和可观测层；工具调用失败时重试两次，再把错误作为 Observation 返回模型决定下一步。

## Full Answer

### MCP 部分

MCP（Model Context Protocol）是 Anthropic 提出的标准化工具协议，定义了 LLM 和外部工具之间的通信格式。本质是解决"每个工具都要单独对接"的 N×M 问题——有了 MCP，N 个 Agent 对接 M 个工具只需要 N+M 个适配器。

**Server 端实现**：用 Python `mcp` SDK 定义工具：

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("data-service")

@mcp.tool()
def query_user(user_id: str) -> dict:
    """查询用户信息，适合用户 ID 已知的精准查询场景。\n不适合：模糊搜索、批量查询。
    """
    return db.query("SELECT * FROM users WHERE id = %s", user_id)
```

每个工具声明 name、description、inputSchema。SDK 把函数签名包装成符合 MCP 协议的 Server。工具的 description 写得准不准直接决定模型能不能准确选工具——description 需要写清楚"什么场景用"和"什么场景不用"。

**Client 端连接**：Agent 通过 stdio（本地进程）或 SSE（HTTP 长连接）连接 MCP Server，拿到工具列表后注入 LLM 的 tools 参数：

```python
# Agent 端伪代码
tools = mcp_client.list_tools()  # [{"name": "query_user", "description": "...", "parameters": {...}}]
response = llm.chat(messages, tools=tools)
```

### Agent 部分（ReAct 架构）

**整体架构分四层**：

- **规划层**：接收用户 query，做意图识别，决定走哪条 Agent 路径（客服、检索、数据分析等）
- **执行层**：ReAct 循环，每轮：LLM 调用 → 解析工具调用 → 执行工具 → 把结果作为 Observation 拼回上下文 → 循环直到模型判断完成
- **记忆层**：短期记忆用 session 内消息历史，长期记忆用向量数据库存用户偏好和历史任务结果
- **可观测层**：每个 span 打 trace_id，工具调用的入参、出参、延迟全量记录，接入 LangFuse 做链路追踪

**ReAct 循环工程实现**：

```python
def run_agent(user_query, max_turns=10):
    messages = [{"role": "user", "content": user_query}]

    for turn in range(max_turns):
        response = llm.chat(messages, tools=mcp_tools)

        if not response.tool_calls:
            return response.final_answer()  # 模型判断完成

        for call in response.tool_calls:
            result = execute_tool(call.name, call.arguments)
            # 重试逻辑：瞬时失败重试两次
            for retry in range(2):
                try:
                    result = execute_tool(call.name, call.arguments)
                    break
                except TransientError:
                    continue
            else:
                result = f"Tool failed after retries: {type(e).__name__}"

            messages.append({"role": "assistant", "content": None, "tool_calls": [call]})
            messages.append({"role": "observation", "content": str(result)})
```

**工具调用失败的处理策略**：不是简单重试到底。重试两次还是失败，就将错误信息作为 Observation 返回给模型，让模型自己决定是换工具、换参数、还是告知用户。关键原则是**不让工具静默失败变成错误答案**——任何一个工具的输出都要有交代，哪怕是"执行失败，请换策略"。

面试延伸点：工具调用失败率监控、工具选择准确率、每个工具的 P50/P99 延迟、trace 数据对模型 fine-tuning 的价值。

## Follow-ups

- MCP 和普通 HTTP API 封装成工具的区别？
- 工具 description 应该写多详细？
- ReAct 循环怎么避免无限循环？
- 长期记忆用什么向量数据库？索引策略是什么？

## Related Concepts

- MCP Protocol
- ReAct Pattern
- Tool Schema Design
- Agent Architecture
- LangFuse / Observability
- Short-term + Long-term Memory

## In Outlines

- [[interview/outlines/agent]]
