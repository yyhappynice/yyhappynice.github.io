---
title: 大模型 GGUF 格式 & 推测性解码
date: 2025-10-22 15:04:40
categories:
  - ai笔记
tags:
  - 人工智能
  - 机器学习
thumbnail: https://user-images.githubusercontent.com/12566627/67846280-ebd8b180-fb3b-11e9-809b-157bbad20f2b.jpg
---

## 什么是 GGUF

[GGUF](https://github.com/ggml-org/ggml/blob/master/docs/gguf.md)（GGML Universal File）是一种专为大型语言模型（LLM）设计的文件格式。它旨在解决大型模型在实际应用中遇到的存储效率、加载速度、兼容性和扩展性等问题，从而简化 LLM 的使用和部署。

### GGUF 的主要特点和优势
* **高效存储**： GGUF 格式优化了数据的存储方式，减少了存储空间的占用，这对于包含大量参数的大型模型尤为重要。它采用*紧凑的二进制编码格式*和*优化的数据结构*来高效地保存模型参数（权重和偏差）。
* **单文件部署**： 它们可以轻松分发和加载，加载模型所需的所有信息都包含在模型文件中，不需要任何外部文件来获取附加信息。
* **快速加载**： GGUF 格式支持快速加载模型数据（使用 mmap），这对于需要即时响应的应用场景非常有用，例如在线聊天机器人或实时翻译系统。
* **跨平台兼容性**： GGUF 兼容多种编程语言，例如 Python 和 R，非常方便在不同平台和环境中使用。大部分语言都可以使用少量代码轻松加载和保存模型，无需外部库。
* **支持微调**： GGUF 支持微调，允许用户根据特定的应用场景调整 LLM，并存储跨应用部署模型的提示模板。
* **取代 GGML**： GGUF 是 GGML 的替代者。GGML 由于在灵活性和扩展性方面存在一些限制，已被弃用，由 GGUF 取代。

### GGUF 的应用



<div style="text-align: center; color: red;">
这是居中的红色文字
</div>


<details>
<summary>点击展开</summary>
这里是隐藏的内容
</details>

这是一个脚注[^1]

[^1]: 这是脚注的内容

{% blockquote %}
这是一个引用块
{% endblockquote %}

{% codeblock lang:javascript %}
function hello() {
    console.log("Hello World!");
}
{% endcodeblock %}

{% link 链接标题 https://example.com %}

[TOC]  # 自动生成目录

- [x] 已完成的任务
- [ ] 未完成的任务
- [ ] 另一个任务
| 左对齐 | 居中对齐 | 右对齐 |
|:-------|:-------:|-------:|
| 内容1  |  内容2  |  内容3 |