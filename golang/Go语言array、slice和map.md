# Go语言array、slice和map

标签（空格分隔）： Golang

---

##数组Array
>* [10]int和[20]int是不同类型
>* 调用func f(arr [10]int)传参时会对数组进行拷贝
>* go语言中一般不直接使用数组
```go
package main

import "fmt"

func PrintArray(arr [5]int) {
	arr[0] = 1000
	for _, v := range arr {
		fmt.Println(v)
	}
}

func PrintArray2(arr *[5]int) {
	arr[0] = 1000
	for _, v := range arr {
		fmt.Println(v)
	}
}

func main() {
	var arr1 [5]int
	arr2 := [3]int{1, 3, 5}
	arr3 := [...]int{2, 4, 6, 8, 10}
	var grid [4][5]int

	fmt.Println(arr1, arr2, arr3)
	fmt.Println(grid)

	//for i:=0; i<len(arr3); i++ {
	//	fmt.Println(arr3[i])
	//}

	//for _, v := range arr3 {
	//	fmt.Println(v)
	//}

	for i := range arr3 {
		fmt.Println(arr3[i])
	}

	PrintArray(arr3)  //1000 4 6 8 10 函数中处理的是数组的拷贝
	fmt.Println(arr3) //2 4 6 8 10

	PrintArray2(&arr3) //1000 4 6 8 10 通过数组指针来修改数组内容
	fmt.Println(arr3)  //1000 4 6 8 10
}
```

## 切片Slice
**slice扩展**
```Go
arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7}
s1 := arr[2:6]
s2 := s1[3:5]
```
s1 和 s2 的值分别为多少？
&emsp;&emsp;在此之前，我们需要了解一下slice的底层实现，slice中包含ptr、len和cap，ptr指向该slice在底层数组中的起始元素，len表示当前slice的长度，cap表示在底层数组中从ptr指向的元素开始，向后至最后一个元素的长度。

>* s1的值为[2 3 4 5],s2的值为[5 6]
>* slice可以向后扩展，不可以向前扩展
>* s[i]访问时，i的值要小于len(s)，向后扩展不能超越底层数组cap(s)，否则访问越界

```go
package main

import "fmt"

func updateSlice(slice []int) {
	slice[0] = 100
}

func main() {
	arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7}

	fmt.Println("arr[2:6] = ", arr[2:6]) //左闭右开 arr[2]-arr[5]
	fmt.Println("arr[:6] = ", arr[:6])   //0-6     arr[0]-arr[5]
	fmt.Println("arr[2:] = ", arr[2:])   //2-8     arr[2]-arr[7]
	fmt.Println("arr[:] = ", arr[:])     //0-8     arr[0]-arr[7]

	fmt.Println("-----------------After updateSlice s1-----------------")
	s1 := arr[2:]
	fmt.Println(s1) //[2 3 4 5 6 7]
	updateSlice(s1)
	fmt.Println(s1, arr) //[100 3 4 5 6 7] [0 1 100 3 4 5 6 7]

	fmt.Println("-----------------After updateSlice s2-----------------")
	s2 := arr[:5]
	fmt.Println(s2) //[0 1 100 3 4]
	updateSlice(s2)
	fmt.Println(s2, arr) //[100 1 100 3 4] [100 1 100 3 4 5 6 7]

	fmt.Println("-----------------Reslice s2-----------------")
	s2 = s2[:4]
	fmt.Println(s2) //[100 1 100 3]
	s2 = s2[2:]
	fmt.Println(s2) //[100 3]

	fmt.Println("-----------------Extending slice----------------")
	arr[0], arr[2] = 0, 2
	s1 = arr[2:6]
	s2 = s1[3:5]
	//fmt.Println(s1[4])  //index out of range
	fmt.Printf("s1=%v, len(s1)=%d, cap(s1)=%d   ", s1, len(s1), cap(s1)) //s1=[2 3 4 5], len(s1)=4, cap(s1)=6
	fmt.Printf("s2=%v, len(s2)=%d, cap(s2)=%d\n", s2, len(s2), cap(s2))  //s2=[5 6], len(s2)=2, cap(s2)=3

	fmt.Println("------------------slice append-----------------")
	s3 := append(s2, 10)
	s4 := append(s3, 11)
	s5 := append(s4, 12)
	fmt.Println("s3, s4, s5 = ", s3, s4, s5)
	//s4 and s5 no longer view arr.
	fmt.Println("before modify s4, arr = ", arr) //0 1 2 3 4 5 6 10
	s4[0] = 100
	fmt.Printf("s3=%v, len(s3)=%d, cap(s3)=%d   ", s3, len(s3), cap(s3)) // s3=[5 6 10], len(s3)=3, cap(s3)=3
	fmt.Printf("s4=%v, len(s4)=%d, cap(s4)=%d   ", s4, len(s4), cap(s4)) // s4=[100 6 10 11], len(s4)=4, cap(s4)=6
	fmt.Printf("s5=%v, len(s5)=%d, cap(s5)=%d\n", s5, len(s5), cap(s5))  // s5=[100 6 10 11 12], len(s5)=5, cap(s5)=6
	fmt.Println("behind modify s4，arr = ", arr) //0 1 2 3 4 5 6 10
}
```

