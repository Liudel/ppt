---
fonts:
  mono: 'LXGWWenKaiGBScreenR'
  serif: 'LXGWWenKaiGBScreenR'
  sans: 'LXGWWenKaiGBScreenR'
  local: 'LXGWWenKaiGBScreenR'
layout: cover


lineNumbers: true

---

# GO范型

跟着 Go 作者学泛型

<div class="uppercase text-sm tracking-widest">
增长后端 王若愚
</div>

<div class="abs-bl mx-14 my-12 flex">
  <img src="/public/pilot-bust.svg" class="h-8">
  <div class="ml-3 flex flex-col text-left">
    <div><b>Share</b>Day</div>
    <div class="text-sm opacity-50">Jul. 06th 2023</div>
  </div>
</div>



---

# go没有范型的模样

```go
func StrSliceToUintSlice(arr []string) ([]uint64, error) {
	res := make([]uint64, 0, len(arr))
	for i := range arr {
		num, err := strconv.ParseUint(arr[i], 10, 64)
		if err != nil {
			return nil, err
		}
		res = append(res, num)
	}
	return res, nil
}

func StrSliceToIntSlice(arr []string) ([]int64, error) {
	res := make([]int64, 0, len(arr))
	for i := range arr {
		num, err := strconv.ParseInt(arr[i], 10, 64)
		if err != nil {
			return nil, err
		}
		res = append(res, num)
	}
	return res, nil
}
```

---
layout: center

---

# Go1.18 泛型的三个特性
1. Type parameters for functions and types，即函数和类型的类型参数
2. Type sets defined by interfaces，即由接口定义的类型集合
3. Type inference，即类型推断



---
layout: center

---
# 1、函数和类型的类型参数

---

# 1.1、类型参数列表（Type parameter lists）

##### 类型参数列表看起来是带方括号的普通参数列表。通常，类型参数以大写字母开头，以强调它们是类型：
```go
[P, Q constraint1, R constraint2]
```
<div class="grid grid-cols-2 gap-x-4">
<div class="mt-8 col-span-1">

##### 非范型版本的最小值函数
```go
func min(x, y float64) float64 {
  if x < y {
    return x
  }
  return y
}
```
</div>
<div v-click class="mt-8 col-span-1">

##### 范型版本的最小值函数
```go
func min[T Ordered](x, y T) T {
  if x < y {
    return x
  }
  return y
}
```
</div>
</div>
<br>
<div v-click>

##### 那这个泛型函数如何调用呢？

</div>
<div v-click>
```go
m := min[int](2, 3)
```
</div>

---

# 1.2、实例化（Type parameter lists）
## 

在调用时，会进行实例化过程：

1）用类型实参（type arguments）替换类型形参（type parameters）

2）检查类型实参（type arguments）是否实现了类型约束

如果第 2 步失败，实例化（调用）失败。


所以，调用过程可以分解为以下两步：
```go
func min[T Ordered](x, y T) T {
  if x < y {
    return x
  }
  return y
}

fmin := min[float64]
m := fmin(2.3, 3.4)

// 和下面等价
m := min[float64](2.3, 3.4)
// 相当于 m := (min[float64])(2.3, 3.4)
```

---

# 1.3、类型的类型参数
##

类型也可以有类型参数。下面是一个泛型版二叉树：

```go
type Tree[T any] struct {
	left, right *Tree[T]
	data        T
}

func (t *Tree[T]) Lookup(x T) *Tree[T]

var stringTree Tree[string]
```

其中的 [T interface{}] ，跟函数的类型参数语法是一样的，T 相当于是一个类型，

所以，之后用到 Tree 的地方，T 都跟随着，即 Tree[T]，包括方法的接收者（receiver）。

---
layout: center

---
# 2、类型集合

---

# 2.1、类型约束
### 

我们定义接口来约束某一个类型是否实现接口的方法

```go
type IGet interface {
  Get()
}

func Test(ig IGet) {
  ig.Get()
}

```
在范型中我们也可以限制范型的中的类型
```go
type Ordered interface {
  int | float64 | ~string
}

func min[T Order](x, y T) T {
  if x < y {
    return x
  }
  return y
}
```

