---
fonts:
  mono: 'LXGWWenKaiGBScreenR'
  serif: 'LXGWWenKaiGBScreenR'
  sans: 'LXGWWenKaiGBScreenR'
  local: 'LXGWWenKaiGBScreenR'
layout: cover

image: https://source.unsplash.com/collection/94734566/1920x1080

---

# ChatGPT食用指南

ChatGPT对接和使用技巧

<div class="uppercase text-sm tracking-widest">
增长后端 王若愚
</div>

<div class="abs-bl mx-14 my-12 flex">
  <img src="/public/chatgpt.svg" class="h-8">
  <div class="ml-3 flex flex-col text-left">
    <div><b>Share</b>Day</div>
    <div class="text-sm opacity-50">Mar. 30th, 2023</div>
  </div>
</div>


---
layout: center

class: text-center
---

# 1、对接ChatGPT

---

# [Chat API](https://platform.openai.com/docs/api-reference/chat/create)
<div class="grid grid-cols-2 gap-x-4">

<div class="mt-8 col-span-1">

### 接口请求

```shell
# 请求
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "model": "gpt-3.5-turbo",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'

```

### 参数
```json
{
  "model": "gpt-3.5-turbo", # 选用的模型
  "messages": [{"role": "user", "content": "Hello!"}]  # 传入的内容
}
```
</div>
<div class="mt-8 col-span-1">

### 接口响应
```json
{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "created": 1677652288,
  "choices": [{
    "index": 0,
    "message": {
      "role": "assistant",
      "content": "\n\nHello there, how may I assist you today?",
    },
    "finish_reason": "stop"
  }],
  "usage": {
    "prompt_tokens": 9,
    "completion_tokens": 12,
    "total_tokens": 21
  }
}
```
</div>
</div>

---
clicks: 2
---

# 获取 [API_KEY](https://platform.openai.com/)
<div class="grid grid-cols-6 h-screen">

<div class="mt-8 col-span-3 h-screen">
  <img style="width: 60%" src="/public/login.png">
</div>
<div class="mt-8 col-span-3 gap-y-4 h-screen">
    <v-click at="0">
    <img style="position:absolute; width: 45%"  src="/public/person.png">
    </v-click>
    <v-click at="1">
    <img style="position:absolute; width: 45%"  src="/public/api_key.png">
    </v-click>
</div>
</div>

---

# 其他常用参数


|  参数名   | 说明  |
|  ----  | ----  |
| temperature  | 值范围[0,2], 值越高回答越随机 |
| top_p  | 从token的角度限制概率，给0.1的表示仅考虑包含前10%概率的token |
| stream  | 流传输，设置为true的话，就是生成token就传输，不用等到生成完成再传输 |
| max_tokens  | 生成的最大令tokens数 |

<br />
<br />

### tips:

1、tokens是GPT理解语义的单位，总的算下来一个汉字相当于2个token，英文的话1个token相当于0.74个单词
2、ChatGPT3.5限制模型限制对话最多只能4096个token（GPT4强大的一点的是最大支持32768tokens）

---
layout: center

class: text-center
---

# 2、突破token限制 

---

# 看看这个限制

<br/>

我们可以简单计算一个汉字相当于2个token, 那么我们实际上只能使用2048个汉字，这要包括ChatGPT生成的token。

你可能觉得这么多汉字在日常对话中够用了，那是因为ChatGPT已经学习到内容就足够应对我们的日常对话了。

但是ChatGPT对于2021年出9月现的理论，企业的数据这都是学习不到的，比如你问一下得物有多少人，他会给你不准的答案，这样除了对话就不能运用到其他领域了

<v-click>
<img style="width:60%;margin:auto" src="/public/error_ans.png">
 </v-click>

---

# 突破限制-上下文提醒

<br/>
<br/>
<br/>
<br/>

<img style="width:60%;margin:auto" src="/public/right_ans.png">
<div style="text-align: center; margin-top: 20px;">
  只要我们在提问的时候带上一些知识，ChatGPT就是能根据提供的知识，给出我们想问的答案。
</div>

---

# 突破限制-字典查询
<br/>
<br/>
新华典大家都知道，整体汉字排布是根据拼音的顺序排版的，我们可以使用拼音检索，也可以根据偏旁部首检索。

现在我们把每个第一个声母当成一篇文档，把我们检索的问题当成部首，这样我们就能通过问题来先找到我们需要的文档。

