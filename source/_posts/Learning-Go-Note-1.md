---
title: Learning Go Note 1
date: 2016-03-31 17:18:08
categories: GO Language
tags: [Go, freshman2coding]
---
![头图](https://zero-space.s3.amazonaws.com/photos/f473f476-8dd6-41fb-9146-25ebbaa8bd91x840.jpg)

>Go语言是谷歌推出的一种全新的编程语言，可以在不损失应用程序性能的情况下降低代码的复杂性。谷歌首席软件工程师罗布派克(Rob Pike)说：我们之所以开发Go，是因为过去10多年间软件开发的难度令人沮丧。

>Go是谷歌2009发布的第二款编程语言。2009年7月份，谷歌曾发布了Simple语言，它是用来开发Android应用的一种BASIC语言.

>Go 同时具有两种编译器，一种是建立在GCC基础上的Gccgo，另外一种是分别针对64位x64和32位x86计算机的一套编译器(6g和8g)。谷歌目前正在研发其对ARM芯片和Android设备的支持。派克表示，“Android手机存在的问题是，我们一直没有一个数学协处理器。”

---
<!-- more -->
几个月前有一阵很闲很闲，就一激灵打算学学Go语言，听说吧[^go]：

[^go]: [《学习Go语言》中文版](http://mikespook.com/learning-go)

有一些东西使得 Go 区别于其他语言。
## 清晰并且简洁
Go 努力保持小并且优美，你可以在短短几行代码里做许多事情；
## 并行
Go 让函数很容易成为非常轻量的线程。这些线程在 Go 中被叫做 goroutines a
## Channel

这些 goroutines 之间的通讯由 channel 完成；
## 快速

编译很快，执行也很快。目标是跟 C 一样快。编译时间用秒计算；
## 安全

当转换一个类型到另一个类型的时候需要显式的转换并遵循严格的规则。Go 有垃圾收集，在 Go 中无须 free()，语言会处理这一切；
## 标准格式化

Go 程序可以被格式化为程序员希望的（几乎）任何形式，但是官方格式是存在的。标准也非常简单：gofmt 的输出就是官方认可的格式；类型后置类型在变量名的后面，像这样 var a int，来代替 C 中的 int a；UTF-8任何地方都是 UTF-8 的，包括字符串以及程序代码。你可以在代码中使用 Φ =Φ + 1；
## 开源

Go 的许可证是完全开源的，参阅 Go 发布的源码中的 LICENSE 文件；
## 开心
用 Go 写程序会非常开心！

反正说着都挺好，这些东西真要是不一直用，两天我就忘干净了...
那就研究研究

惯例我就开始百度[简明/go/教程]之类的关键词，果然就是有

>Go语言在线教程 [A Tour of Go](http://tour.golangtc.com/list)

现在的人实在是太贴心了，这种边看边做的教程真的是友好的不像话。
依然是从hello world开始，
```go
package main
import "fmt"
func main() {
	fmt.Println("Hello, 世界")
}
```


```bash
	C:\Users\Administrator>go
	Go is a tool for managing Go source code.
	Usage:
	 go command [arguments]
	 The commands are:
	 build       compile packages and dependencies
	 clean       remove object files
	 doc         show documentation for package or symbol
	 env         print Go environment information
	 fix         run go tool fix on packages
	 fmt         run gofmt on package sources
	 generate    generate Go files by processing source
	 get         download and install packages and dependencies
	 install     compile and install packages and dependencies
	 list        list packages
	 run         compile and run Go program
	 test        test packages
	 tool        run specified go tool
	 version     print Go version
	 vet         run go tool vet on packages
	 Use "go help [command]" for more information about a command.
```