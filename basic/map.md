# 映射

## 正文

对一个结构体进行映射，即结构体是其值，这里定义键的类型为string。

~~~go
package main

import "fmt"

type Vertex struct {
	Lat, Long float64
}

var m = map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},
}

func main() {
	fmt.Println(m)
}
// 输出
map[Bell Labs:{40.68433 -74.39967} Google:{37.42202 -122.08408}]
~~~

可以缩写为：

~~~go
var m = map[string]Vertex{
	"Bell Labs": {40.68433, -74.39967},
	"Google":    {37.42202, -122.08408},
}
~~~

就是可以把结构体名省略。

## 操作

~~~go
package main

import "fmt"

func main() {
	m := make(map[string]int)

	m["Answer"] = 42
	fmt.Println("The value:", m["Answer"])

	m["Answer"] = 48
	fmt.Println("The value:", m["Answer"])

	delete(m, "Answer")
	fmt.Println("The value:", m["Answer"])

	v, ok := m["Answer"]
	fmt.Println("The value:", v, "Present?", ok)
}
~~~

map有增删查改操作。

- 对于一个键没有其值，相当于插入。
- 对于一个键若有其值，相当于修改。
- 访问键的时候，可带一个bool变量来判断是否存在

这个是很重要的，不论什么时候都要知道一个东西是否可访问，然后在读写，不然容易出现问题。当然map写是随意的，所以没有问题。**但是go语言可缺省判断操作的便利性一定会被不良程序员滥用导致有错误风险。**

## 链接

| 目录 | next |
| --   |  --  |
| [TOC](../TOC.md) | [方法](../Methods_and_interfaces/methods.md) |
