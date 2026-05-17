---
title: Model Serving Interview Outline
category: interview-outline
topic: model-serving
summary: Compact study guide for post-training trade-offs and local LLM deployment performance.
question_count: 2
chapter_count: 0
status: growing
created: 2026-05-08T17:05:00+08:00
updated: 2026-05-08T17:05:00+08:00
---

# Model Serving Interview Outline

## Study Guide

模型服务题可以沿两条主线回答：模型“怎么学得更好”（SFT vs RL），以及模型“怎么跑得更稳更快”（显存估算、推理优化、并行通信）。这两条线分别对应训练目标和系统落地，组合起来最像真实工程场景。

SFT 适合行为克隆和格式对齐，稳定、可控；RL 适合优化复杂目标和策略探索，上限高但训练和奖励设计更难。服务侧则要把硬件和系统约束说清：权重显存只是底线，KV Cache、上下文长度、batch、并发和框架 buffer 才是真实瓶颈；vLLM/PagedAttention、量化、批处理策略和并行通信决定吞吐与时延上限。

## Interview Thread

建议先讲 `[[interview/questions/sft-vs-rl-for-model-training]]`，说明训练方法差异和适用边界；再讲 `[[interview/questions/local-llm-deployment-gpu-vllm-cuda]]`，落到本地部署的显存与性能工程。结尾可以补一句：训练侧目标和服务侧约束必须联动，否则离线提升可能在线上吞吐和成本上失效。

## Questions

- [[interview/questions/sft-vs-rl-for-model-training]]
- [[interview/questions/local-llm-deployment-gpu-vllm-cuda]]

## Key Concepts

- SFT
- RLHF/RLAIF
- Reward Design
- GPU Memory Budget
- KV Cache
- Quantization
- vLLM / PagedAttention
- NCCL Collectives

## Open Gaps

- 还缺少 LoRA/QLoRA 细节、推理服务弹性扩容、观测与告警、故障回滚和多租户隔离的面经样本。
