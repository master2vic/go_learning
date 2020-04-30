# 变量

## 正文

### 循环

~~~go
sum := 0
for i := 0; i < 10; i++ {
	sum += i
}
~~~

就是没有`()`包裹。

可以，其中`init`,`condition`,`post statement`都可以缺省

~~~go
sum := 1
for ; ; {
    sum += sum
}
~~~

可以写为：

~~~go
sum := 1
for sum<1000 {
    sum += sum
}

// 或者

for  {
    sum += sum
}
~~~

这就是go没有while的理由？懒得写词法？不给语法糖？😂

### 条件分支

~~~go
if x < 0 {
    x += 1
}
~~~

我差点以为`if`也得用`for`实现呢。

~~~go
if v := math.Pow(x, n); v < lim {
	return v
}
~~~

`if`条件前可以有一个表达式，这是为啥存在的语法糖呢？老老实实写不行？为了让临时变量的作用域更小？只在if内部，然后也可以用这个值来判断？这样会导致寄存器分配啥的更好吗？

### switch

package main

import (
	"fmt"
	"runtime"
)

func main() {
	fmt.Print("Go runs on ")
	switch os := runtime.GOOS; os {
	case "darwin":
		fmt.Println("OS X.")
	case "linux":
		fmt.Println("Linux.")
        fallthrough
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.\n", os)
	}
}

go 自带break是真的舒服，如果不想break，那就显示的使用`fallthrough`

在switch缺省判断式的时候相当于if-elseif-……else

~~~go
package main

import (
	"fmt"
	"time"
)

func main() {
	t := time.Now()
	switch {
	case t.Hour() < 12:
		fmt.Println("Good morning!")
	case t.Hour() < 17:
		fmt.Println("Good afternoon.")
	default:
		fmt.Println("Good evening.")
	}
}
~~~

### 延期

~~~go
package main

import "fmt"

func tmp() {
	defer fmt.Println("2")

	fmt.Println("1")
}

func main() {
	defer fmt.Println("4")
	tmp()
	fmt.Println("3")
}

// 输出
1
2
3
4
~~~

## 连接

| 目录 | next |
| --   |  --  |
| [TOC](./TOC.md) | [变量](../Methods_and_interfaces/methods_and_interfaces.md) |
