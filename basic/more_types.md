# 其他类型

## 正文

### 类型转化

~~~go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
~~~

缩写为：

~~~go
i := 42
f := float64(i)
u := uint(f)
~~~

反正，go只提供显示类型转化。c++是又隐式类型转化的比如各种类型数字都是往`double`的方向（不是转化为double）转化的。这样计组的时候我们学过：uint和int的比较会出问题，其实就是因为这种隐式转化的风险。

### 类型推导

~~~go
package main

import "fmt"

func main() {
	v := 42           // change me!
	f := 3.142        // float64
	g := 0.867 + 0.5i // complex128
	fmt.Printf("v is of type %T\n", v)
	fmt.Printf("f is of type %T\n", f)
	fmt.Printf("g is of type %T\n", g)
}

// 输出
v is of type int
f is of type float64
g is of type complex128
~~~

这就回答了前面我的好奇，果然go是默认数字为int然后再动态分配到，不会因为42小而分配`int8`这种恰好满足的类型。

### 常量（constants）

就是用`const`修饰变量名就行。

不能修饰临时变量。

同`import`也有一个很好看的写法：

~~~go
package main

import "fmt"

const (
	// Create a huge number by shifting a 1 bit left 100 places.
	// In other words, the binary number that is 1 followed by 100 zeroes.
	Big = 1 << 100
	// Shift it right again 99 places, so we end up with 1<<1, or 2.
	Small = Big >> 99
)

func needInt(x int) int { return x*10 + 1 }
func needFloat(x float64) float64 {
	return x * 0.1
}

func main() {
	fmt.Println(needInt(Small))
	fmt.Println(needFloat(Small))
	fmt.Println(needFloat(Big))
}
~~~

Q: 很奇怪，这里显然如果Small的类型确定是在`const`修饰的时候的话，那么显然这里至少发生了一次隐式类型转化：Small传参给函数的时候。go语言是不允许隐式传参的，那么推测这个类型确定时机实在使用的时候。

来实践一下，go语言不确定类型是不能当右值给另一个不确定类型做类型推导的。那么：

~~~go
package main

import "fmt"

const (
    Big = 1 << 100
	Small = Big >> 99
)

func main() {
	a := Samll
	fmt.Println(a)
}
~~~

是可以是运行的.

而`fmt.Println(Big)`，报错：`constant 1267650600228229401496703205376 overflows int`

`fmt.Println(Big*0.1)`，输出：`1.2676506002282295e+29`

这是不是说明，go其实还是有隐式类型转化的，那就会有类型推导安全问题。这个以后再说吧。

## 链接

| 目录 | next |
| --   |  --  |
| [TOC](../TOC.md) | [指针](./pointer.md) |
