# 包

## 正文

~~~go
package main

import (
	"fmt"
	"math/rand"
)	

import "math/cmplx"

func main() {
	fmt.Printf("Now you have %g problems.\n", math.Sqrt(7))
}
~~~

1. 这里这个头部有的这个`package main`有点像Java一样，这里的主函数入口`func main()`*暂时没看到类型和返回值*。
2. 但是按照官网`import`支持两种方式一个是小括号，还有就是支持像python那样的一个一个import。感觉用小括号更好看吧。引入规则也是一样的可以引入包下的某部分如：`math/rand`。*暂时不清楚能不能像c++一样指定自己在某个文件夹下的包*。
3. 可以看到这里的输出是类似c的那种输出形式。*不知道有没有c++那种封装好的IO流*。
4. 这里体现了go的设计理念，包的函数名都是首字母大写的。但是看到`func main`看起来不是只要函数不首字母大写就不给编译通过。

~~~go
package main

import (
	"fmt"
	"math"
)

func main() {
    // fmt.Println(math.pi) 错误，要大写
    fmt.Println(math.Pi)
}
~~~

好像成员变量也是这个要求。

## 链接

| 目录 | next |
| --   |  --  |
| [TOC](../TOC.md) | [变量](./variables.md) |