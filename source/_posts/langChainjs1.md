---
title: LangChain 框架简介
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
    <col style="width:136px">
    <col style="width:320px">
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
      <td>自行实现数据加载、处理、向量化与检索</td>
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

## 3. LangChain核心组件之Model I/O

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

**2、ChatPromptTemplate**：创建聊天消息列表的提示模板。**生成的是结构化的消息列表，其中每条消息都带有明确的角色**（如系统、用户、AI），用在对话模型中。

```py
# 方式一，使用构造函数创建
chatprompt_template = ChatPromptTemplate([
    # key: 角色, value: 内容,
    ("system", "你是一个反洗钱领域的专家，你的名字是{name}"),  # 系统提示词
    ("human", "帮我解答一下{question}")   # 用户提示词
])

# 填充模版参数
message = chatprompt_template.format(name="RiskHelper", question="什么是反洗钱？")
response = chat_model.invoke(message)
print(response.content)

# 方式二，使用from_messages方法创建
chatprompt_template = ChatPromptTemplate.from_messages([
    ("system", "你是一个反洗钱领域的专家，你的名字是{name}"),
    ("human", "帮我解答一下{question}")
])
# 填充模版参数
messages = chatprompt_template.format(name="RiskHelper", question="什么是反洗钱？")
response = chat_model.invoke(messages)
print(response.content)
```

**3、XxxMessagePromptTemplate** ：特定角色的消息提示词模板，包括：SystemMessagePromptTemplate、HumanMessagePromptTemplate、AIMessagePromptTemplate、ChatMessagePromptTemplate等，通常用ChatPromptTemplate将消息提示词模版打包在一起，一并发送给模型。

```py
# 创建聊天提示词模版
chatprompt_template = ChatPromptTemplate([
    # 创建系统提示词模版
    SystemMessagePromptTemplate.from_template("你是一个反洗钱领域的专家，你的名字是{name}"),
    # 创建用户提示词模版
    HumanMessagePromptTemplate.from_template("帮我解答一下{question}")
])

# 填充模版参数
message = chatprompt_template.format(name="RiskHelper", question="什么是反洗钱？")
response = chat_model.invoke(message)
print(response.content)
```

**4、FewShotPromptTemplate**：样本提示词模板，提供少量的样例作为参考来引导大模型按照特定的格式和风格输出。

```py
# 1. 定义示例数据
examples = [
    {
        "question": "什么是Spring Boot？",
        "answer": "Spring Boot是一个基于Spring框架的开源Java框架，用于简化Spring应用程序的创建和部署。"
    },
    {
        "question": "什么是依赖注入？",
        "answer": "依赖注入是一种设计模式，通过外部容器向对象提供其所需的依赖，而不是对象自己创建依赖。"
    },
    {
        "question": "什么是RESTful API？",
        "answer": "RESTful API是遵循REST架构风格的Web服务接口，使用HTTP方法进行资源操作。"
    }
]
# 2. 定义示例模板
example_prompt = PromptTemplate(
    input_variables=["question", "answer"],
    template="问题: {question}\n答案: {answer}"
)
# 3. 创建few-shot提示词模版
few_shot_prompt = FewShotPromptTemplate(
    examples=examples,
    example_prompt=example_prompt,
    suffix="问题: {question}",
    input_variables=["question"]
)
formatted_prompt = few_shot_prompt.format(question="什么是LangChain？")
response = chat_model.invoke(formatted_prompt)
print(response.content)
```

还有一些其他的提示词模版，可以参考：
https://python.langchain.com.cn/docs/modules/model_io/prompts/prompt_templates/

### 3.5 Output Parsers（输出解析器）

大模型通常返回的内容都是字符串格式，但是我们实际开发中更擅长处理结构化数据，输出解析器就是**将大模型输出的结果转换成特定的结构化数据**，以便应用程序能更方便的处理。LangChain提供了一些常见的输出解析器，如**StrOutputParser**（字符串解析器）、**JsonOutputParser**（json解析器）、**CommaSeparatedListOutputParser**（csv解析器）、**DatetimeOutputParserde**（日期解析器）、**XmlOutputParser**（xml解析器）等下边用**JsonOutputParser**写一个简单的实例

```py
json_parser = JsonOutputParser()
prompt_template = PromptTemplate(
    template="用简单的一句话描述一下什么是{name}？请按照以下格式输出：{format_instructions}",
    input_variables=["name"],
    # 告诉大模型，按照json格式输出
    partial_variables={"format_instructions": json_parser.get_format_instructions()}
)
messages = prompt_template.invoke({"name": "反洗钱"})
response = chat_model.invoke(messages)
print(json_parser.parse(response.content))
```

### 3.6 模型的调用方式

Runnable 接口是使用LangChain组件的基础，它在许多组件中实现，例如语言模型、输出解析器、检索器、编译的LangGraph 图等。Runnable 方式定义了一个标准接口，允许 Runnable 组件用以下方式调用：

**1、invoke**：处理单条输入，等待模型完全处理完成后再返回结果，上述的例子都是invoke调用

**2、stream**：流式响应，逐字符输出模型的响应，类似于我们日常使用的AI应用（元宝）一样，给用户输出是逐字输出，无需等所有的结果生成后一次返回，对于用户交互体验较好。

```py
# 流式输出
chat_model = ChatOpenAI(
    model_name=model_name,
    base_url=base_url,  # 与模型交互的地址
    api_key=api_key,  # 秘钥
    streaming=True   # 开启流式输出
)
chatprompt_template = ChatPromptTemplate([
    SystemMessagePromptTemplate.from_template("你是一个反洗钱领域的专家"),
    HumanMessagePromptTemplate.from_template("请详细讲一下什么是{value}？")
])
message = chatprompt_template.invoke({"value": "反洗钱"})
for output in chat_model.stream(message):
    # 逐个打印token内容
    print(output.content, end="", flush=True)
```

**3、batch**：批量处理，可以将多个消息一起发送给模型。

```py
# 创建三个消息
message1 = [HumanMessage(content="什么是风控？")]
message2 = [HumanMessage(content="什么是反洗钱？")]
message3 = [HumanMessage(content="什么是EDD？")]
messages = [message1, message2, message3]
# 批量调用
response = chat_model.batch(messages)
print(response)
```

LangChain还提供了与上边相对应的异步方法：

