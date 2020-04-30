# 指针

## 正文

~~~go
package main

import "fmt"

func tmp() *int{
	v := 1
	return &v
}

func main() {
	i, j := 42, 2701

	p := &i         // point to i
	fmt.Println(*p) // read i through the pointer
	*p = 21         // set i through the pointer
	fmt.Println(i)  // see the new value of i

	p = &j         // point to j
	*p = *p / 37   // divide j through the pointer
	fmt.Println(j) // see the new value of j
	fmt.Println(tmp() == tmp(),*tmp())
}

//输出
42
21
73
false 1
~~~

这里和c++很相似，都有取址。但是`p++`这样的指针运算是非法的。

**最让我费解的是返回临时变量的地址，每次不相同，但是都是对的。这边有点想不明白go的内存模型了**