然后我们问ChatGPT的时候带着文档，就可以先让ChatGPT学习文档，我们来提问。只要我们把文档的粒度切分合适，那么我们就能发会ChatGPT的价值。

---

# 突破限制-词向量
<br/>
<br/>
在机器学习的过程中，科学家发明了一种计算可以理解的，并且可够算出词和词之间距离的一种方法--词向量。

集体来说就是把词转化成多维向量，通过计算向量间的距离可以分别两词的关联程度。

<br/>
<br/>
<img style="width:100%;margin:auto" src="/public/text_embedding.png">

---

# 词向量接口

<div class="grid grid-cols-2 gap-x-4">

<div class="mt-8 col-span-1">

### 接口请求

```shell
# 请求
curl https://api.openai.com/v1/embeddings \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "input": "Your text string goes here",
    "model": "text-embedding-ada-002"
  }'

```

### 参数
```json
{
    "input": "Your text string goes here", # 输入文本
    "model": "text-embedding-ada-002" # 选用模型
}
```
</div>
<div class="mt-8 col-span-1">

### 接口响应
```json
{
  "data": [
    {
      "embedding": [
        -0.006929283495992422,
        -0.005336422007530928,
        -4.547132266452536e-05,
        -0.024047505110502243
      ],
      "index": 0,
      "object": "embedding"
    }
  ],
  "model": "text-embedding-ada-002",
  "object": "list",
  "usage": {
    "prompt_tokens": 5,
    "total_tokens": 5
  }
}
```
</div>
</div>

---

# 使用流程

### 1、生成向量库
<br/>
<br/>
<img src="/public/get_embedding.svg" style="width:100%;height:20%" class="h-8">

### 2、数据检索
<br/>
<br/>
<img src="/public/search.svg" style="width:100%;height:20%" class="h-8">

---
layout: center

class: text-center
---

# 3、Prompt Engineer
一些写好prompt的方法

---

# 问答问题场景

1、To do and Not To do

我们在跟ChatGPT对话的时候，可以用不可以做什么来限定ChatGPT的输出

当我们的反例太多的时候，我们可以ChatGPT可以输出的方向

| 场景 | 不好的示例 | 好的示例 |
| ------  | ------ | ------ |
| 推荐必备英文单词 | Please suggest me some essential words for IELTS | Please suggest me 10 essential words for IELTS |



---

# 基于示例回答


1、在某些场景下，我们能比较简单地向 AI 描述出什么能做，什么不能做。但有些场景，有些需求很难通过文字指令传递给 AI，即使描述出来了，AI 也不能很好地理解。


| 场景 | 不好的示例 | 好的示例 |
| ------  | ------ | ------ |
| 将电影名称转为 emoji | Convert Star Wars into emoji | Convert movie titles into emoji.<br />Back to the Future: 👨👴🚗🕒<br />Batman: 🤵🦇<br />Transformers: 🚗🤖<br />Star Wars: |

---

# 推理

1、在大语言模型学习的过程中, 出现了推理功能，我们之前问一个比较复杂的问题，ChatGPT可能回答不出来，但是如果你让ChatGPT分步骤推理，ChatGPT就能解答出来，现在这个能力还没有具体的解释，推测可能是ChatGPT在学习程序员编写代码时候，学会了分解任务，分步分析的能力

```text
如果一个房地产经纪人的佣金是某个房子的售价的6％，那么这个房子的售价是多少？
（1）售价减去房地产经纪人的佣金为84,600美元。
（2）购买价是36,000美元，售价是购买价的250%。

（A）仅陈述（1）足以回答问题，但仅陈述（2）不能回答问题。
（B）仅陈述（2）足以回答问题，但仅陈述（1）不能回答问题。
（C）两个陈述合起来足以回答问题，但没有一个陈述单独足以回答问题。
（D）每个陈述单独足以回答问题。
（E）陈述（1）和（2）合起来不能回答问题。
```
---
layout: center
class: text-center pb-5
--- 

### 编程和写 Prompt 有本质的区别么？如果你把 ChatGPT 看作一个编译器或者解释器，其实也没有多大差别。只是编程更为精细，这是更直接和计算机对话的原始方式。而 Prompt 几乎就是自然语言，你可以通过特定的 Prompt 完成特定领域的任务。现在我们常用的编程语言对于未来而言可能是一种汇编语言。

---
layout: center
class: text-center pb-5
---

# THANKS!