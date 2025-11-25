---
title: LangChainjs 框架简介
date: 2025-11-6 11:10:16
categories:
  - LangChainjs
tags:
  - 人工智能
  - 机器学习

---
![langchain](https://github.com/user-attachments/assets/b32fafaa-f59d-46eb-98ac-87c839a8d1fe)

## 1. LangChain概述

### 1.1 什么是LangChain

LangChain是2022年10月（ChatGPT在2022年11月30问世，比ChatGPT还早），由哈佛大学的Harrison Chase（哈里森·蔡斯）发起研发的一个**用于开发基于大语言模型（LLM） 应用程序的开源框架**，它的核心目标是简化AI应用的构建过程，让开发者能像搭积木一样，快速组合各种模块来实现复杂功能，如：搭建智能体（Agent）、问答系统（QA）、对话机器人、知识库等。

**LangChain在Github上的热度变化：**

![热度](https://github.com/user-attachments/assets/23172560-274c-4ab3-97fb-1bce9bc046cf)

**AI大模型架构图：**

![Image](https://github.com/user-attachments/assets/13385a10-66a8-4b0d-b4be-c34dc9894843)

**LangChain所处的位置：**

![Image](https://github.com/user-attachments/assets/22979beb-7cfd-4431-937c-06c2aa98cbe1)

### 1.2 为什么使用LangChain

使用 LangChain 的核心价值在于，它提供了一套**高度模块化、可扩展的工具集**，让开发人员能够快速、灵活地构建功能丰富的大模型应用，无需关注底层复杂的细节。它开发难度小，学习成本低，并且提供了现成的链式组装，能够让复杂的逻辑变得结构化、可扩展。LangChain最初只是一个开源的软件包，如今已称为一个完整的生态系统，它提供了一系列的标准化组件，且都可以单独使用。

下表是直接对接大模型和使用LangChain的对比：


<table>
  <colgroup>
    <col style="width:132px">
    <col style="width:292px">
    <col>
  </colgroup>
  <thead>
    <tr>
      <th>对比维度</th>
      <th>直接调用大模型API</th>
      <th>使用 LangChain 开发</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>开发模式</td>
      <td>相对直接，但复杂功能需大量自定义代码</td>
      <td>模块化组装，提供大量预制组件和链，简化复杂逻辑</td>
    </tr>
    <tr>
      <td>多模型支持</td>
      <td>通常需为不同供应商的API编写适配代码</td>
      <td>统一接口，轻松切换或组合不同模型（如OpenAI、Anthropic等）</td>
    </tr>
    <tr>
      <td>外部数据集成</td>
      <td>自行实现数据加载、处理、向量化与检索逻辑</td>
      <td>内置RAG（检索增强生成）等强大支持，可轻松连接PDF、数据库、API等外部数据源</td>
    </tr>
    <tr>
      <td>上下文管理</td>
      <td>需手动管理对话历史，易超出Token限制</td>
      <td>内置Memory组件，灵活管理短期和长期记忆，维持连贯对话</td>
    </tr>
    <tr>
      <td>复杂任务自动化</td>
      <td>实现多步骤推理或工具调用逻辑复杂</td>
      <td>通过Agents，让模型能自主决策、调用工具（如计算器、搜索引擎）完成任务</td>
    </tr>
    <tr>
      <td>生产部署与调试</td>
      <td>缺乏标准化工具，监控和调试较困</td>
      <td>提供LangSmith等平台，用于监控、追踪和调试应用性能</td>
    </tr>
  </tbody>
</table>

### 1.3 LangChain架构设计（宏观）

LangChain是由多个包组成的框架：

![Image](https://github.com/user-attachments/assets/12608792-a56d-4573-aa4a-c7e3ac092e10)

图中展示了LangChain（V0.3版本）生态系统的主要组件及其分类，分为三个层次：**架构**(Architecture)、**组件**(Components)和**部署**(Deployment)。

**结构1：LangChain（架构中的LangChain，也是后文介绍的）**：封装了一系列的API。

1. langchain：构成应用程序认知架构的Chains，Agents，Retrieval strategies等
2. langchain-community：第三方集成，⽐如：Model I/O、Retrieval、Tool等。
3. langchain-Core：基础抽象和LangChain表达式语言 (LCEL)

**结构2：LangGraph**：基于有向图+条件边来灵活的构建多智能体应用，提供了条件分支、循环、并行等复杂控制流，能够实现状态持久化、断点续跑、时间旅行、人机协作等高级功能。

**结构3：LangSmith**：用于构建、调试、测试、评估、监控和链路追踪大模型应用程序，提供了6大功能，涉及Debugging (调试)、Playground (沙盒)、Prompt Management (提示管理)、Annotation (注释)、Testing (测试)、Monitoring (监控)等。与LangChain无缝集成，从原型阶段过渡到生产阶段。

**结构4：LangServe**：将基于 LangChain 开发的链（Chain）、代理（Agent）等快速部署为 REST API 服务。它基于 FastAPI 构建，极大简化了 AI 应用服务化的流程

**LangChain当中，目前最火的两个模块就是：LangGraph，LangSmith。**

### 1.4 LangChain核心组件

LangChain提供了一个高度模块化且可组合的框架，开发者能通过灵活集成其六大核心组件，来构建功能复杂的大模型应用。

![Image](https://github.com/user-attachments/assets/2f323fe4-3e05-4cc3-b0eb-d4836680596c)

1. **Model I/O（模型交互标准化接口）**：提供统一的模型交互接口，封装提示模板调用、模型推理与输出解析，实现不同大语言模型输入的标准化与输出的结构化处理

2. **Chains（链）**：用于链式工作流编排，将多个模块串联起来组成一个完整的流程，例如，一个 Chain 可能包括一个 Prompt 模板、一个大模型和一个输出解析器，它们协同工作，处理用户输入并返回结果。

3. **Memory（记忆）**：用于保存历史对话和上下文信息，以便在后续对话中使用，从而实现有状态的对话。

4. **Agents（智能体）**：自主决策并调用工具，赋予大模型行动能力

5. **Retrieval（检索，RAG核心）**：从大量的文档或数据源中查找相关信息的核心模块，是构建RAG（检索增强生成）的基础

6. **Callbacks（保障应用可观测性）**：回调机制，允许连接到 LLM 应用程序的各个阶段，可以监控和分析LangChain的运行情况，比如日志记录、监控、流传输等，以优化性能。

**后续章节将详细介绍以上组件的使用方式。**

## 2. LangChain使用之环境准备

### 2.1 安装LangChain

LangChain基于Python开发，因此需确保系统中安装了Python环境。

**安装LangChain**

```python
方式一：pip install langchain
方式二：conda install langchain
```

### 2.2 简单demo

```
chat_model = ChatOpenAI(
    model_name=model_name,
    base_url=base_url,  # 与模型交互的地址
    api_key=api_key,  # 秘钥
    temperature=0.7   # 温度参数,控制生成文本的随机性
)
```
