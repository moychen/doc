# Go语言中的面向对象

标签（空格分隔）： Golang

---
## Golang面向对象
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
>* nil指针也可以调用方法
>* 无论是值接收者和指针接收者，它们调用方法的方式都是一样的

**演示程序**
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

```
## Golang面向接口
###接口的定义和使用
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
而定义mock.Retriever结构中的Get方法时，采用的值接收者，在使用时，既可以采用值方式使用指针方式使用，如下所示
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
###接口的组合
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
###常用系统接口
##总结
我们总结一下前面看到的：Go没有类，而是松耦合的类型、方法对接口的实现。
OO 语言最重要的三个方面分别是：封装，继承和多态，在 Go 中它们是怎样表现的呢？

**封装（数据隐藏）**：和别的 OO 语言有 4 个或更多的访问层次相比，Go 把它简化为了 2 层（参见 4.2 节的可见性规则）:
>* 包范围内的：通过标识符首字母小写，对象 只在它所在的包内可见
>* 可导出的：通过标识符首字母大写，对象 对所在包以外也可见

类型只拥有自己所在包中定义的方法。

**继承**：用组合实现：内嵌一个（或多个）包含想要的行为（字段和方法）的类型；多重继承可以通过内嵌多个类型实现

**多态**：用接口实现：某个类型的实例可以赋给它所实现的任意接口类型的变量。类型和接口是松耦合的，并且多重继承可以通过实现多个接口实现。Go接口不是java和C#接口的变体，而且接口间是不相关的，并且是大规模编程和可适应的演进型设计的关键。
