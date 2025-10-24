---
title: 大模型 GGUF 文件格式 & 推测性解码
date: 2025-9-30 15:04:40
categories:
  - ai笔记
tags:
  - 人工智能
  - 机器学习
thumbnail: https://user-images.githubusercontent.com/12566627/67846280-ebd8b180-fb3b-11e9-809b-157bbad20f2b.jpg

---

## 什么是 GGUF

[GGUF](https://github.com/ggml-org/ggml/blob/master/docs/gguf.md)（GGML Universal File）是一种专为大型语言模型（LLM）设计的文件格式。它旨在解决大型模型在实际应用中遇到的存储效率、加载速度、兼容性和扩展性等问题，从而简化 LLM 的使用和部署。

![GGUF图](https://github.com/user-attachments/assets/acb421ab-2608-4875-b9d6-77db822862bd)

### GGUF 的主要特点和优势
* **高效存储**： GGUF 格式优化了数据的存储方式，减少了存储空间的占用，这对于包含大量参数的大型模型尤为重要。它采用<u>紧凑的二进制编码格式</u>和<u>优化的数据结构</u>来高效地保存模型参数（权重和偏差）。
* **单文件部署**： 它们可以轻松分发和加载，加载模型所需的所有信息都包含在模型文件中，不需要任何外部文件来获取附加信息。
* **快速加载**： GGUF 格式支持快速加载模型数据（使用 mmap），这对于需要即时响应的应用场景非常有用，例如在线聊天机器人或实时翻译系统。
* **跨平台兼容性**： GGUF 兼容多种编程语言，例如 Python 和 R，非常方便在不同平台和环境中使用。大部分语言都可以使用少量代码轻松加载和保存模型，无需外部库。
* **支持微调**： GGUF 支持微调，允许用户根据特定的应用场景调整 LLM，并存储跨应用部署模型的提示模板。
* **取代 GGML**： GGUF 是 GGML 的替代者。GGML 由于在灵活性和扩展性方面存在一些限制，已被弃用，由 GGUF 取代。

### GGUF 的应用
GGUF 格式的模型文件可以用于各种应用场景，例如：

* **本地部署 LLM**： GGUF 格式使得在消费级计算机硬件（包括 CPU 和 GPU）上运行 LLM 成为可能。
* **移动设备上的 LLM 推理**： 由于其高效的存储和加载特性，GGUF 也适用于在移动设备上进行 LLM 推理。
* **快速原型开发**： GGUF 使得开发者可以更快速地加载和测试不同的 LLM 模型。

总而言之，GGUF 是一种重要的 LLM 文件格式，它通过提高存储效率、加载速度和兼容性，简化了 LLM 的使用和部署，并有望成为未来大模型文件标准格式之一。

## 如何本地运行 GGUF 格式的 LLM 模型?

### 模型下载
{% blockquote %}
这里下载的是 unsloth 团队的量化版本.

**下载地址**: https://huggingface.co/unsloth/DeepSeek-R1-Distill-Qwen-32B-GGUF/tree/main
{% endblockquote %}
![下载截图](https://github.com/user-attachments/assets/d2e97832-aeff-4258-b17b-d3cc46b715f3)
注意这个是个合集包, 里面有 Q2-Q8 的量化版本, 选中你喜欢的量化版本, 点击这个下载按钮即可. 不需要全部下载.

### 编译 llama.cpp
由于这个模型比较新 (2025-01-21发布的), 所以需要编译最新的 llama.cpp 才能支持这个模型.

首先下载 llama.cpp 的源码, 然后编译.

```bash
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
cmake -B build
cmake --build build --config Release -j
```
如果你的电脑没有 cmake, 请自己搜索如何安装 cmake. 问问 LLM 是好方法.

### 模型运行
直接运行编译好的 llama.cpp 即可.

```bash
./build/bin/llama-server -m /data/unslouth/DeepSeek-R1-Distill-Qwen-32B-GGUF/DeepSeek-R1-Distill-Qwen-32B-Q8_0.gguf --host 0.0.0.0 --port 9990
```

### ok 啦~!
在浏览器访问你指定的 IP 和端口, 例如 http://192.168.1.2:9990 即可.

![访问](https://github.com/user-attachments/assets/ed52a479-b540-4625-b7c9-e2ab22070b14)


## 推测性解码
推测性解码是一种用于优化大语言模型推理性能的技术。它的核心思想是：<u>在当前大模型生成当前 token 的同时，使用小的草稿模型对未来的 token 进行预测。</u>
![推测性解码](https://github.com/user-attachments/assets/c0b33ae6-0ee0-40c4-b18d-2d62bdd22f25)
### 工作原理

#### 双模型结构：

* **主模型（Target Model）**：完整的大语言模型，精度高但速度较慢
* **草稿模型（Draft Model）**：较小的模型，速度快但精度较低

#### 多头预测：

* 在模型中添加多个预测头（speculative heads）
* 每个预测头负责预测未来的一个 token（N+1, N+2, N+3...）
* 可以在单次前向传播中预测多个 token
* 使用较小的草稿模型快速预测多个可能的后续 token
* 草稿模型通常是主模型的蒸馏版本或更小的变体

#### 验证机制：

* 主模型会验证草稿模型预测的 token 序列
* 如果预测正确，就可以直接使用
* 如果预测错误，则回退到主模型重新生成

### 性能提升
根据文章报告的数据：

* 语言模型可获得约 2 倍的速度提升
* 代码模型可获得约 3 倍的速度提升

### 实现考虑

#### 模型选择：

* 草稿模型通常是主模型的 1/4 到 1/2 大小
* 例如：如果主模型是 70B 参数，草稿模型可能是 7B 或 13B 参数

#### 预测头数量：

* 语言模型通常使用 3-4 个预测头
* 代码模型可以使用 6-8 个预测头

#### 效率平衡：

* 需要在计算量、内存使用和预测准确性之间找到平衡
* 预测头数量增加会带来额外计算开销
* 但如果预测准确，可以减少内存访问次数

这项技术的**主要优势在于它能显著提升模型的推理速度，同时保证输出质量不受影响。它特别适合需要快速响应的生产环境使用。**