1. **astream**：异步流式响应
2. **ainvoke**：异步处理单条输入
3. **abatch**：异步处理批量输入
4. **astream_log**：异步流式返回中间步骤，以及最终响应

如果对具体的调用方式感兴趣可以参考官方文档：LangChain-Runnable调用

https://docs.langchain.com/oss/javascript/langchain/overview

## 4. LangChain核心组件之Chains

### 4.1 Chain（链）概述

链，它将多个组件（提示词模版、大模型、记忆、工具、输出解析器）串联起来，**形成一个可执行的，自动化的工作流程，类似于流水线作业**。它将上一个组件的输出作为下一个组件的输入，将多个步骤串连起来执行，最终将模型的响应结果返回给用户。一个小Chain可以包含提示词模版->大模型->输出解析器，一个主Chain也可以将多个子Chain串起来，比如输入一篇文章先翻译（Chain A），再总结（Chain B）。

![Image](https://github.com/user-attachments/assets/afb1b147-aed5-4cc2-96e6-b5be841a6838)

### 4.2 Chain组件使用

**1、LLMChain（已过时）**：最基础的Chain，将一个提示词模板和一个大模型封装在一起，构成一个可执行的单元。适用于简单的问答、文本生成等单步任务。它的工作流程是：将用户输入的数据填充提示词模板，然后将格式化后的提示词发送给大模型，最终返回大模型的生成结果。

```py
chatprompt_template = ChatPromptTemplate([
    SystemMessagePromptTemplate.from_template("你是一个反洗钱领域的专家，你的名字是{name}"),
    HumanMessagePromptTemplate.from_template("帮我解答一下{question}")
])
# 创建链
chain = LLMChain(llm=chat_model, prompt=chatprompt_template)
# 调用链，先执行prompt，并将结果给到llm，再执行llm，最后返回结果
response = chain.invoke({"name": "RiskHelper", "question": "什么是反洗钱？"})
print(response)
```

注意：上述是LLMChain的使用，但是LLMChain在0.1.17版本已经被标记为过时，且在1.0版本后会被移除。

**2、SequentialChain（顺序链，已过时）**：通过将多个Chain串起来，实现分步任务处理，前一个链的输出作为后一个链的输入，适用于需要分步处理的任务，如先总结再翻译。

```py
chatprompt_template1 = ChatPromptTemplate([
    SystemMessagePromptTemplate.from_template("你是一个{name}领域的专家"),
    HumanMessagePromptTemplate.from_template("请详细讲一下什么是{title}？")
])
chain1 = LLMChain(llm=chat_model, prompt=chatprompt_template1, output_key="text")
chatprompt_template2 = ChatPromptTemplate([
    SystemMessagePromptTemplate.from_template("你是一个文章总结专家"),
    HumanMessagePromptTemplate.from_template("请帮我用一句话概括一下{text}")
])
chain2 = LLMChain(llm=chat_model, prompt=chatprompt_template2, output_key="translation")
# 构建顺序链
chain = SequentialChain(
    chains=[chain1, chain2],
    input_variables=["name", "title"],
    output_variables=["translation"]
)
response = chain.invoke({"name": "反洗钱", "title": "EDD"})
print(response)
```

SequentialChain也在0.1.17版本被标记为过时，且在1.0版本后会被移除

**3、LCEL（LangChain Expression Language，推荐使用的方式）**：是 LangChain 框架中用于构建和组合Chain的一种声明式、模块化且高效的语言。类似于Linux中的管道符 | ，将提示模板、语言模型、输出解析器等组件灵活地组合成可执行的工作流。

```py
json_parser = JsonOutputParser()
prompt_template = PromptTemplate(
    template="用简单的一句话描述一下什么是{name}？请按照以下格式输出：{format_instructions}",
    input_variables=["name"],
    # 告诉大模型，按照json格式输出
    partial_variables={"format_instructions": json_parser.get_format_instructions()}
)
# 使用管道符构建chain
chain = prompt_template | chat_model | json_parser
response = chain.invoke({"name": "反洗钱"})
print(response)
```

除了上述的chain之外，LangChain还提供了以下类型的chain。

1. **LLMMathChain（数学链）**：将用户问题转换为数学问题，再转换为可以使用Python的numexpr库执行的表达式。
2. **RouterChain（路由链）**：根据输入内容分析并决定调用哪个专用的子链处理。可以自动分析用户的需求，然后路由到最适合的链中执行，并返回最终结果。
3. **StuffDocumentsChain（文档链）**：将多个文档内容合并 （“填充”或“塞入”）到单个提示词中，然后调用大模型进行处理。


## 5. LangChain核心组件之Memory

### 5.1 Memory（记忆）概述

我们知道，**大模型本身都不会记忆任何上下文信息**，那为什么我们常用的大模型应用能够清楚的知道我们之前的对话内容（与AI应用可以进行多轮对话）？并且有一定的记忆能力，这就需要额外的模块去保存我们和大模型进行对话的上下文信息，然后在下一次对话前将历史的记录全部发送给大模型，提供给大模型参考以便能够更准确的回答当前的提问。

在LangChain中，这个用于存储用户和模型交互的历史信息的组件叫Memory，它能够让应用记住用户之前说了什么，从而实现对话的上下文感知能力，为构建真正智能和上下文感知的链式对话系统提供了基础。

### 5.2 Memory的原理

![Image](https://github.com/user-attachments/assets/4168175f-531f-48fd-8744-264d615b47b7)

STEP1：用户输入问题
STEP2：从Memory中读取历史消息
STEP3：构建新的提示词，包含历史消息和当前的提问
STEP4：发送给大模型处理
STEP5：解析大模型输出，并返回用户
STEP6：将历史对话和当前对话一起保存在Memory中，以便下一次使用

### 5.3 Memory组件使用

LangChain中提供了一系列的Memory用于保存历史上下文，不同的Memory保存的内容也不一样，常用的使用方式如下：

**1、ChatMessageHistory**：用于存储和管理对话消息的基础类，它直接操作消息对象（如HumanMessage, AIMessage 等），是其它记忆组件的底层存储工具。特点：轻量、无需额外依赖、数据不会持久化（程序重启后丢失）。

```py
# 创建Memory
history = ChatMessageHistory()
# 添加用户消息
history.add_user_message("你好，我是RiskHelper")
# 添加AI消息
history.add_ai_message("我是DeepSeek大模型")
# 添加用户消息
history.add_user_message("我是谁？")
# 通过将history.messages 直接传入大模型，大模型能够看到完整的对话上下文，从而实现有记忆的连续对话
response = chat_model.invoke(history.messages)
print(response.content)
```

**2、ConversationBufferMemory**：是LangChain中最基础、最直接的记忆组件，**按顺序完整地记录用户与AI之间的每一轮对话**，并将这些历史信息提供给模型，以实现连贯的多轮对话。**他能保存全部的上下文信息，对话连贯性最强，但是会消耗大量的Token，且随着对话的进行，会占用大量的内存资源**。它适用于短对话，需要保存完整的上下文信息的复杂任务。

```py
prompt_template = PromptTemplate.from_template(
  "历史对话: {history}，当前问题: {input}"
)
memory = ConversationBufferMemory(memory_key="history")
chain = LLMChain(llm=chat_model, prompt=prompt_template, memory=memory)
response = chain.invoke({"input": "用简单一句话描述什么是LangChain？"})
print(response)
print("==============================================================")
response = chain.invoke({"input": "如何使用它？"})
print(response)
```

**3、ConversationBufferWindowMemory**：只保留最近 K 轮对话，解决ConversationBufferMemory占用内存多、tokken消耗大问题，平衡连贯性与资源消耗。随之带来的问题是会丢失早期上下文，不适合复杂场景。

```py
prompt_template = PromptTemplate.from_template(
  "历史对话: {history}，当前问题: {input}"
)
memory = ConversationBufferWindowMemory(memory_key="history", k=10) # k表示保留的对话轮数
chain = LLMChain(llm=chat_model, prompt=prompt_template, memory=memory)
response = chain.invoke({"input": "用简单一句话描述什么是LangChain？"})
print(response)
```

**4、ConversationSummaryMemory**：它是 LangChain 中一种智能的记忆管理组件，它通过自动生成对话摘要来解决长对话上下文管理的问题。与简单截断历史的记忆类型不同，**它使用大模型来提炼对话精髓，实现长期记忆**。核心思想是：**不存储原始对话记录，而是存储对话的智能摘要**。当新的对话发生时，系统会将现有摘要与最新对话结合，生成更新的摘要。

```py
history = ChatMessageHistory()
history.add_ai_message("你好，我是RiskHelper")
history.add_user_message("你好，什么是反洗钱？")
history.add_user_message("你好，什么是风控”")
memory = ConversationSummaryMemory.from_messages(llm=chat_model,chat_memory=history)
print(memory.load_memory_variables({}))
```

**5、ConversationSummaryBufferMemory**：是LangChain中一种非常实用的混合型记忆组件，它巧妙地**将对话摘要与原始对话缓冲区结合起来，旨在解决长对话场景下既要保持上下文连贯性又要控制资源消耗的核心矛盾**。其核心创新在于采用了分层记忆管理策略，在对话连贯性和资源消耗之间找到平衡点。

```py
memory = ConversationSummaryBufferMemory(
  llm=chat_model,
  max_token_limit=100,
  return_messages=True
)
memory.save_context({"input": "你好，我是RiskHelper"},{"output": "你好，我是DeepSeek大模型"})
memory.save_context({"input": "你好，什么是反洗钱？"},{"output": "反洗钱是指对洗钱犯罪的预防和打击措施"})
memory.save_context({"input": "你好，什么是风控？"},{"output": "风控是指对风险的控制措施"})
memory.save_context({"input": "你好，EDD？"},{"output": "EDD是指对客户身份的识别措施"})
print(memory.load_memory_variables({}))
```

注意： 有些模型（如Deepseek）没提供get_num_tokens_from_messages（计算token）函数，执行时会报错，解决办法继承ChatOpenAI，然后实现get_num_tokens_from_messages函数：

```py
#创建一个继承于ChatOpenAI的类，并实现get_num_tokens_from_messages方法
class DeepSeekChatOpenAI(ChatOpenAI):
    def get_num_tokens_from_messages(self, messages: List[BaseMessage]) -> int:
        #实现get_num_tokens_from_messages方法，返回消息的token数量
        total_tokens = 100 # 自定义函数
        return total_tokens;
chat_model = DeepSeekChatOpenAI(
    model_name=model_name,
    base_url=base_url,
    api_key=api_key
)
```

除了上述介绍的Memory之外，LangChain还提供了一下其他的Memory：

**6、ConversationEntityMemory**：一种基于实体的对话记忆机制，它能够智能地识别、存储和利用对话中出现的实体信息（如人名、地点、产品等）及其属性/关系，并结构化存储，使 AI 具备更强的上下文理解和记忆能力，能够解决信息过载问题。

**7、ConversationKGMemory**： 一种基于知识图谱的先进记忆组件，它将对话内容组织成结构化的知识图谱，实现更加语义化和关联性的记忆管理。

**8、VectorStoreRetrieverMemory**：一种基于向量数据库的先进记忆组件，它利用语义相似度检索技术，从大量历史对话中智能召回与当前对话最相关的记忆片段。

## 6. LangChain核心组件之Tools

### 6.1 Tools概述

Tools是大模型、智能体（Agent）与外部世界进行交互的核心组件，本质上是**封装了一些特定功能的可调用的函数（数学计算、获取时间、搜索等）**， 允许大模型与外部系统、API、数据源和工具进行交互，其核心价值在于极大地扩展了 LLM 应用的能力边界（动作）。

![Image](https://github.com/user-attachments/assets/484fb99e-e988-4f22-9cc6-76b45d907475)

![Image](https://github.com/user-attachments/assets/f8d37bec-199f-4a1d-a4fe-f7894e0d7ff9)

### 6.2 Tools组件使用

Tool 通常包含如下几个要素：

1. 工具名称 - 工具的唯一标识，通常是函数名，也可以自定义
2. 工具描述 - 工具的功能说明，应当清晰准确的描述工具的作用、使用场景等，直接决定Agent是否会使用这个工具
3. 参数定义 - 每个参数的名称、类型、描述、是否必需
4. 返回类型 - 工具返回的数据类型
5. 执行逻辑 - 实际的代码实现

使用流程：

![Image](https://github.com/user-attachments/assets/7bc47bdb-d106-4e9a-b1ce-1f413e90c78a)

STEP1-工具创建：使用@tool注解创建工具。

STEP2-工具绑定：将创建的工具绑定到Agent上（可以绑定多个），包括工具的名称、描述、参数、返回等，让Agent知道有哪些工具可用。

STEP3-工具调用：模型返回调用具体的工具后，Agent根据该工具的参数描述构造参数，并发起调用

STEP4-工具执行：执行工具的具体逻辑，并返回结果。

**两种自定义工具的方式：**

**方式一：@tool装饰器**

```py
@tool(
    # 指定工具的名称，这是 Agent 识别和调用工具时使用的标识符。
    # 默认值：如果不指定，默认使用函数名作为工具名称。
    name_or_callable="multiply",  #
    # 用于告诉 Agent 这个工具的功能和用途。这对 Agent 选择合适的工具至关重要。
    # 默认值：如果不指定，会尝试使用函数的文档字符串（docstring）作为描述。如果文档字符串也没有，则为空字符串。
    description="Multiply two numbers",
    # 控制工具执行结果的返回方式
    # True：工具的输出直接作为 Agent 的最终回答返回给用户
    # False：工具的输出会传递给 Agent，由 Agent 进一步处理或与其他信息结合后再回答
    return_direct=True
)
def multiply(a: int, b: int) -> int:
    """Multiply a and b."""  # 如果不指定description，这会成为工具描述
    return a * b
response = multiply.invoke({"a": 2, "b": 3})
print(response)
```

**方式二：StructuredTool的from_function()**

```py
multiply = StructuredTool.from_function(
    # 绑定函数
    func=multiply,
    # 指定工具的名称，这是 Agent 识别和调用工具时使用的标识符。
    name="multiply",
    # 用于告诉 Agent 这个工具的功能和用途。这对 Agent 选择合适的工具至关重要。
    description="Multiply two numbers"
)
response = multiply.invoke({"a": 2, "b": 3})
print(response)
```

**结合大模型使用：**

注意：tool一般与agent一起配合使用，agent在下一章详细介绍

```py
# 将函数封装为LangChain工具对象
stock_tool = Tool(
    name="getStockPrice",  # 工具名称（模型将使用此名称调用工具）
    func=getStockPrice,  # 工具函数
    description="用于查询公司股票价格。输入应该是公司名称（如：腾讯）"
    # 工具描述（模型根据此决定是否调用）
)
# 创建工具列表（Agent可以使用的所有工具）
tools = [stock_tool]
# 将LangChain工具转换为OpenAI函数调用格式
functools = [convert_to_openai_tool(tool) for tool in tools]
# 从LangChain Hub加载预定义的提示模板
# 'hwchase17/structured-chat-agent'是一个专门为结构化聊天Agent设计的提示模板
prompt = hub.pull('hwchase17/structured-chat-agent')
# 创建结构化聊天Agent，# 参数：模型对象、工具列表、提示模板
agent = create_structured_chat_agent(chat_model, tools, prompt)
# 创建Agent执行器
agent_executor = AgentExecutor(
    agent=agent,                # 配置好的Agent
    tools=tools,                # Agent可用的工具列表
    verbose=True,               # 开启详细日志（显示Agent思考过程）
    handle_parsing_errors=True  # 处理解析错误（防止因格式问题崩溃）
)
# 执行Agent查询
response = agent_executor.invoke({"input": "查询腾讯当日的股票价格"})
print(response)
```

注意：实际使用过程中发现，有时候并没有调用自定义的工具，可能原因是：

1. **大模型认为这个计算太简单（1+1），无需调用工具。**
2. **工具的描述不清楚，大模型无法推断出使用这个工具。**
3. **有些模型对于工具调用支持度不高，如DeepSeek-R1。**

**具体可通过修改工具描述、提示词或换一个模型等引导模型决定调用自定义工具。**

### 6.3 MCP Server

Agent tools可以看做是实现在AI Agent应用中的一系列函数，但是很多Tool 的功能比较通用，如浏览网页，发送消息，天气查询等，所以将通用的工具封装成一个单独的服务，就可以供不同的Agent使用了，这个单独的服务叫MCP Server，**其中MCP是一个通讯协议，用来规范Agent 和MCP Server之间是怎么交互的**，如工具的功能，描述，参数等。MCP Server可以与Agent部署在同一台机器上通过标准输入输出通讯，也可以部署在网络上通过http协议通讯。**虽然MCP是为了大模型而制定的标准，但实际上MCP本身和大模型没有关系，它并不关心Agent使用哪个模型，MCP只负责帮Agent管理工具、资源和提示词。**

LangChain中的Tool 和 MCP Server比较：

| 对比维度            | 内置Tools | MCP Server     |
|--------------------|------------------|-------------------------|
| 部署位置      | Agent 内部（同一进程）    | 独立服务（独立进程/网络） |
| 代码耦合      | 紧耦合（直接引用）        | 松耦合（协议通信） |
| 复用性      | 仅当前 Agent 可用       | 多个 Agent 可共享 |
| 更新维护      | 修改需重新编译 Agent    | 修改需重新编译 Agent 独立更新 |
| 性能         | 本地调用，极快    | 需要网络/IPC通信 |
| 适用场景      | 简单、专用工具    | 通用、复杂工具 |

## 7. LangChain核心组件之Agent

### 7.1 Agent（智能体）概述

![Image](https://github.com/user-attachments/assets/8df80891-6f0f-4a59-b3be-a90d91adc4e2)

Agent（智能体）是一个通过动态协调大模型（LLM）和 工具（Tools）来完成复杂任务的智能系统（思考、分析、拆解任务、逐步实现）。**它让大模型充当"决策大脑"，根据用户输入自主选择和执行工具**（如搜索、计算、数据库查询等），最终生成精准的响应。2025年也被称为Agent元年，**标志着人工智能正式从“思考与对话” 转向 “自主决策与行动”。**

一个Agent应该具备以下核心能力：

![Image](https://github.com/user-attachments/assets/3b2daa77-95c3-4e8a-9e94-192305396518)

1. **大模型（LLM）**：作为大脑，提供推理、知识理解和逻辑判断能力。
2. **记忆（Memory）**：用于保持Agent的上下文信息，具备短期记忆（上下文）和长期记忆（向量存储），支持快速知识检索。使交互具有连续性。
3. **工具（Tools）**：扩展Agent的能力边界，通过调用外部工具（搜索、数据库等）Agent可以获取实时信息，弥补自身的功能局限性。
4. **规划（Planning）**：将具体的任务分解，并具备反思和调整能力，当效果不符合预期时，能够重新规划方案并执行，确保目标最终实现。
5. **行动（Action）**：实际执行决策的能力。比如：检索、推理、编程
6. **协作（cooperation）**：单一的Agent功能通常是有限的，而复杂问题往往需要不同的专业知识的Agent协同解决，不同Agent之间通过A2A协议交互。

**通用人工智能（AGI）将是AI的终极形态（这天是否会到来？不止是技术的突破，还有伦理、法律、社会认知等）。同样，构建智能体（Agent）则是AI工程应用当下的“终极形态”。**

![Image](https://github.com/user-attachments/assets/3ea4a9a7-b623-45bc-9cb8-9cad52e8483c)

### 7.2 Agent Types（运行模式）

在 LangChain 中，Agent是一种由大模型驱动的组件，能够根据用户的输入动态决定执行哪些操作（如调用工具、访问数据等）。不同的 Agent 类型对应不同的决策逻辑和策略，适用于不同的任务场景。

**1、Function Calling**：是让LLM学会“使用工具”。预先定义好一系列函数（如get_weather， search_web），并明确描述它们的功能和参数。当用户提问时，将用户提问以及工具信息（工具描述、参数等关键信息）一起发送给大模型，**大模型不直接生成答案，而是判断是否需要调用这些函数，以及如何调用，并将函数参数，函数名等信息以结构化的格式返回**（如JSON格式）。

a. 关键点：LLM本身不执行函数代码，它只负责规划。实际执行由Agent完成 。
b. 应用场景：适合用于获取实时数据（天气、股价）、查询数据库、执行计算或调用任何外部API等

```py
"""
OpenAI函数调用Agent
特点：1、使用OpenAI的函数调用功能，支持结构化工具调用 2、性能最佳，响应速度快，工具调用准确性高 3、支持并行工具调用，可同时执行多个工具 4、原生支持JSON格式的参数传递
适用场景：1、需要高精度工具调用的应用 2、对响应速度有要求的生产环境 3、需要复杂参数传递的工具集成 4、推荐作为首选Agent类型
"""
AgentType.OPENAI_FUNCTIONS

"""
OpenAI多函数调用Agent - 高级功能
特点：1、支持复杂的多步骤工具调用链 2、能够处理工具间的依赖关系 3、支持条件分支和循环调用 4、具备更强的推理和规划能力
适用场景： 1、需要多步骤复杂任务处理 2、工具间存在依赖关系的场景 3、需要动态决策和分支处理 4、复杂的业务流程自动化
"""
AgentType.OPENAIMULTI_FUNCTIONS
```

**2、ReAct模式（思考与行动）**

![Image](https://github.com/user-attachments/assets/68844d0f-c9be-410e-ac73-3206122fa3f2)

将复杂任务拆解成，**Thought (推理) → Action (行动，即调用工具) → Observation (观察结果) → ... → Final Answer**（**最终答案**）的循环步骤：


a. **Thought（思考）**：大模型首先会分析当前情况，并思考下一步应该做什么以及为什么这么做。

b. **Action（行动）**：根据思考的结果，判断是否需要调用工具，以及调用哪个工具，并生成相应的调用参数，发起工具调用。

c. **Observation（观察）**：查看工具执行的结果，判断是否成功等。如调用写文件工具，会判断文件是否写入成功。

d. **Final Answer（最终答案）**：如果任务已经成功或达到执行次数，则会生成最终答案，否则继续进行“思考-行动-观察”循环步骤。

ReAct的工作原理：**大模型本身不具备此流程（Thought-Action-Observation），实际是根据预先设定好的系统提示词来执行上述步骤的**，如下是一个ReAct的系统提示词（hwchase17/react）：

![Image](https://github.com/user-attachments/assets/38bc5944-fff0-46b6-b6f8-4a56b8c615d3)

LangChain中ReAct的类型：

```py
"""
零样本ReAct描述Agent
特点：1、基于ReAct（Reasoning + Acting）模式 2、通过"思考-行动-观察"的循环进行推理 3、不需要示例，完全依赖工具描述 4、具有良好的可解释性和透明度
适用场景：1、需要清晰推理过程的应用 2、工具描述详细且准确的场景 3、对可解释性有要求的业务场景 4、适合调试和问题排查
"""
AgentType.ZERO_SHOT_REACT_DESCRIPTION

"""
结构化聊天零样本ReAct Agent
特点：1、结合了结构化输出和ReAct推理模式 2、支持更复杂的工具参数传递 3、具备更好的多轮对话能力 4、输出格式更加规范和可解析
适用场景：1、需要结构化输出的聊天应用 2、复杂参数的工具调用场景 3、多轮对话中的工具使用 4、需要格式化响应的业务场景
"""
AgentType.STRUCTURED_CHAT_ZERO_SHOT_REACT_DESCRIPTION

"""
对话式ReAct Agent
特点：1、具备对话记忆能力，能记住历史交互 2、基于ReAct模式进行推理和行动 3、支持上下文相关的工具调用 4、适合长期对话和任务跟踪
适用场景：1、需要维护对话状态的应用 2、长期任务跟踪和管理 3、个性化服务和推荐 4、客户服务和技术支持场景
"""
AgentType.CONVERSATIONAL_REACT_DESCRIPTION
```

### 7.3 ReAct Agent 结合大模型使用

```py

# 创建Tavily搜索工具实例，用于为Agent提供实时网络搜索能力
# 需要在https://tavily.com/ 申请API key 才能使用
search_tool = TavilySearchResults(
    max_results=3,
    description="用于搜索最新网页信息、新闻和历史数据。输入应该是明确的搜索查询。"
)
# 从LangChain Hub拉取预定义的ReAct提示词模板
prompt = hub.pull("hwchase17/react")
# 创建ReAct Agent实例
agent = create_react_agent(
    llm=chat_model,
    prompt=prompt,
    tools=[search_tool]
)
# 创建Agent执行器
agent_executor = AgentExecutor(
    agent=agent,
    handle_parsing_errors=True,  # 自动处理解析错误，提高稳定性
    verbose=True,                # 开启详细日志，显示推理过程
    tools=[search_tool]          # 工具列表，必须与Agent中的工具保持一致
)
# 执行Agent任务
response = agent_executor.invoke( {"input": "2021年2月5日腾讯收盘价是多少？"})
print(response)
```

![Image](https://github.com/user-attachments/assets/f32433ce-4ddc-4182-845f-879134d7b83a)

### 7.4 FUNCTION_CALL 结合大模型、Memory 使用

```py
# 创建Tavily搜索工具实例
search_tool = TavilySearchResults(
    max_results=3, # 限制搜索结果数量，平衡信息完整性和处理效率
    description="用于搜索最新网页信息、新闻和历史数据。输入应该是明确的搜索查询。"
)
# 定义工具列表，可以添加多个
tools = [search_tool]
# 创建提示词模板
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个人工智能小助手，可以回答问题并使用工具。"),
    ("placeholder", "{chat_history}"),
    ("human", "{input}"),
    ("placeholder", "{agent_scratchpad}")
])
# 创建对话缓冲记忆实例
memory = ConversationBufferMemory(
    memory_key="chat_history",
    return_messages=True
)
# 创建工具调用Agent实例
agent = create_tool_calling_agent(chat_model, tools, prompt)
# 创建Agent执行器
agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    memory=memory,
    handle_parsing_errors=True,
    verbose=True
)
response = agent_executor.invoke({"input": "2021年2月5日腾讯收盘价是多少？"})
print(response)
response = agent_executor.invoke({"input": "阿里呢？"})
# 嵌入Memory，可以推断出问的股价，
print(response)
```

![Image](https://github.com/user-attachments/assets/f935b6ba-b759-4029-b2c4-62c2660e1e02)

## 8. LangChain核心组件之Retrieval

### 8.1 Retrieval（检索）概述

Retrieval是用于从大量的文档或数据源中查找相关信息的核心模块，它是构建RAG（检索增强生成）的基础，能够让大模型访问外部知识源，解决模型本身的知识局限性和时效性问题。

### 8.2 大模型幻觉

我们知道大模型有两个主要的特点：

1. **知识冻结**：大模型在训练完成后，**其内部知识就"冻结"在训练时的状态**，并且无法获取或学习训练数据截止日期之后的新信息。
2. **大模型幻觉**：生成看似合理但实际错误、虚构或不存在的信息的现象（例如：9.11 > 9.9）。

虽然记忆机制扩展了AI工程的应用场景，但大模型在专业领域仍面临显著挑战，由于无法掌握全部专业知识，**大模型在回答专业问题时可能生成不准确甚至完全错误的内容**，这个现象被称为“幻觉”。尤其在金融、医疗等高要求领域，一次错误的金额评估或医疗诊断失误都是致命的，对于非专业人士来说可能难以辨识。当前还没彻底解决这个问题的方案，不过大家普遍达成共识的一个方案：

1. 为大模型提供一定的上下文信息，让其输出会变得更稳定。
2. 利用RAG（检索增强生成）技术，将专业领域的知识检索出来和提示词一起发送给大模型，以便模型能生成更可靠的答案。

### 8.3 RAG解决方案

在利用大模型处理特定领域的大规模知识问答时，除了微调模型之外，检索增强生成（RAG）是一个有效的缓解大模型幻觉问题的解决方案。**RAG通过引入外部知识检索机制，在生成前为模型提供相关的实时、专有信息作为上下文，从而直接应对其固有的“幻觉”问题和知识更新瓶颈。**

**RAG流程：**

![Image](https://github.com/user-attachments/assets/e88c599c-52b9-4a5e-8ff1-5e6e2aad5210)

![Image](https://github.com/user-attachments/assets/63e82ea6-3935-48b6-8998-c6e9f7dae2d2)

1、文件解析：解析各种格式的文件并提取出文字。
2、文件切割：把长文本切割成更小的、有逻辑的片段。
3、向量化：将文字片段转换为高纬空间中的向量数字。
4、知识入库：将这些向量数字存进向量数据库中
5、用户提问：用户发起提问
**6、检索**：基于各种算法（余弦相似度、欧氏距离、点积等）去向量数据库里快速找出和问题最相关的几个文本片段。
7、知识重排序：对找出的片段进行梳理排序，找出相似度最高的。
**8、增强**：把选好的知识片段和用户的问题打包在一起交给大模型。
**9、生成**：大模型生成一个准确且相关的回答。

其中第689是RAG步骤中的检索、增强、生成。这里有三个位置涉及到大模型的使用：

第3步：向量化，需要使用EmbeddingModels。

第7步：重排序，需要使用RerankModels（追求回答高精度和高相关性的场景）。

第9步：生成答案，需要使用LLM。

**RAG的优点**

1. **知识实时更新**：只需更新知识库，即可让模型获取最新信息，解决知识冻结问题
2. **成本效益高**：相比微调，仅需构建知识库，成本低，且避免了微调可能带来的“灾难性遗忘”问题
3. **可信度高，可溯源**：答案有据可查，可提供引用来源，有效减少模型“幻觉”。
4. **安全可控**：可限制模型只基于提供的知识回答，避免泄露敏感信息或产生有害内容。


**RAG的缺点**

1. **检索质量是瓶颈**：效果高度依赖于检索器的准确性。不相关的检索结果会直接导致回答错误。
2. **系统复杂性高**：需维护检索、向量数据库等多个组件，比直接调用API复杂得多。
3. **上下文窗口受限**：检索内容会消耗宝贵的上下文窗口，可能挤占大模型的推理空间。
4. **对知识库质量极度敏感**：如果外部知识库本身质量差（内容错误、格式混乱、信息过时），那么RAG系统的输出质量也必然无法保证。

### 8.4 Retrieval流程

![Image](https://github.com/user-attachments/assets/8a0a6147-cc68-43fd-a970-568b16cee1c3)


**阶段一：Source（数据源）**，外部知识库，包含各种类型、格式的文件，如图片、视频、网站等

**阶段二：Load（加载）**，将外部知识库加载到内存中并转换成文档(Document)对象。包含内容和元数据等相关信息。

**阶段三：Transform（转换）**，将文档对象转换为更适合检索和生成的表达形式。包括文本分块、清理冗余信息、关键信息提取等，旨在提升后续处理的效率与质量

**阶段四：Embed（嵌入）**，通过嵌入模型将文本转换为高纬度空间中的数值向量，使得计算机能够理解和处理语义信息，文本可用于向量空间中的各种运算，大大拓展了文本分析的可能性，是自然语言处理领域非常重要的技术。

a. 实现原理：通过特定算法 （如Word2Vec）将语义信息编码为固定维度的向量。

b. 关键特性：相似的词在向量空间中距离相近，例如“猫”和“狗”的向量夹角小于“猫”和“汽车”。

![Image](https://github.com/user-attachments/assets/c8de225e-4783-49d7-b277-4dc8fd592e30)

文本嵌入为 LangChain 中的问答、检索、推荐等功能提供了重要支持。具体为：

1. 语义匹配：通过计算两个文本的向量余弦相似度，判断它们在语义上的相似程度，实现语义匹配。
2. 文本检索：通过计算不同文本之间的向量相似度，可以实现语义搜索，找到向量空间中最相似的文本。
3. 信息推荐：根据用户的历史记录或兴趣嵌入生成用户向量，计算不同信息的向量与用户向量的相似度，推荐相似的信息。
4. 知识挖掘：可以通过聚类、降维等手段分析文本向量的分布，发现文本之间的潜在关联，挖掘知识。
5. 自然语言处理：将词语、句子等表示为稠密向量，为神经网络等下游任务提供输入。

**阶段五：Store（存储）**，将转换后的向量存储在向量数据库中，避免需要时重新计算。

![Image](https://github.com/user-attachments/assets/690fccaf-085e-4693-b9e9-c17c807c8ccb)

### 8.5 Retrieval组件使用

**1、Document Loaders（文档加载器）**

```py
# 加载txt文件
text_loader = TextLoader("./risk.txt")
docs = text_loader.load()
# print(docs)

# 加载pdf文件
pdf_loader = PyPDFLoader(file_path="./risk.pdf")
docs = pdf_loader.load()
print(docs)

# 加载csv文件
csv_loader = CSVLoader(file_path="./risk.csv")
docs = csv_loader.load()
print(docs)

#加载json文件
json_loader = JSONLoader(file_path="./risk.json")
docs = json_loader.load()
print(docs)
```

Documment对象中有两个重要的属性：

page_content：真正的文档内容

metadata：文档内容的元数据

**2、Text Splitters**（**文本拆分器**）

![Image](https://github.com/user-attachments/assets/677bd5fa-a700-433d-a7d2-90b609143fa6)

文本拆分器是将长文档拆分为语义连贯、大小适中的小片段，**主要是解决大模型的上下文窗口限制，并提升检索的准确性和生成内容的质量**。它的工作原理通常遵循“先细拆后合并”的策略：首先将文本分割成小句子，然后按顺序将这些小句子合并成较大的块，直到达到设定的块大小限制。在创建新块时，会与上一个块保留部分重叠，以确保上下文连贯。

为什么需要分隔？

a. **保证生成答案质量**：如果检索到的文本块过大且包含大量无关信息，LLM 可能会被无关内容干扰，无法聚焦于核心问题，甚至可能将无关信息错误地整合进答案，导致生成内容不准确或冗长。

b. **突破模型上下文窗口限制**：所有大模型都有一个固定的上下文窗口，即模型能一次性“看到”并处理的文本总量是有限的。

c. **提升检索的精准度**：检索系统可以直接定位到专门回答该问题的段落，极大地提升了检索结果的相关性和准确性。

基于此，一个有效的解决方案就是将完整的Document对象进行分块处理（Chunking) 。无论是在存储还是检索过程中，都将以这些块(chunks) 为基本单位，这样有效地避免内容不相关性问题和超出最大输入限制的问题。

Chunking拆分的策略：

1. 固定长度切分：严格按照预设的固定字符数或 Token 数进行切分，是最简单直接的方法。
2. 递归切分：采用分层分隔符策略，优先使用大粒度分隔符切分，若不满足大小要求，则逐级使用更小粒度的分隔符递归切分，直到达到目标块大小。默认分隔符顺序为 ["\n\n", "\n", "。", "，", " ", ""]，它会优先按段落、句子、标点进行切分，能有效保留句子和段落的完整性
3. 基于文档结构的切分：利用文档固有的格式和结构进行切分，如 Markdown 的标题、HTML 的标签、代码文件的函数/类定义等。
4. 语义分块：它利用嵌入模型计算句子或段落的向量表示，然后通过分析向量间的相似度来动态确定切分点，将语义相近的内容聚合到同一个块中，旨在保持相关信息的集中和完整。

```py
text_loader = TextLoader("./risk.txt")
docs = text_loader.load()
text_splitter = CharacterTextSplitter(
    # 文本块大小：每个分割块的最大字符数，1000字符通常包含150-200个中文词汇，适合大多数LLM的上下文窗口处理
    chunk_size=1000,
    # 文本块重叠：相邻块之间的重叠字符数，设置为0表示无重叠，节省存储空间，通常设置为chunk_size的10-20%以保持上下文连续性
    chunk_overlap=0,
    # 长度计算函数：用于计算文本长度的函数，len()函数按字符计数，适合中英文混合文本，也可以使用token计数函数获得更精确的控制
    length_function=len,
    # 分割符：优先按换行符分割文本，保持段落结构的完整性，避免句子被截断
    separator="\n"
)
texts = text_splitter.split_text(docs[0].page_content)
print(texts)
```

除了CharacterTextSplitter，LangChain还提供了很多拆分器，如：RecursiveCharacterTextSplitter、TokenTextSplitter、CharacterTextSplitter、SemanticChunker、HTMLHeaderTextSplitter等。具体使用可参考官方文档：https://python.langchain.com.cn/docs/modules/data_connection/document_transformers/

**3、Text Embedding Models（文档嵌入模型）**

将文本转换为数值向量

![Image](https://github.com/user-attachments/assets/a757a0f1-d4ce-416b-9dbb-882d96605f1c)

```py
embeddings_model = OllamaEmbeddings(
    model="nomic-embed-text",
    base_url="http://127.0.0.1:11434",
)
text = "Hello World"
# 句子向量化
vector = embeddings_model.embed_query(text=text)
print(f"嵌入向量长度: {len(vector)}")
print(f"前20个值: {vector[:20]}")

print("=======================================")

texts = ["Hello World", "Today is a sunny day", "No news is good news"]
# 文档向量化
vector = embeddings_model.embed_documents(texts)
for v in vector:
  print(f"前20个值: {v[:20]}")
```

![Image](https://github.com/user-attachments/assets/4678f358-5e24-4988-a8cc-df5376ec4793)

**4、Vector Stores（向量存储）**

![Image](https://github.com/user-attachments/assets/3e96a47f-4bf8-4f94-a919-0b99012a2558)

将文本向量化之后，下一步就是进行向量的存储。这里有两部分：

a. **向量的存储** ：将非结构化数据向量化后，存储在向量数据库。

b. **向量的查询** ：查询时，嵌入非结构化查询并检索与嵌入查询“最相似”的嵌入向量。即具有相似性检索能力。

LangChain提供了50多种不同的向量数据库，参考文档：向量存储

https://docs.langchain.com/oss/python/langchain/overview

```py
# 加载txt文件
text_loader = TextLoader("./risk.txt", encoding="utf-8")
docs = text_loader.load()
# 文本分割
text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=0)
text_docs = text_splitter.split_documents(docs)
# 向量存储，默认存储在内存中， 可通过persistent_directory参数指定存储磁盘路径
vectorstore = Chroma.from_documents(text_docs, embeddings_model, persistent_directory='./chroma_db')
# 相似度搜索，除了similarity_search外，
# 1、直接对问题向量查询（similarity_search_by_vector）
# 2、通过L2距离分数进行搜索（similarity_search_with_score）
# 3、通过余弦相似度分数进行搜索（similarity_search_with_relevance_scores）
# 4、MMR（最大边际相关性，max_marginal_relevance_search）
response = vectorstore.similarity_search("什么是反洗钱？")
print(response)
```

**5、Retrievers（检索器）**

![Image](https://github.com/user-attachments/assets/45a40b33-f61d-4899-a93d-7f9ddf906d20)

向量数据库提供了核心的相似性计算能力，其内置函数（如余弦相似度，欧式距离，点积等）可直接用于实现基础的向量召回。LangChain还提供了 更加复杂的召回策略 ，这些策略被集成在Retrievers（检索器）组件中。检索器本身不存储数据，而是通过查询向量数据库，并集成重排序、多路检索等高级逻辑，最终返回相关的文档片段。

```py
# # 加载txt文件
text_loader = TextLoader("./risk.txt", encoding="utf-8")
docs = text_loader.load()
text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=0)
text_docs = text_splitter.split_documents(docs)
# 向量存储，默认存储在内存中， 可通过persistent_directory参数指定存储磁盘路径
vectorstore = Chroma.from_documents(docs, embeddings_model, persistent_directory='./chroma_db')
# 创建检索器
retriever = vectorstore.as_retriever(
    # 搜索参数，k表示返回的文档数量，score_threshold表示相似度阈值
    search_kwargs={"k": 2, "score_threshold": 0.5},
    # 搜索类型
    search_type="similarity_score_threshold"
)
chain = retriever | chat_model
response = chain.invoke("什么是反洗钱？")
print(response)
```

## 9. 再谈LangChain

到这想必对LangChain有一个清晰的认识了，为了更形象地理解，如果将LLM比作人类的”大脑“，那么LangChain中的组件就像：


1. **LLM（大语言模型） ——  大脑**

整个系统的核心，负责思考、推理、理解和生成。就像人类的大脑，是智能的中枢。它接收来自各方的信息，进行处理，并做出决策或生成回应。

2. **RAG & VectorStore（检索增强生成与向量数据库） ——  图书馆/长期记忆**

存储海量的专业知识（公司文档、知识库等），并能在需要时快速、准确地检索相关信息提供给“大脑”。它是一个巨大的私人图书馆或长期记忆仓库，当你需要解决一个专业问题时，你会来图书馆查阅相关资料，然后将这些资料带给大脑（LLM）进行参考和分析。

3. **Agents（智能体） ——  神经系统/小脑**

负责决策“如何”完成任务。它接收用户指令，进行规划，决定是直接由大脑（LLM）回答，还是需要调用各种工具（Tools），并按照什么顺序来执行。就像人体的神经系统或小脑。大脑（LLM）负责出主意，而Agent负责协调身体各部分去执行这个主意。例如，你想“拿一杯水”，大脑发出指令，神经系统（Agent）会规划并协调眼睛（观察）、手（抓取）等一系列动作。

4. **Tools（工具） ——  手、脚和感官**

是Agent可以调用的具体功能，用于与外部世界交互。就像人的手、脚、眼睛和耳朵。

5. **Chains（链） ——  技能或肌肉记忆**

将多个步骤（LLM调用、工具使用、数据处理）预先定义并链接在一起，形成一个可重复执行的固定流程。像是你学会的一种“技能”或“肌肉记忆”。例如，“泡咖啡”这个技能就是一个链：走到咖啡机前 -> 加咖啡粉 -> 按开关 -> 等待 -> 拿杯子。一旦学会，你就可以不假思索地完成。

6. **Memory（记忆） ——  短期记忆/对话记忆**

用于存储和回顾当前对话的历史信息，使AI能够拥有上下文感知能力，实现连贯的多轮对话。就像你的短期记忆。它让你记得在刚才的对话中对方说了什么，从而能做出相关的回应。没有记忆，每一次对话都是全新的、孤立的。

7. **PromptTemplates（提示模板） ——  沟通技巧/提问模板**

预先设计好的、结构化的提示词，用于更有效、更稳定地向LLM提问。就像沟通技能、演讲技能，写作技能，都有一套固定的模版。

通过这个比喻，我们可以清晰地看到：**LLM是智能的核心（脑）、RAG是知识储备（图书馆）、Agents是协调指挥官（神经系统）、Tools是执行手段（手脚）、Chains是自动化技能（肌肉记忆）、Memory是上下文感知（短期记忆）**，它们各司其职，紧密协作，共同构成了一个能够理解、推理并作用于外部世界的智能体。


### LangChain相关资料地址如下：

LangChain 官网地址： https://www.langchain.com/langchain
LangChain 官网文档： https://python.langchain.com/docs/introduction/
LangChan API文档： https://python.langchain.com/api_reference/
LangChain github地址： https://github.com/langchain-ai/langchain
LangChain中文网： https://python.langchain.com.cn/docs/
Open AI中文文档： https://openai.xiniushu.com/