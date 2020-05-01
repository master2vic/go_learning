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

## 连接

| 目录 | next |
| --   |  --  |
| [TOC](./TOC.md) | [变量](../Methods_and_interfaces/methods_and_interfaces.md) |
