# 数组与切片

~~~go
package main

import "fmt"

func main() {
	var a [2]string
	a[0] = "Hello"
	a[1] = "World"
	fmt.Println(a[0], a[1])
	fmt.Println(a)

	primes := [6]int{2, 3, 5, 7, 11, 13}
	x := primes[1:4]
	y := primes[2:5]
	y[0] = 1000
	fmt.Println(primes)
	fmt.Println(x)
	fmt.Println(y)
}
//输出
Hello World
[Hello World]
[2 3 1000 7 11 13]
[3 1000 7]
[1000 7 11]
~~~

切片操作很像python，左开右闭。但是切片还和原数组保持公用数据，相当于这段地址的别名。

~~~go
s := []struct {
		i int
		b bool
	}{
		{2, true},
		{3, false},
		{5, true},
		{7, true},
		{11, false},
		{13, true},
	}
~~~

go可以给数组用一种非常奇怪的初始化

~~~go
s := []int{2, 3, 5, 7, 11, 13}
s = s[1:4]  // [3 5 7]
s = s[:2]   // [3 5]
~~~

此时数组s变了，也可以像python一样有缺省

### 长度与容量

~~~go
package main

import "fmt"

func main() {
	s := []int{2, 3, 5, 7, 11, 13}
	printSlice(s)

	// Slice the slice to give it zero length.
	s = s[:0]
	printSlice(s)

	// Extend its length.
	s = s[:4]
	printSlice(s)

	// Drop its first two values.
	s = s[2:]
	printSlice(s)
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}
//输出
len=6 cap=6 [2 3 5 7 11 13]
len=0 cap=6 []
len=4 cap=6 [2 3 5 7]
len=2 cap=4 [5 7]
~~~

可以看到，只有在切掉前面的时候容量减少，长度减少不影响原来存在的值。

切片和数组中length只决定能访问到的长度，而容量则表示内存中有的真实情况。

### 构建切片

~~~go
package main

import "fmt"

func main() {
	a := make([]int, 5)
	printSlice("a", a)

	b := make([]int, 0, 5)
	printSlice("b", b)

	c := b[:2]
	printSlice("c", c)

	d := c[2:5]
	printSlice("d", d)
}

func printSlice(s string, x []int) {
	fmt.Printf("%s len=%d cap=%d %v\n",
		s, len(x), cap(x), x)
}
// 输出
a len=5 cap=5 [0 0 0 0 0]
b len=0 cap=5 []
c len=2 cap=5 [0 0]
d len=3 cap=3 [0 0 0]
~~~

用make函数构建可以指定len和cap关键字。

### 切片的数据类型

切片可以包含任何数据类型

![pic](./../img/slice%20of%20slice.jpg)

吓得我以为可以闭包，但是显然没有。

官方的意思：

~~~go
board := [][]string{
	[]string{"_", "_", "_"},
	[]string{"_", "_", "_"},
	[]string{"_", "_", "_"},
}
~~~

我的理解是像lisp那样car和cdr是不同值：

~~~lisp
(cons
	[1 2 3 4]
	[1.0 2.0 3.0 4.0])
~~~

### 遍历

~~~go
package main

import "fmt"

var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}

func main() {
	for i, v := range pow {
		fmt.Printf("2**%d = %d\n", i, v)
	}
}
// 输出
2**0 = 1
2**1 = 2
2**2 = 4
2**3 = 8
2**4 = 16
2**5 = 32
2**6 = 64
2**7 = 128
~~~

这里，range关键字对切片操作的返回值为其下标和对应的值，加上我们前面学习的`_`关键字可以当作不用那么也可以写成：

~~~go
for _, v := range pow {
	fmt.Println(v)
}
for i, _ := range pow {
	fmt.Println(i)
}
for i := range pow {
	fmt.Println(i)
}
~~~

## 相关阅读

[切片的本质](https://blog.go-zh.org/go-slices-usage-and-internals)

## 链接

| 目录 | next |
| --   |  --  |
| [TOC](../TOC.md) | [映射](./map.md) |
