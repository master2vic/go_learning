# 练习

## 问题1

### 题目

求根

### 解

~~~go
package main

import (
	"fmt"
)

func square(x float64) float64 {
	return x * x
}

func average(x, y float64) float64 {
	return (x + y) / 2
}

func Sqrt(x float64) float64 {
	good_enough := func(guess float64) bool {
		tmp := square(guess)
		if tmp >= x {
			return (tmp - x) < 0.01
		} else {
			return (x - tmp) < 0.01
		}
	}
	try := func (guess float64) float64 {
		for !good_enough(guess) {
			guess = average(x/guess,guess)
		}
		return guess
	}
	return try(1)
}

func main() {
	fmt.Println(Sqrt(2))
}
~~~

这个就是之前学SICP的时候学到的求根法，找到fix point就行

~~~lisp
(define (square x) (* x x))
(define (average x y) (/ (+ x y) 2))
(define (sqrt x)
    (define (improve guess)
        (average guess (/ x guess)))
    (define (good-enough? guess)
        (< (abs (- (square guess) x)) 
            .00001))
    (define (try guess)
        (if (good-enough? guess)
            guess
            (try (improve guess))))
    (try 1))
~~~

## 问题2

### 题目

实现 Pic。它应当返回一个长度为 dy 的切片，其中每个元素是一个长度为 dx，元素类型为 uint8 的切片。当你运行此程序时，它会将每个整数解释为灰度值（好吧，其实是蓝度值）并显示它所对应的图像

### 解

~~~go
package main

import "golang.org/x/tour/pic"

func Pic(dx, dy int) [][]uint8 {
	pic := make([][]uint8, dx)
	for i := 0 ;i < dy; i++ {
		tmp := make ([]uint8, dy)
		for j := range tmp {
			tmp[j] = uint8((i+j)/2)
		}
		pic[i] = tmp
	}
	return pic
}

func main() {
	pic.Show(Pic)
}
~~~

## 问题3

### 题目

实现 WordCount。它应当返回一个映射，其中包含字符串 s 中每个“单词”的个数。函数 wc.Test 会对此函数执行一系列测试用例，并输出成功还是失败。

### 解

~~~
package main

import (
	"strings"

	"golang.org/x/tour/wc"
)

func WordCount(s string) map[string]int {
	str := strings.Fields(s)
	m := make(map[string]int)
	for i := 0; i < len(str); i++ {
		k := str[i]
		//检查当前key是否存在
		v, ok := m[k]
		if ok {
			m[k] = v + 1
		} else {
			m[k] = 1
		}
	}
	return m
}

func main() {
	wc.Test(WordCount)
}
~~~

## 问题4

### 题目

斐波那契数列

### 解

~~~go
package main

import "fmt"

// 返回一个“返回int的函数”
func fibonacci() func() int {
	a ,b := 0, 1
	return func() int {
		a, b = b , b+a
		return a
	}
}

func main() {
	f := fibonacci()
	for i := 0; i < 10; i++ {
		fmt.Println(f())
	}
}
~~~

函数 Fibonacci 和内部的值 a,b 绑定在一起了。所以每次调用不是从0，1开始

## 问题5

### 题目

通过让 IPAddr 类型实现 fmt.Stringer 来打印点号分隔的地址。

例如，IPAddr{1, 2, 3, 4} 应当打印为 "1.2.3.4"。

### 解

用Sprintf整合字符串

~~~go
package main

import "fmt"

type IPAddr [4]byte

func (ip IPAddr) String() string {
	return fmt.Sprintf("%v.%v.%v.%v", ip[0], ip[1],ip[2],ip[3])
}

func main() {
	hosts := map[string]IPAddr{
		"loopback":  {127, 0, 0, 1},
		"googleDNS": {8, 8, 8, 8},
	}
	for name, ip := range hosts {
		fmt.Printf("%v: %v\n", name, ip)
	}
}
~~~

## 问题6

### 问题

为之前问题1增加报错机制；创建一个新的类型：

~~~go
type ErrNegativeSqrt float64
~~~

并实现：

~~~go
func (e ErrNegativeSqrt) Error() string
~~~

### 解

~~~go
package main

import (
	"fmt"
)

func square(x float64) float64 {
	return x * x
}

func average(x, y float64) float64 {
	return (x + y) / 2
}

type ErrNegativeSqrt float64

func (e ErrNegativeSqrt) Error() string {
    return fmt.Sprintf("cannot Sqrt negative number: %v",float64(e))
}

