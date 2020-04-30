# 变量

## 正文

~~~go
package main

import "fmt"

var c, python, java bool

func main() {
	var i int
	fmt.Println(i, c, python, java)
}
~~~

终于看到变量声明了，需要`var`关键字，和`func`的机理一致。但是可能这种实现在语法分析的时候BNF里面变量和函数的推到式没有交集，更好看点。

~~~go
var c, python,
 java bool
~~~

这种也是编译过的，估计是匹配了`var`和`type`，中间全部是这个类型。

### 初始化

~~~go
var i, j int = 1, 2
~~~

我没想到这个`=`会在后面。

### 隐式声明

go 的隐式声明变量只能是临时变量，如下：

~~~go
package main

import "fmt"

func main() {
	var i, j int = 1, 2
	k := 3
	c, python, java := true, false, "no!"

	fmt.Println(i, j, k, c, python, java)
}

// 运行结果：
$ 1 2 3 true false no!
~~~

我很好奇为啥这么设计，这个隐式声明是右值推导左值类型。但是这个时候会给这个k分配啥呢？

- 是根据大小自动分配
- 还是默认`int32`+动态分配大小

### 基础类型

~~~go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128
~~~

第一次见到有复数作为基础类型的😀

### 初值

go是默认初值为**0值**，0值不是0。是：

- 0
- false
- 空串
- 等等

这是真的方便，c++默认是没有初始化的，我开始学习的时候不知道多少次栽在这个初始化上。

但是c++还不能全部初始化，因为头文件里面的会出现multi define的情况。

## 连接

| 目录 | next |
| --   |  --  |
| [TOC](./TOC.md) | [变量](./more_types.md) |
