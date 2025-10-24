---
title:  LLM 蒸馏 & Transformer
date: 2025-9-18 20:23:09
categories:
  - ai笔记
tags:
  - 人工智能
  - 机器学习

---

## LLM 蒸馏

### 什么是 LLM 蒸馏技术?

> LLM 蒸馏 (Distillation) 是一种模型压缩技术，**用于将大型语言模型 (LLM) 的知识转移到较小的模型中**。其主要目的是**在保持模型性能的同时，减少模型的大小和计算资源需求**。通过蒸馏技术，较小的模型可以在推理时更高效地运行，适用于资源受限的环境。

![llm蒸馏](https://github.com/user-attachments/assets/4cdd90b7-44a1-457e-b4ec-f17b2da272cf)

### 蒸馏过程

蒸馏过程通常包括以下几个步骤：

* **训练教师模型**：首先训练一个大型且性能优越的教师模型。
* **生成软标签**：使用教师模型对训练数据进行预测，生成软目标 (soft targets) ，这些目标包含了教师模型的概率分布信息。
* **训练学生模型**：使用软目标 (soft targets) 和原始训练数据 (hard targets) 来训练较小的学生模型，使其能够模仿教师模型的行为。 这种方法不仅可以提高模型的效率，还可以在某些情况下提高模型的泛化能力。

### 蒸馏的优点

* 减少模型大小和计算资源需求
* 增加推理速度
* 易于访问和部署

(其实就是小模型相对于大模型的优点)

### 蒸馏可能存在的问题

* **信息丢失**：由于学生模型比教师模型小，可能无法完全捕捉教师模型的所有知识和细节，导致信息丢失。
* **依赖教师模型**：学生模型的性能高度依赖于教师模型的质量，如果教师模型本身存在偏差或错误，学生模型可能会继承这些问题。
* **适用性限制**：蒸馏技术可能不适用于所有类型的模型或任务，尤其是那些需要高精度和复杂推理的任务。

### 典型例子

* **GPT-4o** (教师模型) 中提炼出 GPT-4o-mini (学生模型)
* **DeepSeek-R1** (教师模型) 中提炼出 DeepSeek-R1-Distill-Qwen-32B (学生模型) (这个不是传统意义上的蒸馏了, 是蒸馏+数据增强+微调)

### 其他蒸馏技术

* **数据增强**: 使用教师模型生成额外的训练数据。通过创建更大、更具包容性的数据集，学生可以接触到更广泛的场景和示例，从而提高其泛化性能。
* **中间层蒸馏**: 将知识从教师模型的中间层转移到学生。通过学习这些中间表示，学生可以捕获更详细和结构化的信息，从而获得更好的整体表现。
* **多教师蒸馏**: 通过汇总不同教师模型的知识，学生模型可以实现更全面的理解并提高稳健性，因为它整合了不同的观点和见解。

### Refs

* [Knowledge Distillation: A Survey](https://arxiv.org/pdf/2006.05525)
* [Distilling the Knowledge in a Neural Network](https://arxiv.org/pdf/1503.02531)
* [LLM Distillation Explained: Applications, Implementation & More](https://www.datacamp.com/blog/distillation-llm)

## Transformer

### 什么是 Transformer?

> **Transformer 是一种用于自然语言处理 (NLP) 的深度学习模型架构**, 由 Vaswani 等人在 2017 年提出. 它主要用于处理序列到序列的任务, 如机器翻译, 文本生成等.

简单来讲, 文本生成的 Transformer 模型的原理是——**"预测下一个词"**.

**用户给定的文本 (prompt), 模型会预测下一个词最有可能是什么. Transformer 的核心创新和强大之处在于它使用的自注意力机制（self-attention mechanism）, 这使得它们能够处理整个序列, 并比之前的架构 (RNN) 更有效地捕捉长距离依赖关系.**

另外需要注意的是, GitHub 上的 huggingface/transformers 是 HuggingFace 实现的 Transformer 模型库, 包括了 Transformer 的实现和大量的预训练模型.

目前的 LLM 基本都基于 Transformer 架构, 并对其进行优化技术和训练方法的改进.

![Transformer](https://github.com/user-attachments/assets/d368ee46-1b89-456c-8077-50e077fbcf5f)

### Transformer 的结构

每个文本生成 Transformer 都由以下三个关键组件构成：

#### 嵌入层（Embedding）：

* 文本输入被分割成称为词元（token）的更小单位, 可以是单词或子词
* <u>这些词元被转换成称为嵌入（embeddings）的数值向量</u>
* 这些嵌入向量能够捕捉词语的语义含义

#### Transformer 块：

这是模型处理和转换输入数据的基本构建单元. 每个块包括：

* **注意力机制（Attention Mechanism）：**
  * Transformer 块的核心组件
  * 允许词元之间相互通信
  * 捕捉词语之间的上下文信息和关系

* **多层感知器（MLP）层：**
  * 一个前馈网络, 独立处理每个词元
  * 注意力层的目标是在词元之间路由信息
  * MLP 的目标是优化每个词元的表示

#### 输出概率（Output Probabilities）：

* 最终的线性层和 softmax 层
* 将处理后的嵌入转换为概率
* 使模型能够预测序列中的下一个词元

### Transformer 的优点：

* **并行化处理**：与 RNN 不同, Transformer 不需要按顺序处理数据, 因此可以更好地利用 GPU 进行并行计算, 提高训练速度.
* **长距离依赖**：自注意力机制使得 Transformer 能够有效捕捉序列中远距离的依赖关系.
* **灵活性**：Transformer 可以很容易地扩展到更大的模型 (如BERT、GPT等) , 并在多种 NLP 任务中表现出色.

### Transformer 的缺点：

* **计算复杂度高**：自注意力机制的计算复杂度为O(n^2), 当输入序列长度较长时, 计算资源消耗较大.
* **数据需求大**：Transformer 通常需要大量的数据进行训练, 以便充分发挥其性能.
* **缺乏内在的序列信息**：由于没有内置的序列处理机制 (如 RNN 中的时间步) , 需要额外的机制 (如位置编码) 来引入序列信息.

## Transformer 的优化方案都有哪些?

> 目前使用 Transformer 架构的模型, 都使用了一些优化方案来达到更好的效果或更高的性能, 所以我整理了常见的优化方案 (包括训练和推理), 后续会详细讲解每个优化方案的技术细节.

![Transformer优化](https://github.com/user-attachments/assets/6823137a-0c50-4852-9fcc-a8e143677945)

### 注意力机制优化

* **Flash Attention**
  * 减少内存访问和计算复杂度, 显著提升训练和推理速度
  * 被 Llama2, Qwen, PaLM2, Mistral, DeepSeek 等采用
  * 部分闭源模型可能采用类似技术或自研方案

* **Multi-Query Attention (MQA)**
  * 减少 Key 和 Value 的头数, 降低内存使用和计算量
  * 被 PaLM, Falcon, BLOOM 等采用

* **Grouped-Query Attention (GQA)**
  * MQA 的改进版本, 通过分组共享 Key/Value 矩阵（而非完全独立）实现性能和效率的平衡
  * 被 Llama2, PaLM2, Gemini, Mistral, DeepSeek 等采用

### 位置编码优化

* **RoPE (Rotary Position Embedding)**
  * 通过旋转矩阵实现相对位置编码, 支持更好的长度外推性
  * 被 Llama, DeepSeek, Qwen, Mistral, Falcon, PaLM 等广泛采用
  * 支持 NTK-aware 插值和 Dynamic NTK 等长度扩展方法

* **ALiBi (Attention with Linear Biases)**
  * 线性注意力偏置, 有助于外推到更长序列
  * 被 Bloom, Stable LM 等采用

### 架构优化

* **并行计算优化**

  * 通过解耦注意力层和前馈层的计算路径，实现：
    * 减少层间计算依赖，提升计算并行度
    * 优化内存访问模式，降低显存占用
    * 提高计算资源利用率（特别是 Tensor Core）
    * 支持更大 batch size 的训练
  * 典型实现方案：
    * **PaLM 并行结构**：同时计算注意力层和前馈层，结果合并后做残差连接
    * **GPT-3 模型并行**：通过张量/流水线并行实现超大规模模型训练
  * 被 PaLM, GPT-3, T5, Megatron-LM 等模型采用

* **激活函数优化**

  * 使用 SwiGLU 替代标准 FFN 中的激活函数, 提供更好的性能
  * 被 PaLM, Llama2, Gemini, Qwen 等采用

* **稀疏专家模型 (MoE)**

  * 通过多个专家网络实现大规模参数扩展
  * 被 Mixture-of-Experts, Switch Transformer 等采用

### 上下文长度扩展
* **滑动窗口注意力**

  * <u>局部注意力机制, 减少内存使用, 支持更长序列处理</u>
  * 被 Longformer, BigBird 等采用

* **稀疏注意力**

  * <u>只关注重要的 token, 降低计算复杂度, 提高处理长序列的能力</u>
  * 被 Sparse Transformer, Reformer, Longformer 等采用

### 内存优化

* **参数共享**
  * 跨层参数复用, 减少模型参数量, 降低内存需求
  * 被 ALBERT, T5 等采用

### 训练优化

* **混合精度训练**
  * FP16/BF16 混合精度训练广泛应用于大模型训练
  * FP8 目前主要用于推理阶段（如 NVIDIA H100）, 但 DeepSeek-V3 使用了 FP8 训练, 带来了巨大的成本优势, 甚至最新的论文还尝试了 FP4 训练
  * 大多数现代大模型使用 BF16 训练

* **梯度检查点**
  * 训练时动态重计算, 节省显存, 略微增加计算时间
  * 被大模型训练普遍采用

### 推理优化

* **KV Cache**
  * 通过缓存历史 token 的 Key/Value 矩阵实现：
    * 避免重复计算历史 token 的注意力结果
    * 减少解码时的计算量（复杂度从 O(n²) 降为 O(n)）
    * 降低内存带宽需求，提升推理速度
    * 支持更长的上下文处理
  * 内存管理策略：
    * 预分配固定长度内存
    * 动态扩展机制（如 vLLM 的 PagedAttention）
  * 被 Llama 系列、GPT 系列、PaLM、Gemini、Qwen 等主流模型采用


* **量化技术**
  * NT8/INT4 量化, 减少模型大小和内存占用, 加快推理速度
  * 主流量化方法包括 GPTQ, AWQ 等
  * 支持 per-tensor 和 per-channel 量化粒度
  * 被 LLaMA-2, ChatGLM, Qwen, Mistral, Yi 等采用

* **推理加速技术**
  * 推测解码 (Speculative Decoding)
  * 连续批处理 (Continuous Batching)
  * 动态批处理, 提高 GPU 利用率

### 特定硬件优化

* **GPU 特化**
  * CUDA 核心优化
  * Tensor Core 利用
  * 显存访问优化
  * 算子融合
  * 内存布局优化

这些优化方案通常会组合使用，不同的模型会根据自己的具体需求选择合适的优化方案。比如：
* **Llama2**: 采用 GQA + RoPE + Flash Attention
* **PaLM2**: 使用 GQA + 并行计算优化
* **Qwen**: 采用 Flash Attention + RoPE
