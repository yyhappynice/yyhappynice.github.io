---
title: Attention 注意力机制
date: 2025-10-18 15:13:05
categories:
  - ai笔记
tags:
  - 人工智能
  - 机器学习
mathjax: true

---

## 什么是 Flash Attention?

> Flash Attention 是一种优化的注意力机制, **旨在提高深度学习模型中注意力计算的效率. 它通优化访存机制来加速训练和推理过程.**

![FlashAttention](https://github.com/user-attachments/assets/4e3e07f5-619a-4a22-af38-1701aa6c84c1)

标准的注意力机制使用 HBM 来存储、读取和写入注意力分数矩阵（attention score matrix, 矩阵存储 Q/K/V). 具体步骤为将这些从 HBM 加载到 GPU 的片上 SRAM, 然后执行注意力机制的单个步骤, 然后写回 HBM, 并重复此过程.

而 Flash Attention 则是采用分块计算（Tiling）技术，将大型注意力矩阵划分为多个块（tile），在 SRAM 中逐块执行计算。通过：

* **分块策略**：将 Q/K/V 矩阵分块后流水线处理，避免存储完整的中间矩阵
* **重计算（Recomputation）**：在反向传播时动态重新计算前向结果，而非存储中间值
* **IO优化**：通过精确的内存访问控制，使数据在 HBM 和 SRAM 间的移动最小化

### 优点
* **计算效率高**：通过分块并行计算和半精度（FP16/BF16）优化，充分利用 GPU Tensor Cores
* **内存使用减少**：重计算技术减少 4-20 倍内存占用，支持更长序列训练
* **训练加速**：反向传播通过延迟重计算优化，实现端到端 2-4 倍加速
* **精度保持**：采用平铺分块策略时仍保持数值稳定性，支持混合精度训练

### 缺点
* **实现复杂**：由于需要对底层计算进行优化, Flash Attention 的实现可能比传统注意力机制更复杂.
* **硬件依赖**：在某些情况下, 可能需要特定的硬件支持才能充分发挥其性能优势.
* **调试困难**：优化后的计算过程可能导致调试和故障排查变得更加困难.

总的来说, Flash Attention 是一种强大的工具, 能够在不牺牲性能的情况下提高模型的效率, 但在实现和使用时需要考虑其复杂性和硬件要求.

