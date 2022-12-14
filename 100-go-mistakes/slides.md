---
fonts:
  mono: 'LXGWWenKaiGBScreenR'
  serif: 'LXGWWenKaiGBScreenR'
  sans: 'LXGWWenKaiGBScreenR'
  local: 'LXGWWenKaiGBScreenR'
layout: cover

image: https://source.unsplash.com/collection/94734566/1920x1080

---

# go语言细节

go语言使用中常见的错误和最佳实践

<div class="uppercase text-sm tracking-widest">
增长后端 王若愚
</div>

<div class="abs-bl mx-14 my-12 flex">
  <img src="https://golang.google.cn/images/gophers/pilot-bust.svg" class="h-8">
  <div class="ml-3 flex flex-col text-left">
    <div><b>Go</b>Day</div>
    <div class="text-sm opacity-50">Dec. 22th, 2022</div>
  </div>
</div>

 
---
clicks: 3
---

## 下面的代码有什么问题吗？
<div class="grid grid-cols-3 gap-x-4">
<div class="mt-8 col-span-2">
```go {all|1|1,4,9|1,4,9,15}
var client *http.Client

if tracing {
    client, err := createClientWithTracing()
    if err != nil {
      return err 
    }
} else {
    client, err := createDefaultClient()
    if err != nil {
      return err 
    }
}

client.Get("http://127.0.0.1")


```
</div>
</div>

<v-clicks at="1">
<Tips x1="280" y="130" x2="220" color="#393a34" width="1" text="初始化client" />
</v-clicks>

<v-clicks at="2">
<Tips x1="420" y="185" x2="340" color="#393a34" width="1" text="重新初始化client" />
</v-clicks>
<v-clicks at="2">
<Tips x1="410" y="275" x2="330" color="#393a34" width="1" text="重新初始化client" />
</v-clicks>

<v-clicks at="3">
<Tips x1="330" y="382" x2="270" color="#393a34" width="1" text="client为nil, 程序panic" />
</v-clicks>

---
layout: center

class: text-center
---

# 数据结构
常见的误解

---
layout: center
class: text-center pb-5
---

# THANKS!
