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
