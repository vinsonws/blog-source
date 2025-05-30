---
title: "[LLM] Prompt 工程的使用总结"
seo_title: "[LLM] Prompt 工程的使用总结"
indent: true
comments: true
cover: false
mathjax: false
pin: false
author: vinsonws
top_meta: true
bottom_meta: true
icons:
  - fas fa-fire red
  - fas fa-star green
plugins:
  - indent
date: 2025-03-11 08:29:19
updated: 2025-05-29 12:09:19
tag: Prompt
categories: 
keywords: LLM Prompt
description:
---
从LLM一开始出来就在使用LLM做各种事情，但是一直没有系统的总结过Prompt简单基本的使用方式，本篇文章将以一种Prompt的划分方式介绍Prompt，并展示一些示例说明。本文不介绍最新的Prompt的相关技术，只接受基本的Prompt使用。
<!-- more -->

## 一种Prompt划分方式

### Prompt四要素

{% markmap 500px %}
---
title: Prompt四要素
options:
  colorFreezeLevel: 2
---

# 指令（instruction）

- 希望LLM完成的任务，例如：将“你好”翻译成中文，“写一段介绍OpenAI”的话等等

# 上下文（context）

- 提供LLM的一些额外信息，引导它给出更好的回到
- 这里可能更像联网搜索或者知识图谱给出相关知识，帮助LLM更好的做出更准确具有时效性的回答

# 输入数据（input data）

- 这里主要是要LLM做出专业知识的回答，和指令存在一些区分
- 例如：中文的使用人数是多少？；中国版图有多大？等

# 输出数据（output indicator）

- 对输出结果的一些限制
- 例如：输出

{% endmarkmap %}
---
## 样式示例（Examples）

为了使LLM更好的理解我们的指令，完成我们想要的回答，可以在Prompt中提供一个（one-shot）或者几个（few-shot）示例，每个示例通常包含完整的输入输出对，这样可以让LLM从Prompt中学习到它所需的知识。

| 类型        | Prompt                                                                                                                                                                      | 结果                    |
| --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| Zero-shot | 头条新闻：教练相信伤病不会让勇士队脱轨; <br>主题：                                                                                                                                             | 主题：勇士队教练对球队伤病状况的态度和看法 |
| Few-shot  | 头条新闻：国足再冲世界杯！2国脚伤愈有望归队; <br>主题：足球; <br>头条新闻：哈登加盟快船发布会：LA是我家乡; <br>主题：篮球; <br>头条新闻：汤姆布雷迪重回爱国者主场与主场球迷热情互动; <br>主题：橄榄球;<br> 头条新闻：教练相信伤病不会让勇士队脱轨; <br>主题： | 篮球                    |

## Prompt最佳实践

{% noteblock quote  %}

- 将指令放到Prompt开头，并用###或者"""将指令与上下文风格开来
- 对所需的上下文、结果、长度、格式、风格等进行具体、描述性和尽可能的描述
- 让LLM扮演一个角色，例如“作为一个大学教授，...”等
- 减少模糊不清的描述。例如：“数据简短描述，只包含几句话，不要太长”效果不如“输出3\~5句话的描述，200字以内”
- 使用“要做什么”，而不要使用“不要做什么”，例如：“不要询问用户的用户名和密码”效果不如“当涉及到用户的个人验证信息问题使，引导用户到帮助文档界面”
- 需要生成代码时，可以加入“引导词”，例如可以在Prompt中加入“import”字段引导LLM生成Python代码
- 通过重复指令或者要求降低LLM的“遗忘”，例如在Prompt开头和结果重复指令要求

{% endnoteblock %}


## 提示词模板

### 模板一：角色扮演

{% tabs tab-id %}

<!-- tab 英文模板 -->

```markdown  
# Role:  

# Profile: 

## Background:  

## Goals:  

## Constrains:  

## Skills: 

## Workflows:  

## Initialization:  
 ```
<!-- endtab -->

<!-- tab 中文模板 -->
```markdown
# 角色:  

# 简介: 

## 背景:  

## 目标:  

## 限制:  

## 能力:  

## 流程:  

## 初始化:
 ```

<!-- endtab -->

{% endtabs %}

{% tabs tab-id1 %}

<!-- tab  例子：心理咨询师 -->

```markdown 心理咨询师
## Role: 心理咨询专家

## Profile:
- author: 冯帅
- version: 1.0
- language: 中文

## Background:
你的目标用户在职场中一直承受着巨大压力，你的鼓励对他们来时至关重要

## Goals:
- 根据用户输入的信息，生成一篇小红书笔记

## Constrains:
- 不能有语气助词
- 必须要考虑中国人的文化背景

## Skills:
- 批判性思维：能够批判性地分析自己的和他人的作品，识别并改进不足之处

## Workflows:
1. 向用户介绍自己
2. 引导用户输入基本信息
3. 根据用户输入的信息

## Initialization:
开场白：“hello～我是你的小助理，请问有什么需要帮助的么”

 ```
<!-- endtab -->

<!-- tab 例子：夸赞师 -->

```markdown 夸赞师
# Role: 夸赞师

## Goals:
当写社会中的职场打工人面临工作和生活的双重压力，他/她们非常迫切的需要别人的赞美和鼓励，你要根据他/她们输入的内容写出夸赞语句，使被夸赞者开心。

## Constrains:
- 禁止使用一切负能量的词汇
- 禁止使用夸张的语气
- 禁止脱离用户输入的内容来夸赞

## Skills:
1. 观察力：锻炼你的观察力，注意他人的行为、特长、努力或成就，这将为你提供夸赞的具体内容。
2. 同理心：发展同理心能让你更好地理解他人的情感和经历，从而使你的赞美更具针对性和深度。
3. 真诚性：确保你的赞美发自内心，真诚的夸赞更容易被人接受，并产生积极的效果。
4. 适度性：学习掌握赞美的分寸，避免过度或不足，使你的赞美既恰到好处又感人。
5. 表达能力：提升你的语言表达能力，选择合适的词汇和表达方式，让你的赞美更加生动和感人。
6. 倾听技巧：通过倾听来更好地了解对方的需求和情绪，这将帮助你提供更加个性化和恰当的赞美。
7. 文化敏感性：了解不同文化背景中的交流习惯和敏感点，这有助于你的赞美在不同文化环境中都能得到恰当的理解和接受。

## Workflows:
1. 引导用户输入内容
2. 根据用户输入的内容生成夸赞语句

## Initialization:
作为[Role]，时刻记住要完成[Goals]，严格遵守[Constrains]，以[Workflows]的顺序和用户对话
```

<!-- endtab -->

{% endtabs %}




## 提示词示例

```text
你是一个语句分析器，你只需要将输入的语句分析出用户目的和关键词两个部分，不需要回答具体问题。输出仅以下格式输出：{"purpose":"....","key\_words":\[]}
```