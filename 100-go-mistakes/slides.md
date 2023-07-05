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

# 1、数据结构
默认我们已经知道的规则

---
clicks: 6
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

# Map结构

<div class="grid grid-cols-2 gap-x-10">
<div class="col-span-1">
```go
// A header for a Go map.
type hmap struct {
    // 元素个数，调用 len(map) 时，直接返回此值
	count     int
	flags     uint8
	// buckets 的对数 log_2
	B         uint8
	// overflow 的 bucket 近似数
	noverflow uint16
	// 计算 key 的哈希的时候会传入哈希函数
	hash0     uint32
    // 指向 buckets 数组，大小为 2^B
    // 如果元素个数为0，就为 nil
	buckets    unsafe.Pointer
	// 扩容的时候，buckets 长度会是 oldbuckets 的两倍
	oldbuckets unsafe.Pointer
	// 指示扩容进度，小于此地址的 buckets 迁移完成
	nevacuate  uintptr
	extra *mapextra // optional fields
}
```
</div>
<div class="col-span-1">
<img style="width:100%" src="/public/map_struct.png" />
</div>
</div>

---

# bmap结构

<div class="grid grid-cols-2 gap-x-10">
<div class="col-span-1">
我们在go源码中可以看到bmap结构：
```go
type bmap struct {
	tophash [bucketCnt]uint8
}
```
但是在编译期间，编译器会改变这个结构：
```go
type bmap struct {
    topbits  [8]uint8
    keys     [8]keytype
    values   [8]valuetype
    pad      uintptr
    overflow uintptr
}
```
</div>
<div class="col-span-1">
<img style="height:90%" src="/public/bmap_struct.png" />
</div>
</div>

---

### map中定位key
<div class="grid grid-cols-2 gap-x-10">

<div class="col-span-1">
<img style="height:90%" src="/public/map_find_key.png" />
</div>
<div class="mt-10 col-span-1">

1、首先对key进行hash

2、获取hash后的值的后B位确定bucket

3、根据tophash确定key在桶中的位置

4、如果没有找到并且overflow指针为空，就继续寻找直到找到key, 或者找完为止

</div>
</div>

---

# Map容量调整
##
1、扩容时容量翻倍，申请一个新的数组，采用渐进式哈希的方式进行迁移 

2、迁移过程中支持读写：
- 新增kv只写新表
- 修改和删除双写，保证新老表中的数据一致
- 读取时优先读老表，再读新表
  
3、迁移过程为每次update/remove操作时触发部分搬迁，每次搬迁2部分bucket中的数据：
- 修改的key所在的当前Bucket
- 按照顺序搬迁的一个Bucket（避免某些bucket一直未被访问导致无法搬迁成功）

4、直到所有数据搬迁完成后，删除oldBuckets，使得老哈希表中的Bucket对象被GC回收

---
clicks: 1
---

# map扩容的条件
- 装载因子超过阈值，源码里定义的阈值是 6.5
  - `loadFactor := count / (2^B)`
- overflow 的 bucket 数量过多：当 B 小于 15，也就是 bucket 总数 2^B 小于 2^15 时，如果 overflow 的 bucket 数量超过 2^B；当 B >= 15，也就是 bucket 总数 2^B 大于等于 2^15，如果 overflow 的 bucket 数量超过 2^15。
 <v-click at="0">
  <img v-click-hide="1" style="position:absolute;width:60%;left:25vh;top:35vh"  src="/public/map_grow.png">
</v-click>
<v-click at="1">
  <img  v-click-hide="2"  style="position:absolute;width:50%;left:25vh;top:37vh"  src="/public/map_qy.png">
</v-click>

<!--
第 1 点：我们知道，每个 bucket 有 8 个空位，在没有溢出，且所有的桶都装满了的情况下，装载因子算出来的结果是 8。因此当装载因子超过 6.5 时，表明很多 bucket 都快要装满了，查找效率和插入效率都变低了。在这个时候进行扩容是有必要的。

第 2 点：是对第 1 点的补充。就是说在装载因子比较小的情况下，这时候 map 的查找和插入效率也很低，而第 1 点识别不出来这种情况。表面现象就是计算装载因子的分子比较小，即 map 里元素总数少，但是 bucket 数量多（真实分配的 bucket 数量多，包括大量的 overflow bucket）。

不难想像造成这种情况的原因：不停地插入、删除元素。先插入很多元素，导致创建了很多 bucket，但是装载因子达不到第 1 点的临界值，未触发扩容来缓解这种情况。之后，删除元素降低元素总数量，再插入很多元素，导致创建很多的 overflow bucket，但就是不会触犯第 1 点的规定，你能拿我怎么办？overflow bucket 数量太多，导致 key 会很分散，查找插入效率低得吓人，因此出台第 2 点规定。这就像是一座空城，房子很多，但是住户很少，都分散了，找起人来很困难。



