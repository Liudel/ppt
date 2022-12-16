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
  <img src="/public/pilot-bust.svg" class="h-8">
  <div class="ml-3 flex flex-col text-left">
    <div><b>Share</b>Day</div>
    <div class="text-sm opacity-50">Dec. 22th, 2022</div>
  </div>
</div>

 
---
clicks: 1
---

## 下面的代码有什么问题吗？
<div class="grid grid-cols-12 gap-x-4">

<div class="mt-8 col-span-5">
```go {all|1,4,9,15}
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
<v-click at="1">

<div class="mt-8 col-span-5">
```go
<-- 1、初始化client


<-- 2、重新初始化client




<-- 2、重新初始化client





<-- 3、client为nil, 程序panic
```
</div>
</v-click>
</div>

---
layout: center

class: text-center
---

# 数据结构
默认我们已经知道的规则

---

# slice

<div class="grid grid-cols-2 gap-x-4">

<div class="mt-8 col-span-1">
```go {all|2|5}
// 初始化slices
s := make([]byte, 0, 5)

// 截取其中一段
s := s[2:4]

```
</div>
  <div class="mt-8 col-span-1">
    <v-click at="0">
      <img v-click-hide="1" style="position:absolute; width: 40%"  src="/public/slice-struct.png">
    </v-click>
    <v-click at="1">
      <img  v-click-hide="2"  style="position:absolute; width: 40%"  src="/public/slice-1.png">
    </v-click>
     <v-click at="2">
      <img  v-click-hide="3"  style="position:absolute; width: 40%"  src="/public/slice-2.png">
    </v-click>
  </div>
</div>

---
layout: center
class: text-center pb-5
---

# THANKS!
