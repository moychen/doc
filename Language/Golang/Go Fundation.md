# Go 基础

## Array

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

## Slice

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



## 面向对象
### 结构体和方法
定义方法时需要了解：
```go
func createNode(value int) *Node {
	return &Node{value : value}
}
```
>* Go 语言中没有构造和析构函数，因此一般都是通过普通函数来作为工厂函数创建结构体
>* 注意该函数返回了局部变量的地址，这在Go语言中是允许的。
>* 此时就需要考虑该局部变量是存在栈上还是堆上？
内存分配的位置是有编译器和运行环境决定的，在本环境中返回局部变量和返回局部变量的地址均可（经过测试）。

定义方法时需要注意：
```go
func (node *Node) SetValue(value int)  {
	if nil == node {
		fmt.Println("Setting value to nil node, ignored!")
		return
	}

	node.value = value
}
```
```go
func (node Node) Print() {
	fmt.Print(node.value, " ")
}
```
>* 要改变结构内容必须使用指针接收者，值接收者是Go语言特有的。
>* 结构过大也要考虑使用指针接收者，避免值接收者在拷贝时的花销过大。
>* 考虑到一致性，如有指针接收者，最好都是指针接收者

使用方法时需要注意：
```go
var pRoot *Node
pRoot.SetValue(2) 
```
>* nil指针也可以调用方法，但是nil指针指向不合法内存。
>* 无论是值接收者和指针接收者，它们调用方法的方式都是一样的

**示例**

```go
package main

import "fmt"

type Node struct {
	value int
	left, right *Node
}

func (node *Node) SetValue(value int)  {
	if nil == node {
		fmt.Println("Setting value to nil node, ignored!")
		return
	}

	node.value = value
}

func createNode(value int) *Node {
	return &Node{value : value}
}

func (node Node) Print() {
	fmt.Print(node.value, " ")
}

func (node *Node) traverse() {
	if node == nil {
		return
	}

	if node.left != nil {
		node.left.traverse()
	}
	node.Print()
	if node.right != nil {
		node.right.traverse()
	}
}

func main() {
	var root Node

	root = Node{value: 3}
	root.left = &Node{}
	root.right = &Node{5, nil, nil}
	root.right.left = new(Node)
	root.left.right = createNode(2)
	root.right.left.SetValue(4)

	var pRoot *Node
	pRoot.SetValue(2)

	root.traverse()
}
```

### 封装
>* 包名一般采用CamelCase，首字母大写连在一起
>* 首字母大写：public
>* 首字母小写：private，public和private都是相对于包来讲的
>* 在Go语言中，每个目录为一个包，包名可以和目录名不一样，main包包含了程序执行入口（main函数）
>* 为结构定义的所有方法，必须放在同一个包内，可以是不同的文件

**扩展已有的类型**
1.通过组合的方式
```go
type myTreeNode struct {
	node *tree.Node
}

func (myNode *myTreeNode) postOrder() {
	if myNode == nil || myNode.node == nil {
		return
	}

	left := myTreeNode{myNode.node.Left}
	right := myTreeNode{myNode.node.Right}

	left.postOrder()
	right.postOrder()
	myNode.node.Print()
}
```
2.通过别名的方式
```go
package queue
// 定义了新类型Queue，该类型具有几种方法
type Queue []int
// 因为需要改参数，所以传地址
func (q *Queue) Push(v int) {
    *q = append(*q, v)
}
func (q *Queue) Pop() int {
    head := (*q)[0] // 注意加括号
    *q = (*q)[1:]
    return head
}
func (q *Queue) IsEmpty() bool{
    return len(*q) == 0
}
```
## interface
### 接口的定义和使用

>* 接口的实现是隐式的,只需要结构实现了接口的方法，我们就说实现了这个接口。

在main包中我们定义了一个Retriever接口，里面包含一个Get方法。在mock包中我们定义一个Retriever结构体，实现了Get方法，我们就当做mock.Retriever实现了Retriever接口。
```go
package main
...

type Retriever interface {
	Get(url string) string
}

...
```
```go
package mock

type Retriever struct {
	Contents string
}

func (r Retriever) Get(url string) string {
	return r.Contents
}
```
>* 接口变量自带指针，指的是接口的实现时，接收者为指针类型
>* 接口变量也是值传递，几乎不需要使用接口的指针
>* 指针接收者实现只能以指针方式使用；值接收者既可以使用指针方式，也可以使用值方式。