---

根据 Go 的规则，类型 P、Q、R 方法中包含了 a、b、c，因此它们实现了接口
<img src="gophercon01.png">

---

反过来可以说，接口也定义了类型集（type sets）

类型 P、Q、R 都实现了左边的接口（因为都实现了接口的方法集），因此我们可以说该接口定义了类型集。
<img src="gophercon02.png">

---

既然接口是定义类型集，只不过是间接定义的：类型实现接口的方法集。而类型约束是类型集，因此完全可以重用接口的语义，只不过这次是直接定义类型集：

<img src="gophercon03.png">

---
layout: center

---

# 3、类型推断

---

<div class="grid grid-cols-2 gap-x-4">
<div class="mt-8 col-span-1">
```go
func Scale[E constraints.Integer](s []E, c E) []E {
	r := make([]E, len(s))
	for i, v := range s {
		r[i] = v * c
	}
	return r
}

type Point []int32

func (p Point) String() string {
	return "point"
}

func ScaleAndPrint(p Point) {
	r := Scale(p, 2)
	fmt.Println(r.String())
}

func main() {
	p := Point{3, 2, 4}
	ScaleAndPrint(p)
}
```

</div>
<div class="mt-8 col-span-1">

左边的代码会报错：

r.String undefined (type []int32 has no field or method String)。

<div v-click>
```go
func Scale[S ~[]E, E constraints.Integer](s S, c E) S {
	r := make(S, len(s))
	for i, v := range s {
		r[i] = v * c
	}
	return r
}
```
</div>
</div>
</div>


---
layout: center

---

# 4、一些不足

---

# 4.1、不支持范型方法
## 

主要原因Go泛型的处理是在编译的时候实现的，泛型方法在编译的时候，如果没有上下文的分析推断，很难判断泛型方案该如何实例化，甚至判断不了，导致目前(Go 1.18)Go实现中不支持泛型方案

```go
type StudentModel struct{}
type Client struct{  }
type Querier struct{
  client *Client
}
// Identity 一个泛型方法，支持任意类型.
func (q *Querier) All[T any](ctx context) ([]T, error) { return nil, nil } // method must have no type parameters
```

我们要想实现Client，只能在结构上加范型：
```go
type Querier[T any] struct {
	client *Client
}

func NewQuerier[T any](c *Client) *Querier[T] {
  return &Querier[T]{ 
    client: c
  }
}

func (q *Querier[T]) All(ctx context.Context) ([]T, error) {return nil, nil}
```

--- 

<div class="grid grid-cols-2 gap-x-4">
<div class="mt-8 col-span-1">
```go
package p1
// S 是一个普通的struct,但是包含一个泛型方法Identity.
type S struct{}
// Identity 一个泛型方法，支持任意类型.
func (S) Identity[T any](v T) T { return v }
```

```go
package p2
// HasIdentity 定义了一个接口
type HasIdentity interface {
	Identity[T any](T) T
}
```

```go
package p3
import "p2"
// CheckIdentity 是一个普通函数，
// 检查实参是不是实现了HasIdentity接口，
// 如果是，则调用这个接口的泛型方法Identity.
func CheckIdentity(v interface{}) {
	if vi, ok := v.(p2.HasIdentity); ok {
		if got := vi.Identity[int](0); got != 0 {
			panic(got)
		}
	}
}
```
</div>
<div class="mt-8 col-span-1">

```go
package p4
import (
	"p1"
	"p3"
)
// CheckSIdentity 传参S给CheckIdentity.
func CheckSIdentity() {
	p3.CheckIdentity(p1.S{})
}
```
<br>

##### 一切看起来都没有问题，但是问题是package p3不知道p1.S类型，整个程序中如果也没有其它地方调用p1.S.Identity,依照现在的Go编译器的实现，是没有办法为p1.S.Identity[int]生成对应的代码的。
</div>
</div>

---
layout: center
class: text-center pb-5

---

# THANKS!