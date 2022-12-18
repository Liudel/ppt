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

# Slice基础

<div class="grid grid-cols-2 gap-x-4">

<div class="mt-8 col-span-1">
```go {all|2|5|7,8,9,10|12,13,14,15}
// 初始化slices
s := make([]byte, 0, 5)

// 截取其中一段
s := s[2:4]

// 下面这两行代码有什么问题吗
var s []byte
append(s, 'a')
fmt.Println(s)

// 那么这段代码呢
var m map[int]int
m[1] = 1
fmt.Println(m)

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
    <v-click at="5">
      <img  v-click-hide="6"  style="position:absolute; width: 40%;top: 23vh"  src="/public/map_noninit_panic.png">
    </v-click>
  </div>
</div>

---

# Slice内存泄漏

<div class="grid grid-cols-2 gap-x-10">
<div class="col-span-1">
切片会一直引用的底层的数据，这块内存不会被释放
```go {all|7,8,9,10,11,12|all}
func printAlloc() {
	var m runtime.MemStats
	runtime.ReadMemStats(&m)
	fmt.Printf("%d MB\n", m.Alloc/(1024*1024))
}

// slicing
func getHead() []byte {
	var msg [100000000]byte
	printAlloc()
	return msg[:5]
}

func main() {
	printAlloc()       // 0 MB
	var head []byte
	head = getHead()   // 95 MB
	runtime.GC()
	printAlloc()       // 95 MB
	_ = head 
}
```
</div>

<div v-click="3" class="col-span-1">
copy函数可以帮助我们改善这种情况
```go
var dst []int
src := []int{1, 2, 3}

copy(dst, src)
fmt.Println(dst)
```
<div v-click=4>
我们必须给目标切片分配内存才能复制内容
```go
src := []int{1, 2, 3}
var dst = make([]int, len(src))

copy(dst, src)
fmt.Println(dst) // [1 2 3]
```
</div>
<div v-click=5>
我们改写一下之前的函数
```go
func getCopyHead() []byte {
	var msg [100000000]byte
	printAlloc()
	head := make([]byte, 5)
	copy(head, msg[:5])
	return head
}
```
</div>
</div>

</div>

---
---
# Slice数据
#### 刚刚已经说过了，切片引用同一个底层数据，那么我们修改切片数据的时候也会影响到其他切片.
<div class="grid grid-cols-10 gap-x-10">
<div class="mt-2 col-span-7">

```go
a := []int{1, 2, 3}
b := a[:]
b[1] = 666

fmt.Println("a", a) 
fmt.Println("b", b)
```
 <img v-click style="width:60%;margin-top:3vh" src="/public/slice_data.png">

</div>

</div>

---

# Map初始化
<div class="grid grid-cols-2 gap-x-10">
<div class="col-span-1">
map可以用make函数初始化, 并能减少内存分配次数
```go
const mapSize = 1000
func BenchmarkInitMapCap(b *testing.B) {
	b.Run("without cap", func(b *testing.B) {
		for i:=0;i<b.N;i++ {
		   // 未指定大小
			m := make(map[int]int)
			for k:=0;k<mapSize;k++ {
				m[k]=k
			}
		}
	})

	b.Run("with cap", func(b *testing.B) {
		for i:=0;i<b.N;i++ {
		   // 指定大小
			m := make(map[int]int, mapSize)
			for k:=0;k<mapSize;k++ {
				m[k]=k
			}
		}
	})
}
```
</div>
<div class="col-span-1">
<img v-click style="margin-top:18vh" src="/public/map_init.png" />
</div>
</div>

---

# Map技巧
### 1、go语言官方并没有给我默认类型，我们可以用map实现set，并且节省内存
```go
//  struct {} 是一种普通数据类型，一个无元素的结构体类型，通常在没有信息存储时使用。
// 优点是大小为0，不需要内存来存储struct {}类型的值。
var set = make(map[int]struct{})  
```
<div class="grid grid-cols-2 gap-x-10">
<div class="col-span-1">

### 2、不要对map的遍历循序做任何假设
```go
// 基于go语言实现map的方式，每次便利map的输出结果都是不确定
// 在循环中在循环中的新增、删除结果也是不确定的
m := map[int]bool{
	1: false,2: true,3: true,4: false,5: true,
}

for k, v := range m {
  if v {
    m[k+10] = true
  }
}
fmt.Printf("map:%+v\n", m)
// map:map[1:false 2:true 3:true 4:false 5:true 12:true 13:true 15:true 22:true 23:true 25:true]
// map:map[1:false 2:true 3:true 4:false 5:true 12:true 13:true 15:true]
// map:map[1:false 2:true 3:true 4:false 5:true 12:true 13:true 15:true 23:true 25:true 33:true]
```

</div>
<div v-click class="col-span-1">

### 3、使用已有顺序的数组或者切片遍历
```go
s := []int{1, 2, 3, 4, 5}
m := map[int]bool{
	1: false,2: true,3: true,4: false,5: true,
}

for _, i := range s {
  fmt.Println(m[i])
}
```
</div>
</div>

---

# Map技巧
### 4、map可以并发读,不能并发写和并发读写,想要支持并发可以使用锁或sync.Map
<div class="grid grid-cols-2 gap-x-10">
<div class="col-span-1">
```go
m := make(map[string]int)

go func() {
  for {
      // write
    m["a"] = 1
    time.Sleep(time.Microsecond)
  }
}()

go func() {
  for {
      // read
    _ = m["b"]
    time.Sleep(time.Microsecond)
  }
}()

select {}
```
</div>
<div class="col-span-1">
<img v-click style="margin-top:18vh" src="/public/map_wr.png" />
</div>
</div>

---
layout: center
class: text-center pb-5
---

# THANKS!
