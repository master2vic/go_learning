# 接口

## 看一个例子

~~~go
package main

import (
	"fmt"
	"math"
)

type Abser interface {
	Abs() float64
}

func main() {
	var a Abser
	f := MyFloat(-math.Sqrt2)
	v := Vertex{3, 4}

	a = f  // a MyFloat 实现了 Abser
	a = &v // a *Vertex 实现了 Abser

	// 下面一行，v 是一个 Vertex（而不是 *Vertex）
	// 所以没有实现 Abser。
	a = v  // 报错， 愿意Vertex 实现Abs()得时候没有 "func (v *Vertex) Abs()"

	fmt.Println(a.Abs())
}

type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

type Vertex struct {
	X, Y float64
}


func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

// func (v Vertex) Abs() float64 {
// 	return math.Sqrt(v.X*v.X + v.Y*v.Y)
// }
~~~

这边报错得原因很简单：这个接口函数有

- `MyFloat`值类型的实现
- `Vertex`值类型的实现
- 但是没有`Vertex`类型的实现

我想，这不是加上就可以了吗？

事情没有这么简单，回想函数重载是如何实现的：`同名函数 & （不同参数 | 不同返回类型 ）`

但是这个所属并不在其中：

~~~go
func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
// 报错，因为这不是重载
func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
~~~

## 隐式接口

go的原文：

> 类型通过实现一个接口的所有方法来实现该接口。既然无需专门显式声明，也就没有“implements”关键字。

这不就是因为go没有面向对象的`"class"`关键字，也就没有了class的域如下：

~~~Java
class Type implements interfaces  {
    ...
}
~~~

那么实现这个接口的时机就是用户确定的，不是非要在实现这个类的时候就全部写完，显示实现这个接口。而是谁用谁实现一下。

于是：

> 这样接口的实现可以出现在任何包中

这就是go所认为的解耦。

就像上面例子里面`Abser`接口里面的方法实现完全就没有一个显示的`class`去`implements`。**而是把实现游离在外部**。

这样我有一个问题：**这样会不会太零碎，而不便于找到实现这个实现的位置**。隐式带来的好处一定会在某些地方成为劣势。这种隔离的想法是软件设计模式中所提到的，但是这种设计模式真的那么有助于开发吗？

## 接口值

其实也不是什么稀奇的内容，就是上面例子中的`var a Abser`。

~~~go
func main() {
	var a Abser
	f := MyFloat(-math.Sqrt2)
	v := Vertex{3, 4}

	a = f  // a MyFloat 实现了 Abser
	fmt.Printf("(%v, %T)\n", i, i)
	a = &v // a *Vertex 实现了 Abser
	fmt.Printf("(%v, %T)\n", i, i)
}
// 输出
(-1.4142135623730951, main.MyFloat)
(&{3 4}, *main.Vertex)
~~~

其实还是实例化，有点类似子类和父类的关系。

## nil

~~~go
package main

import "fmt"

type I interface {
	M()
}

type T struct {
	S string
}

func (t *T) M() {
	if t == nil {
		fmt.Println("<nil>")
		return
	}
	fmt.Println(t.S)
}

func main() {
	var i I

	var t *T
	i = t
	describe(i)
	i.M()

	i = &T{"hello"}
	describe(i)
	i.M()
}

func describe(i I) {
	fmt.Printf("(%v, %T)\n", i, i)
}
// 输出
(<nil>, *main.T)
<nil>
(&{hello}, *main.T)
hello
~~~

看到居然"0值"也可以映射出一个接口值, 但是如果是接口值为0值得话就

## 空接口

go 也有一个类似于 Java Object类的一个东西:`interface{}`, 空接口。因为所有类型都至少实现了0个方法。所以我推测要么这个接口才是AST的顶层。

~~~go
package main

import "fmt"

func main() {
	var i interface{}
	describe(i)

	i = 42
	describe(i)
	
	var x int
	describe(x)
}

func describe(i interface{}) {
	fmt.Printf("(%v, %T)\n", i, i)
}
// 输出
(<nil>, <nil>)
(42, int)
(0, int)
~~~

这个例子虽然不能证明`int`的节点派生于这个`interface{}`。但是如果不是从一个AST上构造下来根本没必要这么操作。

## 类型断言

~~~go
package main

import "fmt"

func main() {
	var i interface{} = "hello"

	s := i.(string)
	fmt.Println(s)

	s, ok := i.(string)
	fmt.Println(s, ok)

	f, ok := i.(float64)
	fmt.Println(f, ok)
	
	f, _ = i.(float64) // 不报错(panic)
	fmt.Println(f)
	
	f = i.(float64) // 报错(panic)
	fmt.Println(f)
}
// 输出
hello
hello true
0 false
0
panic: interface conversion: interface {} is string, not float64

goroutine 1 [running]:
main.main()
	/tmp/sandbox699425892/prog.go:20 +0x263
~~~

这就是我所提到的问题，`_`可以防止报panic，但这是掩耳盗铃。但是确实可以硬怼过去。

## 类型选择（工厂模式）

~~~go
package main

import "fmt"

func do(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Printf("Twice %v is %v\n", v, v*2)
	case string:
		fmt.Printf("%q is %v bytes long\n", v, len(v))
	default:
		fmt.Printf("I don't know about type %T!\n", v)
	}
}

func main() {
	do(21)
	do("hello")
	do(true)
}
// 输出
Twice 21 is 42
"hello" is 5 bytes long
I don't know about type bool!
~~~

学过软件设计模式的人相比一定会熟悉的就是三个工程模式。看到这个类型判断是不是很熟悉这种风格呢。