**向slice添加元素**
&emsp;&emsp;当我们使用append向slice中添加元素时，
>* 添加元素时，如果超过slice的cap，系统会重新分配更大的底层数组新的数组大小一般为原slice的2倍（经过简单测试的结果，未看源码）
>* 由于Go语言中函数传参方式为值传递的关系，必须接受append的返回值。在append的内部不会修改slice参数。
>* append函数的使用如下所示， s = append(s, val) 
```go
package main

import (
	"fmt"
)

func printSlice(s []int) {
	fmt.Println("s=", s, "len=", len(s), "cap=", cap(s))
}

func main() {
	fmt.Println("---------------------Creating slice-------------------------")
	var s []int   // Zero value for slice is nil
	printSlice(s) //s= [] len= 0 cap= 0

	for i := 0; i < 100; i++ {
		printSlice(s)
		s = append(s, 2*i+1)
	}
	printSlice(s) //s= [1 3 5 ... 199] len= 100 cap= 128

	fmt.Println()
	s1 := []int{2, 4, 6, 8}
	printSlice(s1) //s= [2 4 6 8] len= 4 cap= 4

	s2 := make([]int, 16) //s= [0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0] len= 16 cap= 16
	printSlice(s2)

	s3 := make([]int, 16, 32) //s= [0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0] len= 16 cap= 32
	printSlice(s3)

	fmt.Println("------------------------------Copying slice----------------------")
	copy(s2, s1)
	printSlice(s2)

	fmt.Println("-------------------Deleting elements from slice------------------")
	s2 = append(s2[:3], s2[4:]...) //删除s2[3]，append的第二个参数为可变长参数，以s2[4:]...表示用该slice的所有元素做可变长参数
	printSlice(s2)

	fmt.Println("-------------------Popping from front--------------------")
	front := s2[0]
	s2 = s2[1:]
	fmt.Println(front)
	printSlice(s2)

	fmt.Println("-------------------Popping fron back---------------------")
	tail := s2[len(s2)-1]
	s2 = s2[:len(s2)-1]
	fmt.Println(tail)
	printSlice(s2)
}
```
**注意点**
>* slice传参是值拷贝的形式，而且是浅拷贝（能够修改底层数组）；
>* slice做函数参数传递，可以修改底层数组，但是不能够原slice；
>* 在使用slice的过程中，一定要注意slice和底层数组区分开来，这样才不容易出错，举例如下。

```go
package main

import (
	"fmt"
)

func TestSLice(myslice []int) {
	fmt.Println(myslice, len(myslice), cap(myslice))//[] 0 8
	for i := 0; i < 8; i++ {
		myslice = append(myslice, i)
	}

	fmt.Println(myslice, len(myslice), cap(myslice))//[0 1 2 3 4 5 6 7] 8 8
}

func test(s []int) {
	fmt.Println(s, len(s), cap(s))//[0 0 0] 3 3
	s = append(s, 100)
	fmt.Println(s, len(s), cap(s))[0 0 0 100] 4 6
}

func main() {
	myslice1 := make([]int, 8, 8)

	fmt.Println(myslice1, "hello")//0 0 0 0 0 0 0 0] hello

	s := myslice1[:0]
	fmt.Println(s, len(s), cap(s))//[] 0 8
	TestSLice(s)

	fmt.Println(myslice1)//[0 1 2 3 4 5 6 7] //底层数组已被修改
	fmt.Println(s, len(s), cap(s))//[] 0 8 //传入的引用未被修改

	fmt.Println()
	s1 := make([]int, 3, 3)
	test(s1)
	fmt.Println(s1)//[0 0 0]
}
```

## Map
**map的操作**
>* 创建：make(map[string]int)
>* 获取元素：m[key]
>* key不存在时，获取到的是Value类型的初始值
>* 用 value, ok:=m[key]来判断是否存在key
>* 用delete(m, key)删除一个key

**map的遍历**

>* 使用range遍历key，或者遍历key-value键值对
>* map是无序容器，如需要排序的话，需要手动对map里面的数据遍历然后进行排序。
>* 使用len获得元素个数

**map的key**
>* map使用哈希表，必须可以比较相等
>* 除了slice、map、function的内建类型都可以作为key
>* struct类型不包含上述字段，也可以作为key

