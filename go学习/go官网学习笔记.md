> [官方文档](https://golang.google.cn/doc/)



go特性：

- the convenience of garbage collection 

- the power of run-time reflection

- It's a fast, statically typed, compiled language that feels like a dynamically typed, interpreted language.



## 安装

mac系统，使用.pkg安装包安装go：[安装](https://golang.google.cn/doc/install)

- 安装完成后，go的安装位置：/usr/local/go
- 安装完成后，go的环境变量位置：/etc/paths.d/go

- 卸载go时，删除上述两个文件。[mac系统卸载go](https://golang.google.cn/doc/manage-install#uninstalling)



---



## Tutorial: Get started with Go

[教程地址](https://golang.google.cn/doc/tutorial/getting-started)

```
生成.mod文件
go mod init example.com/hello

mod file stays with your code
```



---



## Tutorial: Create a Go module

[教程地址](https://golang.google.cn/doc/tutorial/create-module)

Go代码被分组到包（packages）中，包被分组到模块（modules）中。

在Go中，名称以***大写字母***开头的函数可以被不在同一包（package）中的函数调用。



```
将没有发布的包（example.com/greetings）替换为本地路径（../greetings）
go mod edit -replace=example.com/greetings=../greetings
```

