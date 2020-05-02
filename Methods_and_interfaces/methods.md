# 方法和接口

## 正文

go 没有类，但是能定义结构的成员函数：

~~~go
package main

import (
	"fmt"
	"math"
)

type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(v.Abs())
}
~~~

同时不想C++那样有头文件的概念，必须在一个文件内部定义和实现这个结构体的全部成员方法。

这种感觉就是像是一个结构体会有一个成员方法表，每当出现`func (t Type) Foo()`这种定义函数的时候直接在Type的方法表内部插入这个`Foo`函数。

go里面也特别的区别了*值* 和*指针*， 如果`v.Foo()`那么不会改变`v`本身内部的值，而是对一个拷贝的值操作，但是定义是：`func (t *Type) Foo()`的时候，`v.FOO`的操作不是对拷贝的v操作而是`v`本身。

### 指针

~~~go
var v Vertex
v.Scale(5)  // OK
p := &v
p.Scale(10) // OK
~~~

这里我们看到了一个非常混淆的用法，在c++的写法中，我们会明确的感知到代码中值变量和指针变量：

~~~cpp
Vertex v;
v.Scale(5)  // OK
Vertex* p = &v
p->Scale(10) // OK
~~~

看到了吗？`->`和`.`。go语言可能为了简洁是把`->`去掉了，但是我觉得这并不是一个很好的设计，因为代码阅读的时候你会一眼看出来谁是值变量，谁是指针变量。

这里go举了例子:

~~~go
//        当方法是外部方法的时候，
//    结构体的指针和值变量在代码语法上是不一致的
var v Vertex
fmt.Println(AbsFunc(v))  // OK
fmt.Println(AbsFunc(&v)) // 编译错误！

//        当方法是成员变量的时候，
//    结构体的指针和值变量在代码语法上是一致的。
var v Vertex
fmt.Println(v.Abs()) // OK
p := &v
fmt.Println(p.Abs()) // OK
~~~

也就是说，内质为成员方法是更加方便的。当然，调试起来有的时候忘记了为啥变量改变就很麻烦了。

**不要为了方便而滥加成员变量，记住职责单一。**

这里我想知道，go语言是如何做`const &`的因为有很多时候为了避免开销而是用引用，但是又想要避免值得改变。

## 链接

| 目录 | next |
| --   |  --  |
| [TOC](../TOC.md) | [接口](./interfaces.md) |
