---
title: MySQL 深度分页为什么慢？子查询优化的原理是什么？
category: interview-question
topic: database
subtopics: [mysql, deep-pagination, offset, covering-index, keyset-pagination]
question_type: traditional
answer_status: reviewed
priority: high
frequency: high
concepts:
  - "[[concepts/mysql-index]]"
sources:
  - /Users/atan/Documents/llm-wiki-sources/interviews/20260512-阿里-agent应用开发.md
source_questions:
  - 分页查询（Deep Pagination）在数据量大时为什么会变慢？
  - 子查询优化分页的原理是什么？在联合索引覆盖的情况下，子查询节省的是遍历、回表还是 CPU？
summary: 考察深度分页性能退化原理、子查询优化（延迟关联）节省回表的机制、以及游标分页的优势。
created: 2026-05-12T00:00:00+08:00
updated: 2026-05-12T00:00:00+08:00
---

# MySQL 深度分页为什么慢？子查询优化的原理是什么？

## Variants

- `LIMIT 100000, 10` 为什么慢？
- 子查询优化分页能解决什么问题？
- 游标分页和深度分页的区别？
- 联合索引覆盖的情况下子查询节省了什么？

## Short Answer

`LIMIT 100000, 10` 慢在必须扫描并丢弃前 100000 条数据——即使只返回 10 条，也要读取 100010 条。子查询优化（延迟关联）让子查询只取 id（走覆盖索引，不回表），外层再用这 10 个 id 做主键查询，只需 10 次回表。根本解法是游标分页（`WHERE id > last_id`），彻底消除大 offset。

## Full Answer

### 深度分页慢的原因

`LIMIT 100000, 10` 的执行过程：
1. 走索引扫描，定位到满足条件的行
2. **按顺序读取 100000 + 10 条记录**
3. **丢弃前 100000 条**，返回最后 10 条

这 100000 条如果需要回表（非覆盖索引），每条都要一次随机 IO 读聚簇索引——100000 次随机 IO 是极大的开销。即使有覆盖索引，扫描 100000 行的 CPU 和内存开销也不可忽视。

offset 越大，性能越差，是**线性退化**。offset 100 万比 10 万慢 10 倍，这是分页查询不能翻到很深页数的根本原因。

### 子查询优化原理（延迟关联）

```sql
-- 原始慢查询
SELECT * FROM orders LIMIT 100000, 10;

-- 子查询优化（延迟关联）
SELECT * FROM orders o
JOIN (
    SELECT id FROM orders LIMIT 100000, 10
) tmp ON o.id = tmp.id;
```

子查询 `SELECT id FROM orders LIMIT 100000, 10` 只查 `id` 字段。如果 `id` 是主键或者有覆盖索引，**这个查询完全在索引上完成，不需要回表**。拿到 10 个 id 后，外层用这 10 个 id 做主键查询，只有 10 次回表。

原始方案需要 100010 次可能的回表，子查询方案只需要 10 次回表——**节省的主要是回表开销**（随机 IO），不是遍历（遍历 100000 条的开销还在），也不主要是 CPU。

### 游标分页（最优解）

```sql
-- 记住上一页最后一条记录的 id
SELECT * FROM orders WHERE id > 100000 LIMIT 10;
```

这种方案彻底消除了大 offset 的扫描，每次都是 O(1) 定位（主键范围扫描）。但只支持顺序翻页，不能跳页。

适合场景：无限滚动、评论列表、消息列表（这些本身就适合顺序翻）。不适合：需要跳页的表格、搜索结果。

### 联合索引覆盖的情况

如果查询的所有列都在联合索引里（覆盖索引），子查询内层直接走索引不需要回表；但外层 `SELECT *` 还是要回表取全部列。所以**子查询节省的是外层的回表次数**，内层的索引扫描开销（无论是覆盖还是非覆盖）都仍然存在。

## Follow-ups

- 为什么游标分页不能跳页？
- 覆盖索引和主键索引的区别？
- 如果 id 不连续怎么用游标分页？

## Related Concepts

- Deep Pagination
- Covering Index
- Clustered Index
- Keyset Pagination
- Random IO vs Sequential IO
- Index Range Scan

## In Outlines

- [[interview/outlines/database]]
