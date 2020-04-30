# 结构体

~~~go
package main

import "fmt"

type Vertex struct {
	X int
	Y int
}

func main() {
	v := Vertex{1, 2}
	p := &v
	p.X = 1e9
	fmt.Println(v)
}
//输出
{1000000000 2}
~~~

go语言居然可以直接输出一个结构体，而且结构体指针访问的时候还是用`.`而不是`->`

~~~go
package main

import "fmt"

type Vertex struct {
	X, Y int
}

var (
	v1 = Vertex{1, 2}  // has type Vertex
	v2 = Vertex{X: 1}  // Y:0 is implicit
	v3 = Vertex{}      // X:0 and Y:0
	p  = &Vertex{1, 2} // has type *Vertex
)

func main() {
	fmt.Println(v1, v2, v3, p)
}
~~~

各种初始化其中`Vertex{X: 1}`是找到结构体中的那个变量进行赋值
