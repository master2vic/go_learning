# 变量

## 问题

求根

## 解

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

## 连接

| 目录 | next |
| --   |  --  |
| [TOC](./TOC.md) | [变量](../Methods_and_interfaces/methods_and_interfaces.md) |
