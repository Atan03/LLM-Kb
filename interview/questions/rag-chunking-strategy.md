---
title: RAG 的 Chunking 策略有哪些？如何处理 Markdown 结构化文档？
category: interview-question
topic: rag
subtopics: [chunking, semantic-chunking, markdown, structured-documents, parent-index]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/rag-chunking]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260512-阿里-agent应用开发.md
source_questions:
  - 文档切片（Chunking）策略是什么？如何处理 Markdown 等结构化文档？
summary: 考察固定大小分块、语义分块的区别，以及 Markdown 结构化文档（标题层级/代码块/表格）的处理方法。
created: 2026-05-12T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# RAG 的 Chunking 策略有哪些？如何处理 Markdown 结构化文档？

## Variants

- Chunking 策略有哪些？
- Markdown 文档怎么切片？
- 语义分块和固定大小分块的区别？
- 代码块和表格怎么处理？

## Short Answer

固定大小分块按 token 数切，简单但会切断语义；语义分块通过 embedding 相似度检测边界，语义完整性更好。Markdown 文档应利用其天然结构——标题层级（H2/H3）、代码块（整体保留）、表格（整体保留）——而不是无视结构。

## Full Answer

### 通用 Chunking 策略

**固定大小分块（Fixed Size）**：
- 按 token 数切（比如 512 tokens），加 overlap（前后重叠 10-20%）来缓解边界问题
- 优点：实现简单，计算量小
- 缺点：会切断语义——一句话从中间切开，两个 chunk 都不完整
- 适用：没有明显结构的纯文本

**语义分块（Semantic Chunking）**：
- 把相邻句子的 embedding 做相似度计算，相似度骤降的地方就是语义边界，在边界处切割
- 优点：chunk 语义完整性好
- 缺点：计算量大，需要额外的 embedding 调用
- 适用：有语义断层的文本（如新闻文章、混合主题文档）

### Markdown 结构化文档的处理

Markdown 有天然结构，不要浪费它。

**标题层级切分**：
1. 用 `# ## ###` 标题层级切分，每个 H2 或 H3 小节作为一个 chunk 基本单元
2. 如果某个小节内容太长，再按语义二次切分
3. 保留标题信息在每个子 chunk 里（父子索引），检索时就知道这段内容属于哪个章节

**代码块整体保留**：代码语义是整体性的，拆开就废了。

**表格整体保留**：表格拆了行就没意义了，整体作为一个 chunk。

**补充 metadata**：每个 chunk 存 source（文件名）、section（所属标题路径，如 `安装指南 > 环境配置 > Linux`）、chunk_index。检索时 metadata 可以做过滤，生成阶段给模型提供溯源。

```json
{
  "content": "pip install xxx\n...",
  "metadata": {
    "source": "README.md",
    "section": "安装指南 > Linux",
    "chunk_index": 3
  }
}
```

## Follow-ups

- chunk 大小选多少合适？
- overlap 设置多少合理？
- 父子索引是什么原理？

## Related Concepts

- Fixed Size Chunking
- Semantic Chunking
- Parent Index
- Markdown Structure
- Chunk Metadata

## In Outlines

- [[interview/outlines/rag]]