### 性能
![性能](https://github.com/user-attachments/assets/5b18e7cf-8bee-461f-bf56-135211a43ea1)

当使用 H100 显卡且序列长度是512时（数据来自论文测试），PyTorch 的标准处理速度是 62 Tflops，而 Flash Attention 则可以达到 157 Tflops，Flash Attention 2 则可以达到215 Tflops。在 FP16/BF16 精度下，实际加速比可达标准实现的 3-4 倍。

## 什么是 Multi-Head Attention?

多头注意力（Multi-Head Attention）是 Transformer 架构中的一个核心组件，它通过并行运行多个注意力机制来增强模型的性能。

在多头注意力机制中，"头"是指一个独立的注意力机制。每个头有自己的一组权重，用于计算输入的自注意力。通过使用多个头，模型可以从不同的角度和特征空间中提取信息。

![Multi-Head](https://github.com/user-attachments/assets/71ebb120-fbf2-476a-a458-12dd962c5d7c)

### 工作原理

首先我们来看注意力公式, 给定输入向量 $Q$（查询）、$K$（键）和 $V$（值），注意力机制的计算公式为：

$$
\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V
$$

其中，$d_k$ 是$K$ (键) 向量的维度。


多头注意力则将上面的公式拆分, 通过多个独立的注意力头来增强模型的能力。每个头有自己的查询、键和值的线性变换。公式如下：

$$
\text{MultiHead}(Q, K, V) = \text{Concat}(\text{head}_1, \ldots, \text{head}_h)W^O
$$

其中每个 $\text{head}_i$ 计算为：

$$
\text{head}_i = \text{Attention}(QW_i^Q, KW_i^K, VW_i^V)
$$

$W_i^Q, W_i^K, W_i^V, W^O$ 是学习到的参数矩阵。

多头注意力机制将输入分成多个"头"，每个头独立地执行自注意力计算，然后将所有头的输出合并起来。每个头可以关注输入序列的不同方面，从而捕获更丰富的特征信息。

### 核心机制

- **维度拆分**：将输入向量维度 $d_{model}$ 通过线性投影拆分为$h$个$d_k$维度（$d_k$ = $d_{model}/h$），每个头关注不同的特征子空间

### 优点

- **并行计算优化**：虽然总计算量（FLOPs）与单头注意力相同，但拆分后的多个小矩阵乘法（尺寸$h×d_k$）更适配GPU并行计算特性 (当然实际 FLOPs 消耗会略高于单头，因为增加了投影矩阵计算)

### 缺点

- **内存/计算开销**：每个头需要独立的 Q/K/V 投影矩阵，参数数量随头数线性增长：

  $$
  3hd_kd_{model} \text{ (输入投影)} + hd_kd_{model} \text{ (输出投影)} = 4hd_kd_{model}
  $$

  其中：
  - 输入投影：每个头包含 $W_i^Q, W_i^K, W_i^V \in \mathbb{R}^{d_{model}\times d_k}$ 三个矩阵，共 $3hd_kd_{model}$ 参数
  - 输出投影：合并矩阵 $W^O \in \mathbb{R}^{hd_k\times d_{model}}$，贡献 $hd_kd_{model}$ 参数
  - 当采用标准配置 $d_k = d_{model}/h$ 时，总参数量简化为 $4d_{model}^2$（与头数无关）

- **键值缓存瓶颈**：自回归解码时每个头需要独立缓存 K/V 矩阵，显存占用为 $2bd_{model}L$（b=batch_size, L=seq_len）
- **信息冗余**：实验表明不同头可能学习到相似的注意力模式（尤其在后层），造成计算资源浪费
- **工程复杂度**：多头并行计算需要精细的内存布局管理，在长序列场景下容易导致内存带宽瓶颈

### 与 MQA/GQA 的对比

| 特性               | Multi-Head (MHA) | Multi-Query (MQA)       | Grouped-Query (GQA)      |
|--------------------|------------------|-------------------------|--------------------------|
| 键值投影共享      | 无               | 所有头共享同一 K/V 投影 | 分组内共享 K/V 投影      |
| 参数量            | $4hd_kd_{model}$ | $hd_kd_{model} + 2d_kd_{model}$ | $(h + 2g)d_kd_{model}$  |
| 解码显存占用      | 高               | 极低（1/h）             | 中等（g/h）              |
| 模型容量          | 最高             | 最低                   | 可调节（通过分组数 g）   |
| 典型应用场景      | 编码器          | 低内存推理场景         | 质量与效率的平衡点       |


## 什么是 Multi-Query Attention?

>多查询注意力（Multi-Query Attention）是 Transformer 解码器的优化版本，**通过共享键/值投影来显著降低内存消耗，特别适合自回归生成任务。**

![Image](https://github.com/user-attachments/assets/766f5fdb-0f7e-429d-b4da-7f72cb35487b)

### 工作原理

在标准多头注意力基础上进行关键修改：所有注意力头共享同一组键（K）和值（V）的投影矩阵，仅保留查询（Q）的独立投影。公式如下：

$$
\text{MultiQuery}(Q, K, V) = \text{Concat}(\text{head}_1, \ldots, \text{head}_h)W^O
$$

其中每个 $\text{head}_i$ 计算为：

$$
\text{head}_i = \text{Attention}(QW_i^Q, KW^K, VW^V)
$$

$W_i^Q \in \mathbb{R}^{d_{model}\times d_k}$ 保持独立，而 $W^K, W^V \in \mathbb{R}^{d_{model}\times d_k}$ 被所有头共享。

### 核心机制

- **键值共享**：所有注意力头共享同一组 K/V 投影矩阵，仅保留 Q 的独立投影
- **内存优化**：自回归解码时只需缓存单组 K/V 矩阵，显存占用降低为原始 MHA 的 $1/h$

### 优点

- **参数效率**：投影矩阵参数量从 $4hd_kd_{model}$ 降为 $hd_kd_{model} + 2d_kd_{model}$（减少约 75%）
- **解码加速**：KV 缓存量减少 h 倍，在长序列生成（如 2048 tokens）时显著降低内存带宽压力
- **硬件友好**：共享的 K/V 投影产生更规整的内存访问模式，提升 GPU/TPU 利用率

### 缺点

- **容量限制**：共享 K/V 投影削弱了模型对不同表示子空间的捕捉能力，可能影响生成质量
- **训练挑战**：需要更谨慎的参数初始化来补偿表示能力的损失
- **工程复杂度**：共享投影引入跨头依赖，增加分布式计算的同步开销

### 与 MHA/GQA 的对比

| 特性               | Multi-Head (MHA) | Multi-Query (MQA)       | Grouped-Query (GQA)      |
|--------------------|------------------|-------------------------|--------------------------|
| 键值投影共享      | 无               | 所有头共享同一 K/V 投影 | 分组内共享 K/V 投影      |
| 参数量            | $4hd_kd_{model}$ | $(h + 2)d_kd_{model}$  | $(h + 2g)d_kd_{model}$  |
| 解码显存占用      | $2bd_{model}L$   | $2bd_kL$                | $2bgd_kL$               |
| 模型质量          | 最优             | 基线模型 90%-95%        | 接近 MHA (98%-99%)       |
| 典型应用场景      | 预训练          | 低内存推理场景         | 生产环境部署            |

## 什么是 Grouped-Query Attention?

Grouped-Query Attention（分组查询注意力）是 Transformer 架构的改进型注意力机制，在多头注意力（MHA）和多查询注意力（MQA）之间取得平衡。通过分组共享键值投影，在保持模型容量的同时显著降低计算资源消耗。

![Grouped-Query](https://github.com/user-attachments/assets/f6dceb73-8d38-41a0-8927-6194972e2b7e)

### 工作原理

在标准多头注意力基础上进行关键修改：所有注意力头共享同一组键（K）和值（V）的投影矩阵，仅保留查询（Q）的独立投影。公式如下：

$$
\text{MultiQuery}(Q, K, V) = \text{Concat}(\text{head}_1, \ldots, \text{head}_h)W^O
$$

其中每个 $\text{head}_i$ 计算为：

$$
\text{head}_i = \text{Attention}(QW_i^Q, KW^K, VW^V)
$$

$W_i^Q \in \mathbb{R}^{d_{model}\times d_k}$ 保持独立，而 $W^K, W^V \in \mathbb{R}^{d_{model}\times d_k}$ 被所有头共享。


### 核心机制

- **键值共享**：所有注意力头共享同一组 K/V 投影矩阵，仅保留 Q 的独立投影
- **内存优化**：自回归解码时只需缓存单组 K/V 矩阵，显存占用降低为原始 MHA 的 $1/h$

### 优点

- **参数效率**：投影矩阵参数量从 $4hd_kd_{model}$ 降为 $hd_kd_{model} + 2d_kd_{model}$（减少约 75%）
- **解码加速**：KV 缓存量减少 h 倍，在长序列生成（如 2048 tokens）时显著降低内存带宽压力
- **硬件友好**：共享的 K/V 投影产生更规整的内存访问模式，提升 GPU/TPU 利用率

### 缺点

- **容量限制**：共享 K/V 投影削弱了模型对不同表示子空间的捕捉能力，可能影响生成质量
- **训练挑战**：需要更谨慎的参数初始化来补偿表示能力的损失
- **工程复杂度**：共享投影引入跨头依赖，增加分布式计算的同步开销

### 与 MHA/GQA 的对比

| 特性               | Multi-Head (MHA) | Multi-Query (MQA)       | Grouped-Query (GQA)      |
|--------------------|------------------|-------------------------|--------------------------|
| 键值投影共享      | 无               | 所有头共享同一 K/V 投影 | 分组内共享 K/V 投影      |
| 参数量            | $4hd_kd_{model}$ | $(h + 2)d_kd_{model}$  | $(h + 2g)d_kd_{model}$  |
| 解码显存占用      | $2bd_{model}L$   | $2bd_kL$                | $2bgd_kL$               |
| 模型质量          | 最优             | 基线模型 90%-95%        | 接近 MHA (98%-99%)       |
| 典型应用场景      | 预训练          | 低内存推理场景         | 生产环境部署            |

