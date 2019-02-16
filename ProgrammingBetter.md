# Programming Better

## Scene 1

```go
//返回值创造的大量垃圾
for i := 0; i < 100000; i++ {
	//产生十万个垃圾
    getUser(i).doSomeThing()
}

//复用user，没有多余垃圾
user := &User{}
for i := 0; i < 100000; i++ {
    //没有产生垃圾
    getUser(i, user)
    user.doSomeThing()
}

```

在go语言标准库中，也有类似的例子，如库函数conn.Read(buf)，为什么不设计成 buf := conn.Read()？因为这样会产生垃圾，io越频繁，产生的垃圾越多。