func Sqrt(x float64) (float64, error) {
	if x < 0 {
		return x, ErrNegativeSqrt(x)
	}
	good_enough := func(guess float64) bool {
		tmp := square(guess)
		if tmp >= x {
			return (tmp - x) < 0.01
		} else {
			return (x - tmp) < 0.01
		}
	}
	try := func (guess float64) float64 {
		for !good_enough(guess) {
			guess = average(x/guess,guess)
		}
		return guess
	}
	return try(1),nil
}

func main() {
	fmt.Println(Sqrt(2))
	fmt.Println(Sqrt(-2))
}
~~~

> 在 `Error` 方法内调用 `fmt.Sprint(e)` 会让程序陷入死循环。可以通过先转换 e 来避免这个问题：`fmt.Sprint(float64(e))`。这是为什么呢？

在我实际的运行中其实是没有发生循环的，但是我猜测发生死循环的原因是：`fmt.Sprint(e)`会发生递归。

## 问题7

### 题目

实现一个 Reader 类型，它产生一个 ASCII 字符 'A' 的无限流。

### 解

~~~go
package main

import "golang.org/x/tour/reader"

type MyReader struct{}

// TODO: 给 MyReader 添加一个 Read([]byte) (int, error) 方法

func (mr MyReader) Read(b []byte) (int, error) {
	b[0] = 'A'
	return 1, nil
}

func main() {
	reader.Validate(MyReader{})
}
~~~

只要最后一个不返回nil就可以了。

## 问题8

### 题目

编写一个实现了 io.Reader 并从另一个 io.Reader 中读取数据的 rot13Reader，通过应用 rot13 代换密码对数据流进行修改。

其中 rot13 加密就是把当前字母替换为13后面的字母。我们都知道字母一共26个，所以其实26进制补码加法的运算就可以。

### 解

~~~go
package main

import (
	"io"
	"os"
	"strings"
)

type rot13Reader struct {
	r io.Reader
}

func rot13(b byte) byte {
	switch {
	case b >= 'A' && b <= 'M' || b >= 'a' && b <= 'm':
		b += 13
	case b >= 'N' && b <= 'Z' || b >= 'n' && b <= 'z':
		b -= 13
	default:
		return b
	}
	return b  //  没有会报错
}

func (rr rot13Reader) Read(b []byte) (int, error) {
	n, e := rr.r.Read(b)
	for i, v := range b {
		b[i] = rot13(v)
	}
	return n, e
}

func main() {
	s := strings.NewReader("Lbh penpxrq gur pbqr!")
	r := rot13Reader{s}
	io.Copy(os.Stdout, &r)
}
~~~

这里我比较好奇的其实是go语言所判断一个函数的return机制

~~~go
func rot13(b byte) byte {
	switch {
	case b >= 'A' && b <= 'M' || b >= 'a' && b <= 'm':
		b += 13
	case b >= 'N' && b <= 'Z' || b >= 'n' && b <= 'z':
		b -= 13
	default:
		return b  // 1  会报错
	}
	if true {
		return b  // 2  会报错
	}
	return b     //  3  没有会报错不论上面有没有
}
~~~

当在上述情况下，不论有没有1，2。 只要没有最后一个裸露的`return b`我觉得其实这是比较好判断的机制吧。为啥没有这种措施呢。

## 问题9

### 题目

定义你自己的 Image 类型，实现必要的方法并调用 pic.ShowImage。

Bounds 应当返回一个 image.Rectangle ，例如 image.Rect(0, 0, w, h)。

ColorModel 应当返回 color.RGBAModel。

At 应当返回一个颜色。上一个图片生成器的值 v 对应于此次的 color.RGBA{v, v, 255, 255}。

### 解

~~~go
package main

import (
    "golang.org/x/tour/pic"
    "image/color"
    "image"
)

type Image struct{}

func (i Image) ColorModel() color.Model{ 
    return color.RGBAModel
}

func (i Image) Bounds() image.Rectangle{
    return image.Rect(0,0,255,255)
}

func (i Image) At(x,y int) color.Color{
    return color.RGBA{uint8(x),uint8(y),uint8(255),uint8(255)}
}

func main() {
    m := Image{}
    pic.ShowImage(m)  //调用
}
~~~

## 连接

| 目录            | next                                                        |
| --------------- | ----------------------------------------------------------- |
| [TOC](./TOC.md) | [变量](../Methods_and_interfaces/methods_and_interfaces.md) |
