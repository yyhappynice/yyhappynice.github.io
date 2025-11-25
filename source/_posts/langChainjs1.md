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

```py
方式一：pip install langchain
方式二：conda install langchain
```

### 2.2 简单demo

```py
chat_model = ChatOpenAI(
  model_name=model_name,
  base_url=base_url,  # 与模型交互的地址
  api_key=api_key,  # 秘钥
  temperature=0.7   # 温度参数,控制生成文本的随机性
)
```

## LangChain核心组件之Model I/O

### 3.1 Model I/O

Model I/O 是应用程序与大模型进行交互的组件，**它与大模型的关系类似于JDBC与数据库的关系**，本质上都是**为了解耦应用逻辑与底层实现，提供标准化的交互接口**，使应用程序无需关注大模型底层的实现，可与各种大模型进行交互。Model I/O 包括输入**提示(Format)**、**调用模型(Predict)**、**输出解析(Parse)**。分别对应着Prompt Template（提示词模版），Model （大模型）和Output Parser（输出解析器）。

![Image](https://github.com/user-attachments/assets/b2b7a276-4dc6-4496-9199-7a7303543e4c)

说白了，Model I/O 就是LangChain 提供了一系列与大模型交互的API。

### 3.2 大模型分类（按功能）

**1、LLMs（大语言模型）**

也叫非对话模型，**是许多语言模型应用程序的支柱**，通用的文本生成，能够完成一次性的文本生成任务，如写作、翻译等。

```py
llm_model = OpenAI()
llm_model.invoke("什么是LangChain?")
```

**2、Chat Models（对话模型）**

专门为**多轮对话场景**优化的语言模型。与LLMs处理纯字符串不同，Chat Models的输入和输出都是**结构化的消息对象**，能更好地理解和维护对话的上下文。

```py
# 创建ChatOpenAI模型实例
chat_model = ChatOpenAI(
  model_name=model_name,
  base_url=base_url,  # 与模型交互的地址
  api_key=api_key,  # 秘钥
  temperature=0.7   # 温度参数,控制生成文本的随机性
)

# 调用模型
response = chat_model.invoke("用简单一句话概括一下什么是反洗钱？")
# 打印模型响应内容
print(response)
```

**3、Embedding Model（嵌入模型）**

它的核心任务是**将文字、图片等非结构化数据，转换成高维空间中的数值向量**。并且能够将**语义相近的内容在向量空间中的位置也映射得更近。**

```py
# 我这里使用的嵌入模型是自己本地部署的，OpenAI的类为OpenAIEmbeddings
embeddings_model = OllamaEmbeddings(
    model="nomic-embed-text",
    base_url="http://127.0.0.1:11434",
)
vector = embeddings_model.embed_query("hello world")
print(f"嵌入向量长度: {len(vector)}")
print(f"前20个值: {vector[:20]}")
```

### 3.3 Message（消息）

消息是对话模型中通信的基本单位，用于表示与模型通讯的输入与输出，以及包含与对话相关的上下文信息和元数据，每一条消息都包含一个角色（系统、用户、AI等）和内容（用户的输入或模型的输出）以及其他元信息（id、名称、令牌使用情况等），LangChain提供了一个统一的消息格式，可以在不同模型之间使用。

**1、SystemMessage（系统消息）**：设定行为准则，用于定义大模型的角色、运行规则、环境信息等。如果模型不支持SystemMessage则LangChain会将消息合并到HumanMessage中一起发送给大模型。

**2、HumanMessage（用户消息）**：用户的输入，用户向模型发出的提问或指令。

**3、AIMessage（大模型消息，一般是模型返回的结果）**：大模型的输出，这是大模型对HumanMessage和SystemMessage的响应。它不仅包含生成的文本内容（content属性），还可能包含以下结构化信息（外部工具、调用令牌使用情况等元数据）。

**4、ToolMessage/ FunctionMessage**：连接外部能力，向模型传递外部工具或函数调用的执行结果，常用于Agent调用tool。

```py
chat_model = ChatOpenAI(
    model_name=model_name,
    base_url=base_url,  # 与模型交互的地址
    api_key=api_key  # 秘钥
)
messages = [
    # 创建系统消息，定义模型的角色
    SystemMessage(content="我是反洗钱领域的专家，我叫RiskHelper"),      # 创建用户消息，用户的提问
    HumanMessage(content="你好，什么是反洗钱？")
]
# 返回的类型是AI大模型消息
response = chat_model.invoke(messages)
print(type(response)) # 格式是AIMessage
```

### 3.4 Prompt Template（提示词模版）

**Prompt（提示词）**：提示词是与大模型交互时输入的内容，**用来指导大模型生成特定类型的回答或执行特定的任务**。它可以是一个简单的问题、一段详细的任务说明，或包含角色、背景、示例的复杂文本。其核心目的是**约束行为，减少错误，引导模型生成用户期望的响应**。好的提示词可以解决60%以上的模型幻觉问题，优化提示词也是我们后续开发中解决大模型幻觉最多且最有效的方式之一。

如下是一个简单的提示词：
```py
"""
您是一名资深LangChain框架专家，具备以下专业背景：
- 精通LangChain 0.3.x及以上版本的核心架构
- 熟练掌握Models、Prompts、Chains、Agents、Memory五大组件
- 拥有实际企业级LLM应用部署经验

请根据用户需求提供：
1. 架构设计建议（组件选型与组合逻辑）
2. 代码实现方案（包含最佳实践）
3. 性能优化策略（处理长文本/高并发场景）
4. 错误排查指导（常见陷阱与解决方案）用户输入: {input}
"""
```

**Prompt Template（提示词模版）**：固定的提示词限制了模型的灵活性和适用范围，所以 Prompt Template 是一个模板化的字符串，**可以将变量（如用户提问等）插入到模板的占位符中**，从而创建出不同的提示。LangChain提供了很多提示词模版，用于与大模型交互，常见的提示词模版如下：

**1、PromptTemplate**：LLM提示模板，用于生成字符串提示，生成的是单一、无角色区分的纯文本字符串。

```py
# 创建PromptTemplate
prompt_template = PromptTemplate(
    template="如何成为一个{domain}领域的专家",
    input_variables=["domain"]
)
# 填充模版参数，将反洗钱填充到domain字段中
messages = prompt_template.invoke({"domain": "反洗钱"})
response = chat_model.invoke(messages)
print(response.content)
```