在real包中我们定义Retriever结构实现了接口Retriever，实现了Get方法，并将其接收者定义为指针。
```go
package real

import (
	"time"
	"net/http"
	"net/http/httputil"
)

type Retriever struct {
	UserAgent string
	TimeOut   time.Duration
}

func (r *Retriever) Get(url string) string {
	resp, err := http.Get(url)
	if err != nil {
		panic(err)
	}

	bytes, err := httputil.DumpResponse(resp, true)
	resp.Body.Close()
	if err != nil {
		panic(err)
	}

	return string(bytes)
}
```
在定义real.Retriever结构中的方法Get时，采用的指针接收者，因此在使用时只能以指针方式使用，如下所示
```go
func download(r Retriever) string {
	return r.Get("http://www.imooc.com")
}
```
```go
var r Retriever
r = &real.Retriever{
	UserAgent: "Mozilla/5.0",
	TimeOut: time.Minute,
}

fmt.Printf("%T, %v\n", r, r)
fmt.Println(download(r))
```
而定义mock.Retriever结构中的Get方法时，采用的值接收者，在使用时，既可以采用值方式使用也可以使用指针方式使用，如下所示：
**值传递方式**

```go
var r Retriever 
r = mock.Retriever{"This is a mock retriever!"}

fmt.Printf("%T, %v\n", r, r)//mock.Retriever, &{This is a mock retriever!}
fmt.Println(download(r))
```
**指针传递方式**
```go
var r Retriever
r = &mock.Retriever{"This is a mock retriever!"}

fmt.Printf("%T, %v\n", r, r)//*mock.Retriever, &{This is a mock retriever!}
fmt.Println(download(r))
```
>* Go语言中的任意类型： interface{}
>* Type Assertion
>* Type Switch

可以通过 type Assertion 和 Type Switch 来判断接口变量表示的实体类型

**Type Assertion**
```go
//Type assertion
if realRetriever, ok := r.(*real.Retriever); ok {
	fmt.Println(realRetriever.UserAgent)
} else {
	fmt.Println("not a real retriever!")
}
```
**Type Switch**
```go
func inspect(r Retriever) {
	switch v := r.(type) {
	case mock.Retriever:
		fmt.Println("contents", v.Contents)
	case *real.Retriever:
		fmt.Println("UserAgent: ", v.UserAgent)
	}
}
```
### 接口的组合
一个接口可以包含一个或多个其他的接口，这相当于直接将这些内嵌接口的方法列举在外层接口中一样。
比如接口 File 包含了 ReadWrite 和 Lock 的所有方法，它还额外有一个 Close() 方法。

```go
type ReadWrite interface {
    Read(b Buffer) bool
    Write(b Buffer) bool
}

type Lock interface {
    Lock()
    Unlock()
}

type File interface {
    ReadWrite
    Lock
    Close()
}
```

## OO总结

我们总结一下前面看到的：Go没有类，而是松耦合的类型、方法对接口的实现。
OO 语言最重要的三个方面分别是：封装，继承和多态，在 Go 中它们是怎样表现的呢？

**封装（数据隐藏）**：和别的 OO 语言有 4 个或更多的访问层次相比:
>* 包范围内的：通过标识符首字母小写，对象 只在它所在的包内可见
>* 可导出的：通过标识符首字母大写，对象 对所在包以外也可见

类型只拥有自己所在包中定义的方法。

**继承**：用组合实现：内嵌一个（或多个）包含想要的行为（字段和方法）的类型；多重继承可以通过内嵌多个类型实现

**多态**：用接口实现：某个类型的实例可以赋给它所实现的任意接口类型的变量。类型和接口是松耦合的，并且多重继承可以通过实现多个接口实现。Go接口不是java和C#接口的变体，而且接口间是不相关的，并且是大规模编程和可适应的演进型设计的关键。

## 第三方库

### maxmind/geoip2

