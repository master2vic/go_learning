# 方法

没想到，go教程会比变量先教方法。

[语法哲学](https://blog.golang.org/declaration-syntax)

搞计算机的人说实话比我想象的要更考虑哲学，优雅。当然工程除外，可能优先是易维护等……

## 正文

~~~go
package main

import "fmt"

func add(x int, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(42, 13))
}
~~~

好吧，应该首字母大写是指库提供的就那样，而不是说自己写也是要这样才编译过。根据go语法设计哲学，int在后面也就是`func foo(Args Type) Type`的形式，而不是`Type foo(Type Args)`。记住就行。

哦，这个也可以像c++那样折叠类型声明，但是函数里面也可以折叠我是没想到的。上面代码可以写成：

~~~go
func add(x , y int) int {
	return x + y
}
~~~

还可以多参数返回，这比c++自由，但是更自由不代表更好。

~~~go
func swap(x, y string) (string, string) {
	return y, x
}
~~~

这是什么操作，go管他叫naked return。看出来naked了，但是确实会对较长函数带来阅读不便利。但是这个东西在编译上还是很好实现的，只要用返回列表先构建出这个作用域内的变量和其类型，在下面引用的时候查表就有了。

~~~go
package main

import "fmt"

func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

func main() {
	fmt.Println(split(17))
}
~~~

## 连接

| 目录 | next |
| --   |  --  |
| [TOC](./TOC.md) | [变量](./variables.md) |
