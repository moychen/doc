# Programming Better

## 1. 编写高质量代码

### 1.1 C/C++

以下内容部分来自：

```
<<编写高质量代码：改善C++程序的150个建议>>
```

#### 1.1.1 不要让main函数返回void

#### 1.1.2 

### Golang

## 2. 合理的设计

### 2.1 算法相关

#### 2.1.1 算法复杂度

##### 2.1.1.1 主定理



## 建议

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