对于条件 1，元素太多，而 bucket 数量太少，很简单：将 B 加 1，bucket 最大数量（2^B）直接变成原来 bucket 数量的 2 倍。于是，就有新老 bucket 了。注意，这时候元素都在老 bucket 里，还没迁移到新的 bucket 来。而且，新 bucket 只是最大数量变为原来最大数量（2^B）的 2 倍（2^B * 2）。

对于条件 2，其实元素没那么多，但是 overflow bucket 数特别多，说明很多 bucket 都没装满。解决办法就是开辟一个新 bucket 空间，将老 bucket 中的元素移动到新 bucket，使得同一个 bucket 中的 key 排列地更紧密。这样，原来，在 overflow bucket 中的 key 可以移动到 bucket 中来。结果是节省空间，提高 bucket 利用率，map 的查找和插入效率自然就会提升。
-->

---
layout: center

class: text-center
---

# 2、控制流

---

# for循环

### go range语法糖
<div class="grid grid-cols-2 gap-x-10">
<div class="col-span-1">
```go
a := []int{-1}
for i := range a {
  a = append(a, i)
}
fmt.Println(a)
```
</div>
<div class="col-span-1">
```go
a := []int{-1}
for i := 0; i < len(a); i++ {
  a = append(a, i)
}
fmt.Println(a)
```
</div>
<div v-click class="col-span-1 mt-5">
1、输出[-1, 0]
</div>
<div v-click class="col-span-1 mt-5">
2、程序死循环
</div>
<div v-click class="col-span-1 mt-5">
```go
arr := [2]int{1, 2}
res := []*int{}
for _, v := range arr {
  res = append(res, &v)
}

fmt.Println(res)
```
</div>
<div v-click="5" class="col-span-1 mt-5">
```go
// The loop we generate:
//   len_temp := len(range)
//   range_temp := range
//   for index_temp = 0; index_temp < len_temp; index_temp++ {
//           value_temp = range_temp[index_temp]
//           index = index_temp
//           value = value_temp
//           original body
//   }
```
</div>
<div v-click="4" class="col-span-1">
3、输出两个一样的地址
</div>

</div>

---
layout: center

class: text-center
---

# 3、函数

---

# 灵活对函数进行类型转换

<div class="grid grid-cols-2 gap-x-10">
<div class="col-span-1">
1、我现在有这样一个函数
```go
func MyAdd(x, y int) int {
	return x + y
}
```

2、现在我们需要实现这个接口
```go
type BinaryAdder interface {
	Add(int, int) int
}
```
<div v-click>
3、我们当然不去写一个Add函数调用MyAdd函数
```go
type BinaryAdder interface {
	Add(int, int) int
}

type BinaryAdderFunc func(int, int) int

func (f BinaryAdderFunc) Add(x, y int) int {
	return f(x, y)
}
```
</div>
</div>
<div v-click=2 class="col-span-1">
4、你会问这样有什么不一样的吗
```go
// net/http/server.go 
type Handler interface {
	ServeHTTP(ResponseWriter, *Request)
}

// 魔法！
type HandlerFunc func(ResponseWriter, *Request)

// ServeHTTP calls f(w, r).
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
	f(w, r)
}
```
<div v-click=3>
5、我们实现http就是用这种方式实现的
```go
func greeting(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Welcome, Gopher!\n")
}

func main() {
  http.ListenAndServe(":8080", http.HandlerFunc(greeting))
}
```
</div>
</div>
</div>

---
layout: center

class: text-center
---

# 4、结构体


---

# 内嵌结构体

```go
type StructB struct {
    A // A is another struct
}
```

内嵌结构体是Go提供一种介于继承和组合之间的折中方案。

外层的结构体完全复制了里层的结构体的方法。

缺点:

**内嵌结构体最大的特点以及缺点就是暴露了被潜入结构体的方法，哪怕这些方法并不想被外面的调用者使用。**
```go
type SMap struct {
  sync.Mutex // 内嵌Mutex
  data map[string]string
}
func (m *SMap) Get(k string) string {
  m.Lock()
  defer m.Unlock()

  return m.data[k]
}
```

---

# method的本质
##

`method`就是以`receiver`作为第一个参数的函数。没错，method就是函数。
<div class="grid grid-cols-2 gap-x-10">
<div class="col-span-1">
```go
type Person struct {
	Name string
}

// value method
func (p Person) GetName() string {
	return p.Name
}

// pointer receiver
func (p *Person) SetName(name string) {
	p.Name = name
}
```
```go
func package_Person_GetName(p Person) string{
  return p.Name
}

func package_Person_SetName(p *Person) {
  p.Name = name
}
```
</div>
<div v-click class="col-span-1">
```go
type customer struct {
	balance float64
}


func (c customer) add(operation float64) {
	c.balance += operation
}

func main() {
	c := customer{
		balance: 100,
	}
	c.add(50.)
	fmt.Printf("balance: %.2f\n", c.balance)
}
```
</div>

</div>

---
layout: center
class: text-center pb-5
---

# THANKS!
